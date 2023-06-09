# 从 Heroku 迁徙到 Dokku

> 原文：<https://dev.to/coreyja/migrating-from-heroku-to-dokku-4m4m>

## Heroku

这一年来，我接待了许多不同的提供商，但我总是会回到 Heroku。他们让托管变得如此简单，部署总是只需轻轻一推！有了他们的 Github 集成，我甚至不再手动推送到 Heroku，合并到主自动部署。

尽管如此，Heroku 还是有一个很大的缺点。成本。我想为我所有的副业 HTTPS，我也想为他们大多数使用自定义域。这立刻把我踢出了自由层。我的大部分副业项目都需要一名工人和一个网络流程，所以在 Heroku 上，每个项目每月最低 14 美元。当我开始这个迁移时，我在 Heroku 上有 2 个副业项目，并且还想开始托管一个 [Octobox](https://octobox.io/) 实例。这将使我在 Heroku 每月花费 32 美元。

## 寻找杜库

有一天，当我在寻找一个平台来帮助自己托管更容易时，我遇到了 [Dokku](https://github.com/dokku/dokku) ！它标榜自己是一款`Docker powered mini-Heroku`手机，这听起来正是我想要的。我在 Docker 方面的经验很少，但是我喜欢容器的概念，并且想深入其中。这个想法是，在得到 Dokku 设置后，我将能够复制我自己的小 Heroku 环境。

## 服务器

我决定选择数字海洋。起初，我想把我的 package tracker 侧项目移过来，并设置 Octobox。所以我买了一个 2GB 的 vCPU 盒子，每月 10 美元。我在想这应该和 4 个 Heroku dynos 差不多。一个 web 和一个 worker，分别用于我想要主持的两个项目。

我毫无困难地用数字海洋一键式安装程序安装了 Dokku，并在我新创建的 droplet 中指出了一个子域。Dokku 的第一步设置是通过一个 web 安装程序。这可以通过访问 Droplet 的 ip 或域并遵循向导来访问。它将让您为 Dokku 用户添加一个 SSH 密钥，以及一些默认的 Virtualhost 设置。向导完成后，Dokku box 本身不再响应 web 请求，它没有 web GUI。

因为没有贵由通过 SSH 与 Dokku 盒子交互。而您可以作为 root 用户 SSH 到机器中，并在本地运行`dokku`命令。建议以 dokku 用户的身份 SSH。因为这个用户被限制只能运行`dokku`命令，所以您必须使用远程命令来发送您想要传递给`dokku`命令的 cli 选项。例如，要检查 Dokku 的版本，您可以运行类似
的命令

```
 ssh -t dokku@DOKKU_HOST version 
```

## 设置 Rails 应用程序

这大多遵循[正式文件](http://dokku.viewdocs.io/dokku/deployment/application-deployment/#deploying-to-dokku)

第一步是创建我们想要部署的第一个应用程序

```
 ssh -t dokku@DOKKU_HOST apps:create APP_NAME 
```

接下来是为我的数据库添加 Postgres 插件，并将其链接到应用程序。要添加 Postgres，您需要以 sudo 的身份运行，所以让 SSH 以 root 的身份进入机器。

```
ssh root@DOKKU_HOST
sudo dokku plugin:install https://github.com/dokku/dokku-postgres.git
exit 
```

现在我们需要创建数据库并将其链接到我们的应用程序。应用程序和数据库名称不需要唯一。因为我计划只运行一个应用程序的单个 env，所以我决定将我的数据库和应用程序命名为相同的，这样更容易跟踪。

```
ssh -t dokku@DOKKU_HOST postgres:create APP_NAME
ssh -t dokku@DOKKU_HOST postgres:link APP_NAME APP_NAME 
```

### 迁移现有数据库

如果这是一个全新的应用程序，我们可以跳过这一步。但这个应用程序在 Heroku 上有一个现有的数据库，我们需要迁移过去。

Heroku 使用下面的命令
很容易导出我们数据库的备份并下载它

```
heroku pg:backups:capture
heroku pg:backups:download 
```

Dokku postgres 插件也提供了一个简单的方法来导入这个备份。我们必须跨越的唯一障碍是将我们的数据库备份上传到我们的 Dokku 服务器，这样我们就可以导入它。这是 SCP
的工作

```
scp database.dump root@DOKKU_HOST:/tmp
ssh -t dokku@DOKKU_HOST postgres:import APP_NAME < /tmp/database.dump 
```

### 环境变量

从 Heroku 复制 ENV 变量非常简单。

```
 heroku config 
```

这将输出现有 Heroku 应用程序的环境变量。然后我简单地使用下面的命令
将它们添加到 Dokku 中

```
 ssh -t dokku@DOKKU_HOST config:set APP_NAME KEY1=VALUE1 KEY2=VALUE2 
```

您不想复制下来的一个环境变量是`DATABASE_URL`。这是我们的 Heroku 数据库的 URL，当我们之前链接我们的新 Dokku 数据库和应用程序时，我们已经设置了一个`DATABASE_URL` ENV 变量，它将对您的应用程序可用。

### 域

默认情况下，如果您在初始 web GUI 设置期间设置了主机名，您的应用程序将在 DOKKU_HOST 的子域中可用。该子域将成为您的应用程序名称。因此，对于主机名为`dokku.somecoolsite.com`和应用名为`AwesomeApp`的应用来说，应用的 url 应该是`awesomeapp.dokku.somecoolsite.com`

你可以添加自定义网址。只需设置域名的 DNS 指向你的 Dokku 主机的 IP 地址。然后运行类似下面的命令

```
 ssh -t dokku@DOKKU_HOST domains:add APP_NAME CUSTOM_DOMAIN 
```

如果我们愿意，现在可以清除默认设置。我们可以使用`domains:remove`命令
来实现

```
 ssh -t dokku@DOKKU_HOST domains:remove APP_NAME APP_NAME.DOKKU_HOST 
```

### Dockerfile

package tracker side 项目是一个 Rails 应用程序，我发现了这篇关于在 Dokku 上设置 Rails 应用程序的伟大文章，使用 Docker [“用 Dockerfile 优化 Ruby on Rails 的 Dokku 部署速度”](https://pawelurbanek.com/optimize-dokku-deployment-speed)。本文更侧重于从基于 Dokku buildpack 的构建过渡到基于 Dockerfile 的构建。我想直接使用基于 Dockerfile 的部署，但是我发现这篇文章是一个很好的资源。

在那篇文章中，我得到了一个`Dockerfile` all 设置，并向`app.json`添加了部署前和部署后任务。

现在我们离部署我们的应用程序只有一步之遥了！

```
 git remote add dokku dokku@DOKKU_HOST
git push dokku master 
```

这将类似于 Heroku 部署，因为我们将在 git 推送中看到部署输出。在这里，你可以看到它建立你的 done 文件，当它完成后，你的应用程序将可用！

### 缩放流程

与 Heroku 中的 dynos 类似，如果应用程序需要进行横向扩展，我们可以旋转多个不同类型的流程。这是一个附带的项目应用程序，几乎没有流量，所以我们只需要一个网站和一个工人进程。

```
ssh -t dokku@DOKKU_HOST ps:scale APP_NAME web=1 worker=1 
```

## Dokku 的局限性

Dokku 的一个很大的局限性是所有的东西都运行在一台服务器上。如果你的应用需要扩展到一个以上的服务器，Dokku 就不是合适的工具。

## 接下来是什么？

虽然我的应用程序目前都不需要能够横向扩展到多台服务器所提供的规模，但我仍然不喜欢这成为我的设置中的一个限制因素。我正在研究 Kubernetes 来管理我的个人项目和托管应用的“舰队”,我想这将是我的下一步。