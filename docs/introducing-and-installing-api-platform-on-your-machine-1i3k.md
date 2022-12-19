# 在您的机器上介绍和安装 API 平台

> 原文：<https://dev.to/mrgeek/introducing-and-installing-api-platform-on-your-machine-1i3k>

我是一个全栈 web 开发人员，对 API 很认真。现在已经 2 年了，我仍然在用同样的步骤创建我实现的每一个 API，当然我是在用一些框架自动化一些东西，这些框架让事情变得容易、有组织和简单，从 [Express](https://expressjs.com/) 和 [Sails](https://sailsjs.com/) ，到使用 [Symfony](https://symfony.com/) ，最后是 [API 平台](https://api-platform.com/)！

两个月前——我的记性不好——我在想，我希望有一天我能更快地自动化 API 设计和实现，也许运行几个 PHP 命令来创建一个完整的实体及其关系和 API 路径，并公开它们，用一个基于注释的实体的自动生成的文档——也许——..直到我的一个朋友告诉我他偶然发现了这个很棒的框架，“我们需要检查一下，这 100%会很棒”——他说。他是对的，他们做了我想做的事情，甚至更多。

[![pffff](img/e2fb1e72e40c22e7bff8b2ad1db41a3c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RTN7hXTr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s5ku4wmmm336gcg29p5x.gif)T3】

<center>pffff!</center>

废话少说，反正我也不是什么好作家。让我们开始安装:

> 首先你需要在你的机器上安装 docker & [docker-compose](https://docs.docker.com/compose) ，你可以按照 docker 官方文档[这里](https://docs.docker.com/install)来安装。

1.  你需要克隆 API 平台 repo，通过运行这个命令`git clone git@github.com:api-platform/api-platform.git`
2.  然后当你站在回购的主根上。运行以下命令来实际运行平台容器`docker-compose up -d`

    > **如果您遇到此错误或类似情况**
    > 
    > T2`ERROR: for client Cannot start service client: driver failed programming external connectivity on endpoint api-platform_client_1 (7d82cb397e0a882f7474f227541ca43eb804316ece68c8a7dc5ac9af91972202): Error starting userland proxy: listen tcp 0.0.0.0:80: bind: address already in use`
    > 
    > 这实际上是不言而喻的，它意味着端口 80 正在你当前的操作系统中使用，所以检查哪个服务正在使用它，并杀死它或更好地改变它的端口。在我的例子中，这个服务是 apache，所以一个简单的 sudo 服务 apache2 stop 就足够了。

3.  现在一切都应该正常了，`docker-compose up -d`应该充满了绿色的 done 语句，表示一切都正常。你应该可以先去`[http://localhost](http://localhost)`，然后导航到`[https://localhost](https://localhost)`，看到很酷的 API 平台 PWA 客户端。

[![pffff](img/ad00a5c8e1e6ff4f5325cd244ef32e11.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9W38T58Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z5wcl64ea1fnbujt5iu5.gif)

> 但是如果不是呢！
> 在这里你可能会遇到这个[问题，没关系我遇到了，而且](https://github.com/api-platform/api-platform/issues/730) [jcarrier-vp solution](https://github.com/api-platform/api-platform/issues/730#issuecomment-399941316) 为我解决了这个问题。

现在，您已经准备好创建自己的 API，并考虑遵循 [API Platform awesome 文档](https://api-platform.com/docs/distribution)这真的很有帮助，他们有一个很酷的演示，可以帮助您更多地了解它。