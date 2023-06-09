# 使用无人机 CI 构建和部署 Jekyll 站点

> 原文：<https://dev.to/adelowo/building-and-deploying-a-jekyll-site-with-drone-ci-2eib>

> 本文原载于 [https://lanre.wtf](https://lanre.wtf)

我已经用 Jekyll、Github pages 和 Cloudflare 托管我的博客一段时间了。对我的 Github repo(ade lowo . Github . io)`===`的简单推送，我的 Jekyll 站点的
部署。他们会自动构建和部署它。对我来说，没有基础设施开销。

虽然这很好，但出于几个原因，我想完全控制服务器和软件。

*   自托管(几乎)总是被认为更好。
*   我想要一个真正的 TLS 证书。虽然这些免费证书应该归功于 Cloudflare，但它们的基本工作方式是“从浏览器或客户端到 Cloudflare 的连接是加密的，然后 cloud flare 将请求代理到上游服务器 ***未加密的*** ”。我非常相信安全性，除了静态站点(它没有什么有趣的功能)之外，我会把任何东西放在 TLS 连接的后面。但是现在这种情况已经改变了，我甚至把静态网站放在 TLS 连接的后面。[特洛伊·亨特的博客文章](https://www.troyhunt.com/heres-why-your-static-website-needs-https/)
*   Devops:在我工作的地方，除了编写软件之外，我还做了大量的系统管理工作，建立内部和外部服务——顺便说一下，这是非常棒的工作。我不会把我所做的称为 Devops(现代 Devops？？？).所有面向内部和外部的 API 都是用 Golang 编写的，只需要一个`scp`加上一些重启逻辑(虽然是自动的)。我们使用现代服务(例如，我们严重依赖[领事](https://lanre.wtf/blog/2018/02/18/managing-production-configuration/))但是我们的部署风格仍然是 pre Docker (pre Kubernetes？？？).个人成长的下一步行动是提高这些管理技能，并将它们转化为更现代的技能，Devops。>我们不是 Docker for production 的粉丝。不过，我们确实利用它来进行开发。>虽然，有一个尚未启动的项目，我们可以从 ***将所有事情都归档*** 并使用像 Kubernetes 这样的管弦乐队中受益。

### ci 无人机

Drone 是一个用 Golang 编写的持续集成(s/integration/delivery)服务器。但最有趣的是它的 Docker 用法。所有构建管道都在一个容器中执行，这很有意义。

由于我从 Github pages 转移过来，我也需要能够自动化
部署。推送一个回购，网站就建成了，部署了。

> 我在 ci.lanre.wtf 建立了一个个人 CI 服务器[。](https://ci.lanre.wtf) [如何设置无人机 CI 见安装文档](http://docs.drone.io/installation/)。

### 管道

管道是构建项目的步骤。对于一个更大的项目，它可以
包括一个 MongoDB 服务器来运行集成测试，或者仅仅是林挺来确保代码符合一定的质量。但对于我们的杰基尔部署来说，这只是

*   检查站点可以由 jekyll 创建..这就像`bundle exec jekyll build`一样简单。仅此而已。
*   将文件复制到远程位置。我的意思是这只是几页，没有别的事可做。

#### 确保网站建成

为此，我们将需要 jekyll docker 映像，因为这个 build
管道将在容器中运行。在你的`.drone.yml`文件中，添加下面的
；

```
 pipeline:
  build:
    image: ruby
    commands:
      - gem install bundler
      - bundle install
      - bundle exec jekyll build 
```

我们使用标准的`ruby`容器，安装 bundler，使用 bundler 安装
项目的依赖项。之后我们测试回购是否还在继续。

#### 连续部署

太棒了，我们已经设置了持续集成，以确保网站不会随机中断，但我们仍然需要自动进行 deeploy 更改。由于
的前一步构建了站点(进入`_site`目录)，所有需要的
就是将这些文件复制到某个位置。

这就是 Drone CI 中插件的用武之地。插件本身也是常规的 docker 容器。写插件，然后容器化，懂吗？

为此我选择了使用`rsync`，所以我使用这个[插件用于那个](https://github.com/Drillster/drone-rsync)。我们
需要给插件提供一些参数，这样它就可以为我们做所有的
艰苦工作，它们包括主机、源文件/目录、
主机上的目标、你的私钥。

> 您不必公开您的私钥。无人机对
> 机密有很好的支持..秘密价值是如此灵活，他们甚至可以限制到一个特定的 docker
> 图像，而且他们不会暴露在公关。参见[文件](http://docs.drone.io/manage-secrets/)
> 
> 这里有一个 [scp 插件](https://github.com/appleboy/drone-scp#usage-from-drone-ci)如果你想
> 使用它

要添加我们的私钥作为秘密，我们需要运行下面的代码:

```
 drone secret add \ 
  -repository repo/name \
  -image drillster/drone-rsync \
  -name rsync_key \
  -value @./path_to_id_rsa 
```

> 注意:你必须先做`export DRONE_SERVER=https://ci.yoursite.com`，然后再做`export DRONE_TOKEN=TOKEN_HERE`。你可以在 https://ci.example.com/account/token 的
> T3 拿到你的代币，而且那里有导游。

然后，您可以更新`.drone.yml`文件

```
 deploy:
    image: drillster/drone-rsync
    hosts: [ "example.com" ]
    source: _site/*
    target: ~/example.com
    recursive: true
    user: youruser
    delete: true
    secrets: [ rsync_key ] 
```

> 你也可以把你的用户作为一个秘密存储在`rsync_user`中。

你的`.drone.yml`现在应该是这个样子；

```
 pipeline:
  build:
    image: ruby
    commands:
      - gem install bundler
      - bundle install
      - bundle exec jekyll build

  deploy:
    image: drillster/drone-rsync
    hosts: [ "example.com" ]
    source: _site/*
    target: ~/example.com
    recursive: true
    user: youruser
    delete: true
    secrets: [ rsync_key ] 
```

您还可以采取一个额外的步骤，那就是确保`deploy`
管道在主设备上运行。这是为了方便拉请求，你
不会希望 PR 构建报告为失败——记住[秘密不会暴露给
拉请求](http://docs.drone.io/manage-secrets/)，因此部署步骤
将总是失败。为此，您必须更新您的部署步骤以包含

```
 when:
      event: [ push ] ## Would run only on a push to master
      branch: [ master ] ## Only the master branch can be deployed to production 
```

你可以在这里看一下[建立这个网站的配置](https://github.com/adelowo/personal-site/blob/master/.drone.yml)加上我在[的失败尝试，以确保这个工作](https://ci.lanre.wtf/adelowo/personal-site)