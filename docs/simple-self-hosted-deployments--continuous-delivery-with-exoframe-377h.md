# 使用 Exoframe 实现简单的自托管部署和持续交付

> 原文：<https://dev.to/yamalight/simple-self-hosted-deployments--continuous-delivery-with-exoframe-377h>

有很多服务可以让你把项目的部署简化到一个命令，例如 [Now.sh](https://zeit.co/now) 、 [Heroku](https://www.heroku.com/) 、 [surge](https://surge.sh/) 等。它们中的大多数都非常令人惊奇，并且提供了许多简洁的功能。不幸的是，几乎所有这些都有一个缺点——你不能在自己的服务器上使用它们。我想大多数时候这不是什么大问题。但是有时候你有一个项目或者一个设置，你需要使用你自己的服务器。
这就是为什么我构建了[exo frame](https://github.com/exoframejs/exoframe)——一个自托管工具，允许使用 Docker 进行简单的单命令部署。

## exo frame 能做什么？

以下是 Exoframe 提供的基本功能列表:

*   单一命令项目部署
*   基于 SSH 密钥的认证
*   滚动更新
*   部署令牌(例如，从配置项部署)
*   通过 letsencrypt *实现自动 HTTPS 设置
*   自动 gzip 压缩*
*   限速支持*
*   简单访问部署日志
*   坞站-合成支持
*   多个部署端点和多用户支持
*   客户端、服务器和 Traefik 的简单更新程序
*   可选的自动子域分配(即每个部署都有自己的子域)
*   群体模式部署
*   复杂配方支持(即在一个命令中部署复杂系统)

* trae fik 提供的功能

## 它是如何工作的？

Exoframe 的工作方式非常简单——你唯一需要安装的就是一个带有 Docker 守护进程的服务器。

Exoframe 服务器本身以一种非常简单的方式工作——在执行时，它将产生一个 [Traefik](https://traefik.io/) 的实例，该实例将处理域管理、HTTPS、负载平衡和所有这些事情。
然后它简单地从 Exoframe CLI 获得打包的项目，并在为您配置 Traefik 和 Docker 时将它们部署到您的 Docker 守护进程。
认证是使用私有-公共 RSA 密钥完成的，所以如果您已经可以使用您的私有密钥 SSH 到您的服务器，通常您不需要任何额外的设置。如果你还在使用 SSH 的登录和密码，请看[这个指南](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2)，它将帮助你切换到更安全的密钥认证。

## 简单三步入门

下面的指南假设您已经有了一个可以通过 SSH 访问的服务器，并且已经在上面安装了 Docker 。

### 第一步:安装 Exoframe 服务器

你需要做的就是在你的 Docker 守护进程中执行 Exoframe Server，就像这样:

```
docker run -d \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /path/to/exoframe-folder:/root/.exoframe \
  -v /home/user/.ssh/authorized_keys:/root/.ssh/authorized_keys:ro \
  -e EXO_PRIVATE_KEY=your_private_key \
  --label traefik.backend=exoframe-server \
  --label traefik.frontend.rule=Host:exoframe.your-host.com \
  --restart always \
  --name exoframe-server \
  exoframe/server 
```

*确保将`/home/user`和`/path/to/exoframe-folder`更改为您的本地路径。

该命令将启动 Exoframe 服务器，允许它通过`docker.sock`访问您的 Docker，将 config 文件夹链接到您的本地文件夹以在重启/更新/等时保存配置，链接您的`authorized_keys`文件以进行身份验证，并最终为 Traefik 设置规则。你可以在[服务器文档](https://github.com/exoframejs/exoframe/blob/master/docs/ServerInstallation.md)中找到每一行更详细的解释。

### 第二步:安装 Exoframe CLI

一旦 Exoframe 服务器启动，您将需要安装 Exoframe CLI 来与之通信。安装 CLI 有两种选择。

1.  如果您的系统上已经安装了 Node.js 和 npm，您可以通过 npm: `npm install exoframe -g`安装它
2.  如果你没有 Node.js 并且不想安装它，你可以从 GitHub 上的 releases 下载[一个预构建的二进制文件。](https://github.com/exoframejs/exoframe/releases)

### 步骤 3:将 CLI 连接到您的服务器

设置完成后，您需要使用以下命令将 CLI 指向您的服务器:

```
$ exoframe endpoint https://exoframe.server.url 
```

最后，使用`exoframe login`登录，选择已被授权访问您的服务器的私钥，并在需要时输入密码。

就这样，你可以开始部署了！

## 使用外框架部署

要部署项目，只需从项目文件夹中执行`exoframe`。
部署的配置是使用配置文件完成的，该文件包含您的项目名称、您要分配给它的域、您要设置的环境、重启策略和一系列其他选项。配置文件的结构包含在[外框架文件](https://github.com/exoframejs/exoframe/blob/master/docs/Basics.md#project-config-file)中。
您也可以使用`exoframe config`命令交互生成或编辑当前项目的配置文件。

deploy 命令使用项目模板来自动生成 docker 文件(如果没有的话)。Exoframe 目前捆绑了以下项目类型:

*   基于静态 HTML 的项目——将使用 [nginx](http://hub.docker.com/_/nginx) 映像进行部署
*   基于 Node.js 的项目-将使用[节点](https://hub.docker.com/_/node)映像进行部署
*   基于 Docker 的项目-将使用您的 docker 文件进行部署
*   docker——基于组合的项目——将使用您的 docker——组合文件进行部署

另外，你可以安装和使用 [maven](https://github.com/exoframejs/exoframe-template-maven) 、 [java](https://github.com/exoframejs/exoframe-template-java) 和 [tomcat](https://github.com/exoframejs/exoframe-template-tomcat) 模板。

您也可以按照本指南中的[要求创建自己的模板。](https://github.com/exoframejs/exoframe/blob/master/docs/TemplatesGuide.md)

现在让我们看看 Exoframe 为您的部署提供的一些额外的小功能。

### HTTPS 与 Letsencrypt

因为 Exoframe 使用 Traefik 来管理域，所以您可以通过 letsencrypt 证书获得对 HTTPS 的现成支持！
要启用它，您需要编辑您的服务器配置文件并设置以下字段:

```
letsencrypt: true # whether to enable letsencrypt, default "false"
letsencryptEmail: your@email.com # email used for letsencrypt 
```

一旦设置完成，只需启动您的 Exoframe 服务器。

**警告:**如果您已经运行了 Exoframe 服务器和 Traefik，您还需要从 Docker 守护进程中删除旧的 Traefik 实例，并重新启动 Exoframe 服务器。这是必需的，因为 Traefik 需要用附加的 letsencrypt 参数重新创建。

一旦完成，你所有的项目将自动部署到 HTTPS，并带有 letsencrypt 证书！

### 自动生成子域

Exoframe 还允许您启用自动域名生成。这可以通过将服务器中的`baseDomain`字段设置为您想要使用的顶级域来完成。
例如，将它设置为`.codezen.net`将导致部署的服务没有明确指定的域名，获得像`exo-user-demo-d234ah3.codezen.net`
这样的域名。当您想要快速部署和测试您的项目而不使用真实的域名时，这很有用。

### 用 Exoframe 连续部署

Exoframe 有一个称为“部署令牌”的特殊特性，它允许您将项目部署到您的服务器，而无需使用您的私钥登录。要设置这个部署过程，您首先需要生成这样的令牌。这可以通过执行以下命令来完成:

```
$ exoframe token 
```

在执行时，您应该得到一个新生成的令牌，它可以用来将项目部署到当前的 Exoframe 服务器。

**重要提示:**确保保存令牌，因为无法再次读取，只能生成新的令牌。

一旦有了令牌，您就可以在 CI 服务中使用它，通过在部署期间传递它来使用 Exoframe 部署您的项目，就像这样:

```
$ exoframe -u -t $YOUR_EXOFRAME_TOKEN 
```

你可以在这里找到关于[设置 Travis-CI 的更多详细指南。](https://hackernoon.com/continuous-deployment-for-your-node-js-projects-in-10-minutes-with-exoframe-bdf48340c1be)

### 部署食谱

docker 允许使用 docker-compose 文件部署复杂的系统，但是这种部署受到 Docker 约束的限制——您不能等待服务变得可用，不能执行 post-init 脚本，可能很难准备配置，等等。
当然有些事情是有解决办法的- [其中一些](https://docs.docker.com/compose/startup-order/)在官方 Docker 文档中提到，其他的[在 github 问题](https://github.com/docker/compose/issues/1809)中讨论。

Exoframe 旨在通过“食谱”功能解决其中一些问题。它提供了一种运行第三方复杂部署方案(本质上是直接访问 Docker 守护进程的 javascript 代码)的方式。这些方法提供了一种在一个命令中快速简单地部署复杂项目的方法。

食谱工作方式非常简单:

1.  开发人员[编写了一个外框架配方](https://github.com/exoframejs/exoframe/blob/master/docs/RecipesGuide.md),并将其发布在 npm 上
2.  用户在服务器和本地安装和配置 Exoframe
3.  用户使用外部框架设置配方名称执行配方，其中配方名称是来自 npm 的包名称
4.  Exoframe 从 npm 中提取配方，并向用户呈现一组开发人员定义的问题(见下面的截图)
5.  一旦用户回答了问题，Exoframe 服务器就执行配方代码，并以所需的方式设置系统

作为一个示例，您可以通过简单地执行`exoframe setup exoframe-recipe-mysql`和回答一组简单的问题来部署由 MariaDB 支持的 Wordpress 和 PHPMyAdmin。
或者你可以通过简单地执行`exoframe setup exoframe-recipe-ghost`来部署你自己的 Ghost 博客。
如果你对更复杂的食谱例子感兴趣——看看[这个](https://github.com/hobbit-project/exoframe-recipe-hobbit)大数据基准测试平台——[霍比特人项目](http://project-hobbit.eu/)。很整洁，不是吗？😁

## 电流限制

目前有两个与 Docker 的外部框架交互相关的限制:

1.  对 docker-compose 项目配置的有限支持。虽然 Exoframe 可以很容易地操作它直接部署的容器，但是改变 docker-compose 服务的配置是非常有限的。因此，您必须手动设置域的 Traefik 标签。不幸的是，除了提供更好的文档，没有真正的方法来“修复”这个问题。
2.  有限的群体支持。虽然当前版本的 Exoframe 可以在 swarm 模式下工作，但是如果您尝试从源文件部署项目(即构建映像)，那么部署到集群中将会失败。这是因为 Exoframe 没有任何跨群节点同步构建映像的方法。不过，有一个计划最终会解决这个缺点。

## 结论

如果你觉得 Exoframe 有趣- [试试吧](https://github.com/exoframejs/exoframe)！
和往常一样——感谢任何反馈和贡献😄