# 我为什么要用 Node.js？

> 原文：<https://dev.to/apcelent/why-the-hell-would-i-use-nodejs-17k>

## 简介

JavaScript 越来越受欢迎，随之带来了许多变化，如今 web 开发的面貌已经大不相同。如今，我们可以通过运行在服务器和浏览器上的 JavaScript 在网络上做的事情，在几年前是难以想象的，或者被封装在沙盒环境中，如 Flash 或 Java 小程序。

在深入研究 Node.js 之前，您可能想了解一下在统一语言和数据格式(JSON)的堆栈中使用 JavaScript 的好处，这允许您以最佳方式重用开发人员资源。由于这更像是 JavaScript 而不是 Node.js 带来的好处，我们在这里就不多讨论了。但是在堆栈中加入节点是一个关键优势。

正如维基百科所说:“Node.js 是 Google 的 V8 JavaScript 引擎、libuv 平台抽象层和核心库的打包汇编，核心库本身主要是用 JavaScript 编写的。”除此之外，值得注意的是，Node.js 的创始人 Ryan Dahl 的目标是创建具有推送功能的实时网站，“受 Gmail 等应用程序的启发”。在 Node.js 中，他为开发人员提供了一个在非阻塞、事件驱动的 I/O 范例中工作的工具。

在经历了 20 多年基于无状态请求-响应范例的无状态 web 之后，我们终于拥有了具有实时双向连接的 web 应用程序。

