# 构建一个定制的 WordPress Nodejs 应用程序🍃MongoDB 图集，💙微软 Azure ML/AI，和⚡无服务器功能！

> 原文：<https://dev.to/mrahmadawais/building-a-custom-wordpress-nodejs-app-with--mongodb-atlas--microsoft-azure-mlai-and--serverless-functions-3eec>

**TL；DR** 我正在为一个企业客户端构建一个定制的 WordPress 仪表板，它由 Node.js 之上的 React.js 提供支持，以 MongoDB Atlas 作为数据库。

这个仪表板使用几个微软 Azure 服务，例如认知服务、Azure 应用服务，尤其是无服务器的⚡ Azure 功能。在这篇文章中，你将学习如何用它来构建一个小模块，以及我选择堆栈、应用和产品背后的原因。

* * *

🚀我的一个企业客户拥有一家大型网络和媒体公司，他建立了一个大规模的 WordPress 网站。他最近[咨询我](https://ahmadawais.com/contact/)关于建立一个定制的 WordPress 仪表盘(基于 [WordPress REST API](https://developer.wordpress.org/rest-api/) )的可能性——通过机器学习和人工智能帮助他做出明智的商业决策。

🤔随着 JavaScript 蚕食世界，WordPress 通过[创建 Gutenberg](https://ahmadawais.com/create-guten-block-toolkit/) 项目来适应这种变化——我想到了一种架构/堆栈，其中 WordPress 将成为我们的内容层，这是一个熟悉的经过战斗考验的环境，它可以很好地完成用 JavaScript 构建的定制仪表板的工作。

😲当您的任务是构建一个现代 JavaScript 应用程序时，您会发现自己处于不同框架、工具和开发工作流的混合体中。JavaScript 生态系统在过去的几年里成长了很多。今天我们有许多好的选择。

🎟所以，在研究了我的选择之后——我选择在 Node.js 之上使用 React.js 来开始构建定制的 WordPress 仪表盘。虽然该项目目前处于构思阶段，但我认为在这里分享我们的一些目标以定义我选择堆栈背后的背景是很重要的。

# 🛡

## 自定义仪表盘目标

假设您拥有一家大型网络公司，其中有超过 500 家酒店(位于三个不同的国家)使用您的服务为他们的会议厅、IT 会议和在线财产管理(如网站和博客)提供支持。我的客户就是这么做的。

这大部分是由一个巨大的多站点 WordPress 实例驱动的，它管理酒店、网站、在线预订、注册、活动、门票、评论和评论的一切。也有其他运行不同软件的系统能够通过 REST API 产生内容。

我们已经开始创建一个定制的 WordPress 仪表盘，有很多目标，但是我列出了一些和这篇文章相关的目标。看看我到目前为止构建的，都是基于无服务器的 Azure 函数——非常棒。

[![A Custom WordPress Dashboard with MongoDB Atlas, Microsoft Azure, & Serverless Functions!](img/ac39d63f9d873d586986323bed6b106d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ERLa79cW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://ddwva799xzrph.cloudfront.net/items/3b401D3l3r2D1F2I2g30/Screen%2520Recording%25202018-05-22%2520at%252008.20%2520PM.gif)

### 👀高级数据报告

定制仪表板将报告所有高级数据，例如，我的客户的投资组合(500 多家酒店)中发生的实时销售、基于实体/时间和基于日期的细分。

以及他的每支球队每天、每周、每月的表现。所有这些数据都被输入到 MongoDB Atlas 中。稍后会详细介绍。

### ⚡Serverless 自动化

大多数模块都是建立在无服务器架构之上的——在这种情况下，这提供了巨大的好处。所有的自动化总是在运行，费用是随用随付的，即按使用量付费。

初步粗略估计，该解决方案比让服务器虚拟机一直运行要经济 34%。我们使用 [Azure 函数](https://azure.microsoft.com/en-us/services/functions/)来实现这种无服务器自动化。

### 🔥物联网中心

大约有 200 名 IT 经理为我的客户工作，他们拥有支持物联网的设备，可以向多个在线项目提供数据。该定制仪表板还包括用于做出更好决策的数据，并将整个注册、管理和维护团队的中心连接到一个位置。

你可能已经猜到了，这个项目利用微软 Azure 的[物联网中心](https://azure.microsoft.com/en-us/services/iot-hub/)来连接、监控和管理所有的物联网资产。

### 🤖机器学习和人工智能

为了让这个仪表盘通过机器学习变得人工智能，我们使用了微软 Azure 的许多不同服务。

有一个巨大的数据集被馈送到 [ML Studio](https://studio.azureml.net/) 中，它随后帮助我们预测不同的决策，如空间管理、IT 事件的低注册趋势，以及这些事情为什么以及何时发生等问题。

虽然机器学习部分超出了本文的范围，但我仍然计划通过 Azure 的[认知服务](https://azure.microsoft.com/en-us/services/cognitive-services/)接触一些令人敬畏的人工智能。

### 🕰直播&实时

这个定制仪表板最重要的一个方面是它是实时的。这意味着，我需要一个托管的数据库，能够处理如此大量的数据，并且仍然保持高度可用。

但同时，这是出于管理的目的，不需要对 WordPress 站点有任何影响。对于这个仪表板来说，这是一个关键的系统设计决策。我的意思是，我们可以用这个定制的仪表板做各种各样的实验，但是它不应该对运行多站点 WordPress 实例的数据库/服务器有任何影响。

# 🎛

## 【蒙戈布】&蒙戈布地图集

对于这个定制的 WordPress 仪表板，我使用 [MongoDB Atlas](https://www.MongoDB.com/cloud/atlas) 作为 DBaaS(数据库即服务)。我非常高兴。当我第一次分享我将使用 MongoDB 时，许多开发人员都有顾虑。

[![MongoDB Gray Logo Fullcolor Rgb 01](img/b6d9615d9a333b4e8bfaacfa05b6f428.png)T2】](https://www.MongoDB.com/)

大多数问题都是关于为什么要通过添加另一个数据库来增加另一层复杂性。为什么不直接使用 WordPress 数据库呢？为了回答这些问题以及更多的问题，我准备了一份我为什么使用 [MongoDB Atlas](https://www.MongoDB.com/cloud/atlas) 的理由清单。

### ♨对 RDBMS 的厌恶

我个人不喜欢关系数据库。大多数时候，对我来说，它们是构建应用程序的绊脚石。我必须完全摆脱我正在构建的应用程序，考虑我未来的数据库，并设计一个好的模式，这对于我的开发工作流来说总是以糟糕的练习告终。这充其量是反直觉的——至少对我来说是这样。

### 💸硬盘便宜| CPU/RAM 不便宜

旧的数据库主要是为了节省磁盘空间而设计的。这导致了过多的问题，如标准化、索引，并使分片、自动缩放和复制更加困难。

如今，磁盘空间非常便宜。另一方面，CPU/RAM 不是，如果您最终做出了错误的选择，那么您的系统管理成本会迅速飙升。

就像你想创建一个定制的仪表板，但是你的系统设计架构师让你付出了两个系统管理员的代价。同样，我的客户想要一个托管解决方案，而不需要雇用 IT/DevOps 团队——至少对于一个实验性的定制仪表板来说是这样。

### 🍀MongoDB 的 Pro

*   **无模式**。灵活的双赢模式。我不需要改变任何东西，我的常规应用程序开发工作流，创建一个基于 Node.js 的应用程序，在那里我可以操作 JSON 类型的数据，我只需将它输入 MongoDB，它就可以工作了。
*   **工作流一致性**。按照我的自定义仪表板的表示方式创建文档。销售、视频、讲座、评论、评论、注册等。所有这些在前端和后端都有相似的数据表示，甚至在数据库中也是如此。我通过中间件管理第三方数据。*这种一致性转化为干净的代码。*
*   **易于横向扩展**。它通过使用副本集来扩展读取。通过使用分片(自动平衡)来缩放写入。只要启动另一台机器，你就可以走了。最重要的是，与通过 RDBMS 进行垂直扩展不同，MongoDB 允许您以不同的一致性水平进行水平扩展。这是一个很大的优势。➕
*   **成本**。当然，这取决于是哪种 RDBMS，但是 MongoDB 是免费的，可以在 Linux 上运行，非常适合在更便宜的商品套件上运行。

### 🍃为什么 [MongoDB 图集](https://www.MongoDB.com/cloud/atlas)？

好了，现在我知道 MongoDB 是正确的数据库选择，有这么多不同的选项来托管您的数据库。我可以通过 DigitalOcean 在我的 Linux 机器上进行自托管，使用 AWS/Azure 这样的云提供商，或者选择特定于 MongoDB 的 DBaaS 服务。

[![9 MongoDB Atlas](img/91d19ef588b2f980ca4cbbe4492bb48f.png)T2】](https://www.MongoDB.com/cloud/atlas)

但是我想要一个快速、安全和可管理的 MongoDB 解决方案，我可以随着我们在这个定制的 WordPress 仪表板中附加的模块数量的增长而轻松扩展。那是 [MongoDB 图集](https://www.MongoDB.com/cloud/atlas)。

> MongoDB Atlas 是一个云托管的 MongoDB 服务，由构建数据库的同一个团队设计和运行。您猜怎么着，我相信他们遵循了最佳操作实践，因为他们是构建 MongoDB 的第一人。

我希望这个定制仪表板是自我管理的，无服务器的，使用 MongoDB Atlas 使我不必担心软件修补、备份和新数据库更新的可靠配置设置。这又是一大优势。➕

此外，MongoDB Atlas 支持跨平台、跨地区和跨不同云提供商的事实使它成为一个更好的选择。我认为每个集群都有两个副本集，随时可以扩展。

### 🔋MongoDB 罗盘

既然我们要使用 MongoDB，那么最好有一个工具，通过它我们可以探索我们的数据库，查看更改，调试等等。为此，MongoDB 再次率先推出了名为 [MongoDB Compass](https://www.MongoDB.com/products/compass) 的产品。看一看。

[![8 MongoDB Compass](img/e19f14f8e7788efd9dbd29f8f8d27159.png)](https://www.MongoDB.com/products/compass) 我建议你去下载 [MongoDB 指南针](https://www.MongoDB.com/products/compass)。

这是可视化 MongoDB 数据库的最佳工具。这里有一组功能:

*   **可视化和探索:**看看你的数据库，了解事情的进展，甚至可视化地图/坐标等东西。
*   **插入、修改和删除:**您还可以在 MongoDB compass 中对您的 DB 执行 CRUD 操作。使测试更容易。
*   **调试和优化:**最后，分析您的数据，进行调试，甚至在一个出色的数据库 GUI 中找出性能问题。如果您使用 MongoDB，这个工具是必备的。
*   可扩展:最好的部分是你可以构建自己的插件来扩展 MongoDB Compass。这是关于[构建自己的指南针插件](https://docs.MongoDB.com/compass/master/plugins/creating-compass-plugins/?_ga=2.265049868.203146249.1526899658-414138157.1518239990)的文档。
*   企业版 : MongoDB Compass 有几种版本:社区版(免费)和企业版(授权)——企业版可以让你可视化 DB 模式。

## ✅入门 MongoDB 图集

让我们开始构建一个简单的模块，它是我正在构建的自定义 WordPress 仪表盘的一部分。在本模块中，我们将收集所有与销售相关的数据。为此，我们需要一个 MongoDB 实例，当然我们在这里使用的是 MongoDB Atlas。

### 第一步:进入 [MongoDB 图集→](https://www.MongoDB.com/cloud/atlas)

去 [MongoDB Atlas](https://www.MongoDB.com/cloud/atlas) 网站注册一个完全免费的 MongoDB 实例，托管在 AWS 上，共享内存和 512 Mb 存储空间。点击`Get Started Free`按钮。

[![1 MongoDB Atlas](img/447def83999a6f25d88ba64cf8fa08a7.png)T2】](https://www.MongoDB.com/cloud/atlas)

### 第二步:在 [MongoDB 图集报名→](https://www.MongoDB.com/cloud/atlas)

现在继续用你的电子邮件 ID 注册并填写详细信息。令人惊讶的是，你可以注册并使用一个免费的 MongoDB Atlas 托管数据库实例，他们甚至不需要你为此添加信用卡。

[![2 MongoDB Atlas Signup](img/69269df1ef013a9ba2782041ec3e5da9.png)T2】](https://www.MongoDB.com/cloud/atlas)

### 第三步:创建集群

现在，您将被重定向到一个页面，其中包含关于您将要创建的新 MongoDB 集群的大量信息。我建议您查看一下这些信息，然后点击底部的`Create Cluster`按钮，就像下面的截图一样。

[![3 MongoDB Atlas Create Cluster](img/f2b46095e932614708d8bc8d80ac35fb.png)T2】](https://www.MongoDB.com/cloud/atlas)

### 第 4 步:创建数据库用户/通行证

这需要一分钟的时间，您的数据库将被创建。一旦发生这种情况，前往`Security` > `MongoDB Users`并点击右边的`+ ADD NEW USER`按钮，为您的数据库创建一个新用户。为了这篇介绍性文章，让我们保持所有其他设置为默认设置。

我将用户/通行证设置为`usermongo`,但你知道得更清楚。

[![GIF for MongoDB Atlas, Microsoft Azure, & Serverless Functions](img/64e7cb6feb6357a97b2f52c8d4d4f425.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o5F7VXB4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://ddwva799xzrph.cloudfront.net/items/1K393D0r3k3b3u193837/Screen%2520Recording%25202018-05-21%2520at%252007.35%2520PM.gif)

### 第 5 步:将 IP 添加到白名单中进行访问

为了能够访问您的 MongoDB Atlas 数据库，您需要使用托管您的应用程序的服务器的 IP 来设置 IP 白名单。身份验证超出了我在这里讨论的范围，所以为了演示的目的，让我们只允许每个人(这实际上是生产中的一个坏习惯)。

所以，再一次，把头转向`Security` > `IP Whitelist`，点击右边的`+ ADD IP ADDRESS`按钮，最后点击`ALLOW ACCESS FROM ANYWHERE`按钮允许匿名访问。

[![GIF for MongoDB Atlas, Microsoft Azure, & Serverless Functions](img/3b6a0cc1d6cd880ece877432307612d9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--k-0ZcGy1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://ddwva799xzrph.cloudfront.net/items/1k3y3A0A0p0D2M3M3T1a/Screen%2520Recording%25202018-05-21%2520at%252007.45%2520PM.gif)

### 第六步:通过 MongoDB Compass 连接

既然我们的数据库的 IP 访问和用户已经创建，我们可以选择连接字符串并使用它通过 MongoDB Compass 应用程序连接到我们的数据库。

转到`Connect`然后选择`Connect with MongoDB Compass`并下载指南针，如果你现在还没有，复制 URI 连接字符串。最后，打开指南针，它应该能够检测到剪贴板中的连接字符串，允许它连接到您的数据库。

您可以可视化您的数据库，分析其性能，甚至运行完整的 CRUD 操作。厉害！💯

[![GIF for MongoDB Atlas, Microsoft Azure, & Serverless Functions](img/225c59e4ab278a8d9c82ff497296d69c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pRKZ5mL9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://ddwva799xzrph.cloudfront.net/items/3t3P2440132S0B0B1l26/Screen%2520Recording%25202018-05-21%2520at%252008.08%2520PM.gif)

现在我们已经创建了一个 MongoDB Atlas，将它与 MongoDB Compass 连接起来，接下来我们可以开始构建 Node.js 应用程序了。

# ⓦ

## WordPress REST API — FTW！

这个基于 WordPress 的 Node.js 定制仪表板通过 [WordPress REST API](https://developer.wordpress.org/rest-api/) 与 WordPress 实例交互。由于这是一个 Node.js 应用程序，我使用了一个由[K·亚当·怀特](https://twitter.com/kadamwhite)编写的名为 [`wpapi`](https://www.npmjs.com/package/wpapi) 的令人敬畏的库。他还开发了一个基于 WordPress 应用程序的演示版 [`express`](https://www.npmjs.com/package/express) 。这就是我在构建这个定制仪表板时受到的启发。所以，你会在这里看到很多。

### 🚀基于 [`express`](https://www.npmjs.com/package/express) 的定制路由器

路由器设置有 [`express`](https://www.npmjs.com/package/express) 。这里有一个使用 WordPress 和 express 的基本错误处理程序和路由器模板。