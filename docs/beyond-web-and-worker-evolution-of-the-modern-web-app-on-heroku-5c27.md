# 超越网络和工作者:Heroku 上现代网络应用的演变

> 原文：<https://dev.to/heroku/beyond-web-and-worker-evolution-of-the-modern-web-app-on-heroku-5c27>

这是一系列博客文章中的第一篇，回顾了过去 10 年来 web 应用架构的发展。这篇文章分析了驱动架构变化的力量，以及新架构的高级视图。在以后的文章中，我们将放大到系统特定部分的细节。

适用于许多组织的标准 web 应用程序体系结构在过去 10 年中发生了巨大的变化。早在 2008 年 Heroku 的早期，一个标准的 web 应用程序架构由一个响应 HTTP 请求的 web 进程类型、一个持久化数据的数据库和一个管理[作业队列](https://devcenter.heroku.com/articles/background-jobs-queueing)的 worker 进程类型加上 Redis 组成。

<figure>[![An image of a common web app architecture on Heroku in 2008](img/59f48eda3e7a934bfeb0cb19fa636e4e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ad0r3KMQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://heroku-blog-files.s3.amazonaws.com/posts/1534270203-Beyond%2520Web%2520and%2520Worker%2520Blog%2520Post%2520%2528A%2529%25281%2529.png)

<figcaption>2008 年 Heroku 上的现代 web app</figcaption>

</figure>

现在，在 2018 年，网站(或移动应用程序)是许多公司与客户互动的主要方式。几乎每个企业都不可避免地融入了技术。随着技术被赋予为企业做更多事情的任务，我们作为开发人员需要新的架构来开始现代 web 应用程序。

<figure>[![An image of the evolution of the modern web app arhcitecture on Herkou](img/1b0a1559c2a49e61e4182d4cf52aac23.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nQOJC4Ou--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://heroku-blog-files.s3.amazonaws.com/posts/1534270441-Beyond%2520Web%2520and%2520Worker%2520Blog%2520Post%2520%2528C%2529.png) 

<figcaption>先睹为快:现代 web app 架构的演变</figcaption>

</figure>

## 改变驱动因素

在过去的十年里，用户对电脑、手机和软件的期望有什么变化？这种变化的驱动力是什么？这些变化对新的 web 应用架构意味着什么？

### 更快的网络体验

web 请求/响应模式的限制导致了糟糕的用户体验。想象一下，当你点击某个东西时，你电脑上安装的每个应用程序都必须重新渲染整个屏幕！现在——为了模拟网络的延迟——增加了使用二十年前的计算机但运行今天的软件的限制。这就是大部分网络用户的体验。令人震惊的是，我们忍受了这么多。

我们对更丰富、更具交互性和更低延迟的 web 体验的渴望推动了单页面应用程序和构建它们所需的 JavaScript 工具的流行。jQuery 等便利库的使用让位于 Backbone.js 和 Knockout.js 等库，这些库最终支持创建一个在用户浏览器中运行的内聚、可维护的应用程序。

下一代库——我们现在处于 2018 年——包括 React、Angular、Ember 和 Vue，诞生于我们对第一代库的体验，加上 Node.js(这里是作为一个构建工具)的爆炸式流行以及对 JavaScript(或者更准确地说，是版本 ES2015、16、17 等的 ECMAScript)的快速改进。

那么，为了让我们的 web 应用程序支持单页面应用程序，我们需要做哪些架构上的改变呢？首先，我们需要一种更有效地将 JavaScript 交付给用户浏览器的方法。单页应用程序包含的 JavaScript 比我们之前发送到浏览器的要多得多(可能还有更多图片、视频、CSS 和 HTML)。第二，这意味着我们需要一个公开的 API，单页应用程序可以从中获取动态数据。

下面是我们将要添加到架构中的新概念列表:

*   **内容交付网络** -有效的全球网络缓存，可以比我们的 web 服务器更快地向用户的浏览器交付 JavaScript、HTML、CSS 和图像。
*   **API Gateway**——一个公开的 API，单页应用程序可以通过它安全地请求它需要的数据。

### 智能手机

今天，我们大多数人都希望找到一个移动应用程序，用于我们想与之互动的任何产品或服务，或者至少是一个在我们的移动设备上运行良好的网站。我们现在使用移动设备做许多十年前只能在桌面浏览器上做的事情，甚至是我们永远无法在桌面浏览器上做的新事情，如接收基于位置的通知、扫描数字优惠券或跟踪锻炼。而且这种趋势看起来不会消失:[2011 年，世界人口的 10%是智能手机用户，现在 2018 年，三分之一是智能手机用户](https://www.statista.com/statistics/330695/number-of-smartphone-users-worldwide/)。在美国、瑞典和南韩等许多国家， [70%的人口都在使用智能手机](https://en.wikipedia.org/wiki/List_of_countries_by_smartphone_penetration)。

iOS 应用商店和 Android Market(现在的 Google Play)出现于 2008 年，开创了无处不在的原生移动应用的时代，因为移动网络浏览器过去没有、现在仍然没有(尽管它们变得越来越好)提供与原生应用相同的用户体验。

那么，在我们的架构中，我们需要什么来支持原生移动应用呢？我们需要一种方法让应用程序与我们的服务器进行通信，以接收推送通知、接收数字优惠券或保存用户锻炼的 GPS 数据。幸运的是，我们可以使用我们添加的概念之一来支持单页面应用程序:API 网关。

### API 作为新的 UI

好像现在每个公司都想要自己的 API。显然，像 Heroku、GitHub 和 Amazon Web Services 这样客户是开发人员的公司需要 API，但是现在许多客户不是开发人员的公司——像美国银行、T2、梅西百货、礼来公司和 T5——都有 API。ProgrammableWeb 的 [API 目录](https://www.programmableweb.com/apis/directory?order=created&sort=desc)列出了超过 19，000 个公司或其他组织构建的公共 API，其中大多数来自主要客户不是开发者的公司。所有这些 API 对开发者来说都是一种新的用户界面。

一个 API 为你的产品打开了数百万软件开发者创造性扩展的无限可能。谷歌地图、Twilio 和 Braintree 可能没有想到拼车是其 API 的用例，但如果没有地图、短信和支付 API，优步和 Lyft 就不可能实现。

API 还提供了与产品集成的标准点。通常用电插座来解释这一点。没有一个标准化的电源插座，我们将不得不手动为每一个新设备接入我们家的电力系统。电源插座为我们提供了设备和电网之间的标准接口。类似地，因为 API 有共同的标准，比如使用 [JSON](https://en.wikipedia.org/wiki/JSON) 内容类型的 [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) over [HTTP](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol) ，我们作为开发人员需要更少的时间和专有技术来集成两个不同的系统。

正如您可能已经猜到的，这个 API 可以使用我们在上一节中添加的相同的架构概念:API 网关。

### 流数据(和大量数据)

我们开发的软件正在以指数增长的速度产生数据。我们对这些数据的要求越来越高。我们不仅需要数据来构建用户的网络或移动体验，还需要数据来进行报告、通知 A/B 测试或蓝/绿部署、用户体验研究、计费和合规性。我们通常需要数据的实时视图，并能够灵活地创建不同的视图来支持不断发展的业务需求。

“数据消防水管”是一个比喻，用来描述对所有这些移动数据的管理。我们需要一个数据消防软管来帮助有组织地移动这些数据。它可以是将数据从生产点移动到存储点，允许内部服务相互通信，或者将实时数据发送到分析和可视化工具。

那么，我们需要在我们的架构中添加什么来管理、观察、分析和可视化数据流呢？

*   **[阿帕奇卡夫卡](https://devcenter.heroku.com/articles/kafka-on-heroku)**——一个可以可靠地管理大量数据流的工具，数据生产者和数据消费者可以很容易地连接到它
*   **[AWS S3](https://aws.amazon.com/s3/) / [谷歌云存储](https://cloud.google.com/storage/)**——对所有数据进行有效的无限和冗余存储
*   **[AWS 红移](https://aws.amazon.com/redshift/)/[Google big query](https://cloud.google.com/bigquery/)**——一个数据仓库工具，用来组织数据并使其快速可查询
*   **[Heroku data clips](https://devcenter.heroku.com/articles/dataclips)/[Metabase](https://www.metabase.com/)/[Looker](https://looker.com/)**-数据查询和可视化工具，简单连接到数据仓库，易于开始使用

### 适应性

不断增加的复杂性(即业务复杂性、需求复杂性、软件对业务越来越重要、用户对实时性的期望越来越高、数据量不断增长等)。)意味着开发者需要构建和操作适应性更强的系统。

构建适应性更强的系统的一种越来越流行的方法是由许多小的、离散的服务组合而成。您可以将这种技术称为微服务、分布式系统、面向服务的架构，甚至是功能即服务，重要的概念是将单独维护和部署的软件组件组合成一个系统。

将新功能构建为独立的服务，而不是放在一个整体中，这样可以更容易地分离关注点。作为一名开发人员，在构建或维护较小的服务时，我可以在头脑中保留较少的概念。作为一名工程经理，我的团队可以并行处理更多项目。需要说明的是，这种技术并非没有缺陷。看看 Ryan Townsend 的博客文章,关于他的公司将一个庞然大物解构为服务的旅程。

### 别忘了秤

显然，对规模的需求在 2018 年并不新鲜，但所有这些新架构组件的添加使得规模扩展比十年前更加困难。以前，我们可以通过三个杠杆来管理我们的大部分伸缩需求:web 的水平伸缩 [dynos](https://devcenter.heroku.com/articles/dynos) (Heroku 的轻量级容器技术)、worker dynos 的水平伸缩和 PostgreSQL 的垂直伸缩。现在我们有几十个杠杆需要担心。互联网用户的持续增长进一步加剧了规模问题。[在我们美丽的蓝色大理石上，有一半的人可以上网！](https://en.wikipedia.org/wiki/Global_Internet_usage#Internet_users)

这是 PaaS 和托管服务显示其价值的一个领域。好的方法可以减少你在扩展系统时需要考虑的杠杆数量。

## 汇集一切

因此，让我们看看我们需要添加到架构中的所有组件:

*   **内容交付网络** -有效的全球网络缓存，可以比我们的 web 服务器更快地向用户的浏览器交付 JavaScript、HTML、CSS 和图像。
*   **API Gateway**——一个公开的 API，单页应用程序可以通过它安全地请求它需要的数据。
*   **[阿帕奇卡夫卡](https://devcenter.heroku.com/articles/kafka-on-heroku)**——一个可以可靠地管理大量数据流的工具，数据生产者和数据消费者可以很容易地连接到它
*   **[AWS S3](https://aws.amazon.com/s3/) / [谷歌云存储](https://cloud.google.com/storage/)**——对所有数据进行有效的无限和冗余存储
*   **[AWS 红移](https://aws.amazon.com/redshift/)/[Google big query](https://cloud.google.com/bigquery/)**——一个数据仓库工具，用来组织数据并使其快速可查询
*   **[Heroku data clips](https://devcenter.heroku.com/articles/dataclips)/[Metabase](https://www.metabase.com/)/[Looker](https://looker.com/)**-数据查询和可视化工具，简单连接到数据仓库，易于开始使用

我们需要思考两个新概念:

*   面向服务的适应性架构
*   可扩展-首先进行设计，使扩展成为正常的运营活动，而不是凌晨 2 点的繁重活动

让我们从高层次来看我们的新架构。我们讨论的所有新组件都已整合到一个系统中。

<figure>[![An image of a new modern web app architecture](img/059a1e315d388b704e4cd5d38dfcbe6d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8NJYWQw_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://heroku-blog-files.s3.amazonaws.com/posts/1534271873-Beyond%2520Web%2520and%2520Worker%2520Blog%2520Post%2520%2528B%2529%25281%2529.png)

<figcaption>2018 年现代 web app 架构</figcaption>

</figure>

与 2008 年的版本相比，它看起来要构建和管理很多东西，对吗？如果您尝试自己构建和管理它，这是可能的。事实是，使用像 Heroku 这样的 PaaS 比自己在 IaaS(或自己的数据中心)上构建更容易部署和管理这样的东西。

## 展望未来

当然，我们仍然有许多悬而未决的问题。dynos 是如何发现彼此并相互交流的？CDN 提供了什么，它是如何到达那里的？卡夫卡如何将数据写入 S3 或红移？代码如何部署工作？CI/CD 是如何工作的？我们如何监控所有组件的健康状况？我们需要考虑哪些安全问题？

在后续的博客文章中，我们将放大这个系统的特定部分，并向您展示如何将它们部署到 Heroku。在那之前，我希望这个高级架构能够作为许多组织在 2018 年构建 web 应用程序时需要考虑的主要概念的指南。

非常感谢帮助我写这篇文章的人:Vik Rana、Charlie Gleason、Jennifer Hooper、Scott Truitt、Jon Mountjoy、Jon Byrum、Michael Friis、Hunter Loftis、Stephanie Chung 和 Jonathan Fulton 的 [Web Architecture 101](https://engineering.videoblocks.com/web-architecture-101-a3224e126947) 文章。非常感谢您的评论、讨论、想法、灵感、语法帮助、绘图和错别字捕捉。