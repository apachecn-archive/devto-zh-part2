# 拉勒维尔及其生态系统

> 原文：<https://dev.to/ppshobi/a-look-into-laravel-and-its-ecosystem-e52>

作为一名主要从事 PHP 和 Laravel 的开发人员，我接触过大量与 Laravel 相关的服务和产品。这些使得 Laravel 在 PHP 开发人员中很受欢迎(我认为是非常受欢迎)。

尽管 Laravel 被批评为包含电池的框架，但自 2014 年 6 月推出稳定的 4.2 版以来，它的受欢迎程度飙升。

#### 查看过去 5 年的谷歌趋势图

[![Laravel Google Trends](../Images/d9b0c19089ab4539e89cc958bcafca57.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qsNoLdlj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4n4a6gmkuhryax3wqtn5.png)

我想说 Laravel 获得了它的发展势头，不仅因为它是一个开发人员友好的框架或者一个健壮的框架；而是因为它周围的产品和服务生态系统有助于更快地制作应用程序。很像苹果的产品和生态系统。

截至 2018 年 8 月， **Laravel 5.7** 发布，随着生态系统的出现，它变得更加强大。让我在这里列举一些。即使我没有在生产中使用它们，我也会认为这些东西和 laravel 本身一样重要。

1 .[Laracasts.com](https://laracasts.com)

当没有足够的资源向开发人员介绍或教授框架时，框架的健壮性没有帮助。至少在我的例子中，我非常懒的去学习一个新的框架，因为我从它那里学到的只是框架如何做一件特定的事情？在 Framework X 中路由是如何完成的并没有改变路由是什么。

Laracasts 是 laravel 世界中显而易见的名字之一。Jefrrey Way 制作关于 laravel 和相关技术的最新截屏。我个人觉得这些视频很有娱乐性和知识性(这是开发者的网飞)。Laracasts 不仅仅是关于 Laravel 的，它也是 PHP 不可分割的一部分，并介绍了它的复杂性。

2 .[Laravel.com](https://laravel.com)

不管软件是什么，如果没有文档，它是不完整和不可用的。Laravel.com 包含框架的文档，官方支持的包，如 Scout、Passport 等，以及相关主题，如设置开发环境、部署说明等

作为一个开源项目，laravel 文档也是不断更新和准确的。

3 .[拉勒维尔家园&贴身男仆](https://laravel.com/docs/homestead)

以前，建立一个与生产环境相似的开发环境是一件痛苦的事情，但是有了虚拟化技术，这项任务就像安装几个软件一样简单，而且也不需要担心跨平台的问题。

我同情那些使用 xampp 或 mamp 并在部署后抱怨的人。是时候换到新世界了，伙计们…

4 .发送

Envoyer 是构建在 laravel 本身之上的部署工具。它可以挂钩到您的版本控制系统事件，并自动触发部署。Envoyer 可以在 AWS、Digital Ocean 等平台上部署任何 PHP 应用程序……(当然，正如他们所说，是为 Laravel 而调的)。

5 .拉勒维尔锻造厂

Laravel forge 是一项服务器供应和管理服务。它可以在 AWS、Digital Ocean 或 Linode 上为你创建和管理 PHP 服务器。它可以提供 SSL 证书，也可以管理部署。

Forge 为您提供了先进的 Linux 服务器配置，并且还支持水平扩展。

6 .[拉里韦尔内腔](https://lumen.laravel.com/)

卢蒙是拉勒维尔的弟弟。Laravel 的精简版本，主要关注于 api 和微服务的开发。

一个用于构建基于 API 的应用程序的 goto 软件。

7 .[拉勒维尔火花](https://spark.laravel.com/)

我不会说 spark 是 laravel 开发者生态系统不可或缺的一部分。但是要超快地搭建一个应用程序，你需要 laravel spark。Spark 可以用现成的账单、订阅、发票、通知等帮助你启动一个应用程序

8 .拉勒维尔新星

新星是生态系统的新成员。Laravel Nova 为您的应用程序提供了一个管理面板。通过添加现成的 CRUD 组件，您可以非常容易地构建一个管理部分。

通常 nova 不需要任何数据库表来管理自己。它完全是代码驱动的。你根本不用接触现有的 laravel 型号。

9 . [Laravel 地平线和队列](https://horizon.laravel.com/)

Laravel 自带队列组件。您可以通过 horizon 轻松查看和管理您的异步作业和队列。

Horizon 与 Redis 集成，它可以根据指定的配置自动重试失败的作业，这使我们的生活更加轻松。

10 .[拉韦勒换挡](https://laravelshift.com/)

Laravel Shift 是一项可以代表您自动升级 Laravel 版本的服务。你所要做的就是访问你的 git 库，它会通过创建一个详细的 pull 请求来升级你的代码库。

11 .自 2012 年 Laravel 首次发布以来，Laravel News 一直为我们带来最新的新闻、教程和播客。我建议每个人都去那里看看，以获得关于 Laravel 的最新消息。

### 最终注释

PHP 运行着大约 70%的网络。作为截至目前 GitHub 中~46k 星的开源项目，Laravel 确实脱颖而出。它没有任何意义，但它将存在一段时间。与官方软件包，如收银员的帐单，童军的即时搜索等...它甚至更加强大，几乎每天都有新的特性和缺陷被添加到这个框架中。

🔥🔥**我想说请继续关注更多精彩内容**🔥🔥