[![Alt text of image](img/cd5ea9c2d54108b2bedd590898bc543c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--llpaXIMr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.apcelent.cimg/apcelent-nodejs-tutorial.jpg)

一句话:Node.js 在通过 websockets 使用推送技术的实时 web 应用程序中大放异彩。这有什么革命性的？在经历了 20 多年基于无状态请求-响应范式的无状态 web 之后，我们终于有了具有实时双向连接的 web 应用程序，其中客户端和服务器都可以发起通信，允许它们自由地交换数据。这与典型的 web 响应范例形成了鲜明的对比，在典型的 web 响应范例中，客户端总是发起通信。此外，它完全基于运行在标准端口 80 上的开放 web 堆栈(HTML、CSS 和 JS)。

有人可能会说，我们已经以 Flash 和 Java 小程序的形式实现了多年，但实际上，这些只是沙盒环境，使用 web 作为传输协议交付给客户端。此外，它们是孤立运行的，通常在非标准端口上运行，这可能需要额外的权限等。

凭借其所有优势，Node.js 现在在许多依赖其独特优势的知名公司的技术堆栈中发挥着关键作用。

在这篇文章中，我不仅将讨论这些优势是如何实现的，还将讨论为什么您可能想要使用 Node.js，以及为什么不使用，并使用一些经典的 web 应用程序模型作为示例。

## 它是如何工作的？

Node.js 的主要思想:在面对跨分布式设备运行的数据密集型实时应用时，使用非阻塞、事件驱动的 I/O 来保持轻量级和高效性。

那是一口。

 `What it really means is that Node.js is not a silver-bullet new platform that will dominate the web development world. Instead, it’s a platform that fills a particular need.` 

它真正的意思是，Node.js 不是一个将主宰 web 开发世界的银弹新平台。相反，它是一个满足特定需求的平台。理解这一点是绝对必要的。你肯定不希望使用 Node.js 进行 CPU 密集型操作；事实上，将它用于繁重的计算将会抵消它几乎所有的优势。Node 真正的亮点在于构建快速、可伸缩的网络应用程序，因为它能够以高吞吐量处理大量的并发连接，这相当于高可伸缩性。

它是如何在引擎盖下工作的非常有趣。在传统的 web 服务技术中，每个连接(请求)都会产生一个新线程，占用系统 RAM 并最终达到可用 RAM 的最大容量，与之相比，Node.js 在单线程上运行，使用非阻塞 I/O 调用，允许它支持数万个并发连接(保存在事件循环中)。

[![Alt text of image](img/bd5a329a06f910678cafd39dd8f63ee8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cv22FO8H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.apcelent.cimg/apcelent-nodejs-toptal1.png)

快速计算一下:假设每个线程都有 2 MB 的内存，那么在一个有 8 GB 内存的系统上运行，理论上最多有 4000 个并发连接，再加上线程间上下文切换的成本。这是传统 web 服务技术中通常会遇到的情况。通过避免所有这些，Node.js 实现了超过 100 万并发连接的可伸缩性级别(作为概念验证)。

当然，还有在所有客户机请求之间共享一个线程的问题，这是编写 Node.js 应用程序的一个潜在缺陷。首先，繁重的计算会阻塞节点的单线程，并给所有客户端带来问题(稍后将详细介绍)，因为传入的请求会被阻塞，直到所述计算完成。其次，开发人员需要非常小心，不要让异常冒泡到核心(最顶层)Node.js 事件循环，这将导致 Node.js 实例终止(实际上使程序崩溃)。

用于避免异常浮出水面的技术是将错误作为回调参数传递回调用者(而不是像在其他环境中那样抛出它们)。即使一些未处理的异常设法冒泡，也有多种范例和工具可用于监视节点进程并对崩溃的实例执行必要的恢复(尽管您将无法恢复用户的会话)，最常见的是 Forever 模块，或者使用外部系统工具 upstart 和 monit 的不同方法。

## NPM:节点包经理

在讨论 Node.js 时，有一点绝对不能忽略，那就是使用 NPM 工具对包管理的内置支持，这是每个 Node.js 安装默认提供的。NPM 模块的思想与 Ruby Gems 非常相似:一组公开可用的、可重用的组件，通过在线存储库的简单安装即可获得，并具有版本和依赖性管理。

打包模块的完整列表可以在 [NPM 网站](https://npmjs.org/)上找到，或者使用 Node.js 自动安装的 NPM CLI 工具访问。模块生态系统对所有人开放，任何人都可以发布他们自己的模块，这些模块将被列入 NPM 资源库。在 http://howtonode.org/introduction-to-npm 的[可以找到 NPM 的简介(有点旧，但仍然有效)。](http://howtonode.org/introduction-to-npm)

当今最受欢迎的 NPM 模块包括:

*   Express - Express.js，一个受 Sinatra 启发的 Node.js web 开发框架，也是当今大多数 node . js 应用程序的事实标准。

*   [Connect](https://github.com/senchalabs/connect#readme) - Connect 是 Node.js 的可扩展 HTTP 服务器框架，提供了一组被称为中间件的高性能“插件”；这是 Express 的基础。

*   [Socket.io](https://socket.io/) 和[Sockjs](https://github.com/sockjs)——当今最常见的两个 websockets 组件的服务器端组件。

*   [Jade](https://pugjs.org/api/getting-started.html) -流行的模板引擎之一，灵感来自 HAML，Express.js 中的默认设置

*   [mongo])([https://www.npmjs.com/package/mongodb](https://www.npmjs.com/package/mongodb))和[mongojs](https://github.com/mafintosh/mongojs)——MongoDB 包装器为 Node.js 中的 MongoDB 对象数据库提供 API

*   [递归](https://github.com/NodeRedis/node_redis)递归客户端库。

*   coffee-script 编译器，允许开发者使用 coffee 编写他们的 Node.js 程序。

*   [下划线](https://www.npmjs.com/package/underscore) ( [lodash](https://lodash.com/) ，[lazy](https://www.npmjs.com/package/lazy))——JavaScript 中最流行的实用程序库，打包用于 Node.js，以及它的两个对应物，通过采用稍微不同的实现方法，承诺[更好的性能](http://philosopherdeveloper.com/posts/introducing-lazy-js.html)。

*   [forever](https://www.npmjs.com/package/forever)——可能是确保给定节点脚本持续运行的最常见的实用程序。面对任何意外故障，保持 Node.js 流程在生产中正常运行。

这样的例子不胜枚举。有大量真正有用的包可供所有人使用(无意冒犯我在这里省略的那些包)。

## 应该使用 Node.js 的地方的例子

## 聊天

聊天是最典型的实时多用户应用。从 IRC(过去)，通过在非标准端口上运行的许多专有和开放的协议，到今天在 Node.js 中使用在标准端口 80 上运行的 websockets 实现一切的能力。

聊天应用程序实际上是 Node.js 的最佳范例:它是一个轻量级、高流量、数据密集型(但处理/计算量低)的应用程序，运行在分布式设备上。这也是一个很好的学习用例，因为它很简单，但是它涵盖了您将在典型的 Node.js 应用程序中使用的大多数范例。

让我们试着描述一下它是如何工作的。

在最简单的例子中，我们的网站上有一个单独的聊天室，人们可以在那里以一对多(实际上是全部)的方式交换信息。例如，假设我们在网站上有三个人都连接到我们的留言板。

在服务器端，我们有一个简单的 Express.js 应用程序，它实现了两件事:1)GET '/'请求处理程序，它为包含留言板和“发送”按钮的网页提供服务，以初始化新的消息输入；2)websockets 服务器，它侦听 web socket 客户端发出的新消息。

在客户端，我们有一个设置了两个处理程序的 HTML 页面，一个用于“Send”按钮单击事件，它获取输入消息并将其发送到 websocket，另一个用于侦听 websockets 客户端上的新消息(即其他用户发送的消息，服务器现在希望客户端显示这些消息)。

当其中一个客户端发布消息时，会发生以下情况:

1.  浏览器通过 JavaScript 处理程序捕捉“发送”按钮的点击，从输入字段(即消息文本)中获取值，并使用连接到我们服务器的 websocket 客户端(在网页初始化时初始化)发出 websocket 消息。

2.  websocket 连接的服务器端组件接收消息，并使用广播方法将其转发给所有其他连接的客户端。

3.  所有客户端通过在网页中运行的 websockets 客户端组件接收作为推送消息的新消息。然后，他们获取消息内容，并通过将新消息附加到公告板上来就地更新网页。

[![Alt text of image](img/3ed0effbbd2425c62cdf1066b0a12dfb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HWPD-Tot--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.apcelent.cimg/apcelent-nodejs-toptal2.png)

这是一个最简单的例子。对于更健壮的解决方案，可以使用基于 Redis 存储的简单缓存。或者在一个更高级的解决方案中，一个消息队列来处理消息到客户端的路由，以及一个更健壮的交付机制，该机制可以弥补临时连接丢失或在注册客户端离线时为其存储消息。但是不管你做了什么改进，Node.js 仍然会在相同的基本原则下运行:对事件做出反应，处理许多并发连接，并保持用户体验的流畅性。

## 对象 DB 之上的 API

尽管 Node.js 确实在实时应用程序中大放异彩，但它非常适合公开来自对象数据库(例如 MongoDB)的数据。JSON 存储的数据允许 Node.js 在没有阻抗不匹配和数据转换的情况下工作。

例如，如果您正在使用 Rails，您将从 JSON 转换成二进制模型，然后当数据被 Backbone.js、Angular.js 等使用时，通过 HTTP 将它们公开为 JSON。，甚至是普通的 jQuery AJAX 调用。使用 Node.js，您可以简单地用 REST API 公开您的 JSON 对象，供客户端使用。此外，当从数据库中读取或写入数据时(如果您使用的是 MongoDB)，您不需要担心 JSON 和其他任何东西之间的转换。总之，通过在客户机、服务器和数据库中使用统一的数据序列化格式，可以避免多次转换。

## 排队输入

如果您正在接收大量并发数据，您的数据库可能会成为瓶颈。如上所述，Node.js 本身可以轻松处理并发连接。但是因为数据库访问是一个阻塞操作(在这种情况下)，我们遇到了麻烦。解决方案是在数据真正写入数据库之前确认客户端的行为。

通过这种方法，系统可以在高负载下保持其响应能力，这在客户端不需要确认数据写入成功时特别有用。典型的例子包括:记录或写入用户跟踪数据，成批处理，直到以后才使用；以及不需要立即反映的操作(如在脸书上更新“赞”计数)，其中[最终一致性](https://www.allthingsdistributed.com/2007/12/eventually_consistent.html)(在 NoSQL 世界中经常使用)是可接受的。

数据通过某种缓存或消息队列基础设施(例如， [RabbitMQ](http://www.rabbitmq.com/) ， [ZeroMQ](http://zeromq.org/) 进行排队，并由单独的数据库批量写入过程或计算密集型处理后端服务进行消化，这些服务编写在性能更好的平台上，用于执行此类任务。类似的行为可以用其他语言/框架实现，但不是在相同的硬件上，具有相同的高吞吐量。

[![Alt text of image](img/bc24a90912a27c488649aa1c339cf6c6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--R-Zbbw4W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.apcelent.cimg/apcelent-nodejs-toptal3.png)

简而言之:使用 Node，您可以将数据库写操作推到一边，稍后再处理它们，就好像它们成功了一样。

## 数据流

在更传统的 web 平台中，HTTP 请求和响应被视为孤立的事件；事实上，它们实际上是溪流。这个观察结果可以在 Node.js 中用来构建一些很酷的特性。例如，可以在文件上传的同时对其进行处理，因为数据是通过数据流传入的，我们可以以在线方式对其进行处理。这可以用于实时音频或视频编码，以及不同数据源之间的代理(见下一节)。

## 代理

Node.js 很容易用作服务器端代理，它可以以非阻塞的方式处理大量的并发连接。这对于代理具有不同响应时间的不同服务，或者从多个源点收集数据特别有用。

举个例子:假设一个服务器端应用程序与第三方资源进行通信，从不同来源获取数据，或者将图像和视频等资产存储到第三方云服务。

尽管存在专用的代理服务器，但是如果您的代理基础设施不存在或者您需要一个本地开发的解决方案，那么使用 Node 可能会有所帮助。我的意思是，您可以使用 Node.js 开发服务器构建一个客户端应用程序，用于资产和代理/存根 API 请求，而在生产中，您可以使用专用的代理服务(nginx、HAProxy 等)来处理此类交互。).

## 券商-股票交易员的仪表盘

让我们回到应用程序级别。另一个桌面软件占主导地位，但很容易被实时网络解决方案取代的例子是经纪人的交易软件，用于跟踪股票价格，进行计算/技术分析，并创建图形/图表。

切换到基于网络的实时解决方案将允许经纪人轻松切换工作站或工作地点。很快，我们可能会在佛罗里达的海滩上看到它们..或者伊比沙岛..或者巴厘岛。

## 应用监控仪表板

另一个非常适合 web 套接字节点的常见用例是:跟踪网站访问者并实时可视化他们的交互。(如果你感兴趣，这个想法已经被[蜂鸟](http://hummingbirdstats.com/)产品化了)。

你可以从你的用户那里收集实时的统计数据，或者当你的访问者到达你的漏斗中的一个特定点时，你可以通过打开一个沟通渠道来引入与他们的有针对性的互动，从而将它提升到一个新的水平。(如果你感兴趣，这个想法已经被 [CANDDi](https://www.canddi.com/) 产品化了)。

想象一下，如果你实时知道你的访问者在做什么，如果你可以可视化他们的互动，你将如何改善你的业务。有了 Node.js 的实时双向套接字，现在就可以了。

## 系统监控仪表板

现在，让我们来看看基础设施方面。例如，设想一个 SaaS 提供商想要为其用户提供一个服务监控页面(例如 GitHub 的状态页面)。使用 Node.js 事件循环，我们可以创建一个强大的基于 web 的仪表板，它以异步方式检查服务的状态，并使用 websockets 将数据推送到客户端。

使用这种技术，可以实时报告内部(公司内部)和公共服务的状态。将这一想法推进一步，尝试想象一个网络运营中心(NOC)监控电信运营商、云/网络/托管提供商或一些金融机构中的应用，所有这些应用都运行在由 Node.js 和 websockets 支持的开放 web 堆栈上，而不是 Java 和/或 Java 小程序。

注意:不要试图在 Node 中构建硬实时系统(即需要一致响应时间的系统)。对于这类应用程序，Erlang 可能是更好的选择。

## 服务器端 WEB 应用

带有 Express.js 的 Node.js 也可以用于在服务器端创建经典的 web 应用程序。然而，尽管可能，Node.js 在呈现的 HTML 中使用的这种请求-响应范例并不是最典型的用例。这种方法有正反两方面的理由。以下是一些需要考虑的事实:

优点:

*   如果您的应用程序没有任何 CPU 密集型计算，您可以用 Javascript 从上到下构建它，如果您使用像 MongoDB 这样的 JSON 存储对象 DB，甚至可以构建到数据库级别。这大大简化了开发(包括招聘)。

*   爬虫会收到完全呈现的 HTML 响应，这比运行在 Node.js 上的单个页面应用程序或 websockets 应用程序要友好得多。

缺点:

*   任何 CPU 密集型计算都会阻碍 Node.js 的响应，因此线程平台是更好的方法。或者，您可以尝试扩展计算[*]。

*   在关系数据库中使用 Node.js 仍然是一件痛苦的事情(更多细节见下文)。帮自己一个忙，如果你想执行关系操作，可以选择任何其他环境，比如 Rails、Django 或 ASP.Net MVC。[*]这些 CPU 密集型计算的替代方法是创建一个高度可伸缩的 MQ 支持的环境，通过后端处理让 Node 作为前端“职员”来异步处理客户端请求。

## 不应该使用 Node.js 的地方

## 服务器端 WEB 应用后面带有关系数据库

例如，将 Node.js 和 Express.js 与 Ruby on Rails 进行比较，当涉及到关系数据访问时，显然倾向于后者。

Node.js 的关系数据库工具仍处于早期阶段；他们相当不成熟，不太容易共事。另一方面，Rails 自动提供开箱即用的数据访问设置，以及 DB 模式迁移支持工具和其他 gem(双关语)。Rails 及其同类框架有成熟的、经过验证的活动记录或数据映射器数据访问层实现，如果您试图用纯 JavaScript 复制它们，您会非常怀念这些实现。[*]

尽管如此，如果你真的倾向于一直保持 JS(并准备拔掉一些头发)，请留意 Sequelize 和 Node or m2——两者都还不成熟，但它们最终可能会赶上。

[*]在保持 Rails 后端及其对关系数据库的轻松访问的同时，将 Node 单独用作前端是可能的，这并不罕见。

## 繁重的服务器端计算/处理

说到繁重的计算，Node.js 并不是最好的平台。不，你肯定不想在 Node.js 中构建一个[斐波那契计算服务器。通常，任何 CPU 密集型操作都会抵消 Node 通过其事件驱动的非阻塞 I/O 模型提供的所有吞吐量优势，因为当线程忙于处理数字时，任何传入的请求都会被阻塞。](https://zef.me/node-js-and-the-case-of-the-blocked-event-loop-27e20a3aca20)

如前所述，Node.js 是单线程的，只使用一个 CPU 内核。当谈到在多核服务器上增加并发性时，节点核心团队正在以集群模块的形式做一些工作[ref:[http://nodejs.org/api/cluster.html](http://nodejs.org/api/cluster.html)。您还可以通过 nginx 在反向代理后面非常容易地运行几个 Node.js 服务器实例。

对于集群，您仍然应该将所有繁重的计算任务卸载到在更合适的环境中编写的后台进程，并让它们通过 RabbitMQ 这样的消息队列服务器进行通信。

即使您的后台处理最初可能运行在同一台服务器上，这种方法也有可能具有非常高的可伸缩性。这些后台处理服务可以很容易地分发到不同的工作服务器，而不需要配置前端 web 服务器的负载。

当然，您也可以在其他平台上使用相同的方法，但是使用 Node.js，您可以获得我们已经讨论过的高请求/秒吞吐量，因为每个请求都是一个小任务，可以非常快速有效地处理。

## 结论

我们已经从理论到实践讨论了 Node.js，从它的目标和抱负开始，到它的优点和缺点结束。当人们遇到 Node 的问题时，几乎总是归结为这样一个事实:阻塞操作是万恶之源——99%的 Node 误用都是直接后果。

 `In Node, blocking operations are the root of all evil—99% of Node misuses come as a direct consequence.` 

记住:Node.js 从来不是为了解决计算伸缩问题而创建的。它是为解决 I/O 伸缩问题而创建的，[它确实做得很好](https://www.srirangan.net/2012-05-node-js-critics-which-part-of-event-driven-non-blocking-io-model-you-dont-understand)。

为什么要用 Node.js？如果您的用例不包含 CPU 密集型操作，也不访问任何阻塞资源，您可以利用 Node.js 的优势，享受快速和可伸缩的网络应用程序。欢迎来到实时网络。

这篇文章最初发表在 [Apcelent 技术博客](https://blog.apcelent.com/why-the-hell-would-i-use-nodejs.html)