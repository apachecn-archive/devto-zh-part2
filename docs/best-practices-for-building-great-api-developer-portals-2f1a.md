# 构建优秀 API 开发人员门户的最佳实践

> 原文：<https://dev.to/yos/best-practices-for-building-great-api-developer-portals-2f1a>

[![](../Images/d0670f2c9776e6fa27e5938a3d748493.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OgJaZH1b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/mr5xBjP.png)

一个没有适当文档的 API 并不比没有 API 好。拥有一个优秀的开发者门户是 API 开发者体验的重要组成部分，有助于推动采用。

开发人员门户的用户体验、API 文档的完整性、您可以轻松搜索到满足其需求的正确解决方案，以及开发人员可以开始调用您的端点的速度，这些都是 API 产品成功的基础。

本指南记录了拥有一个优秀的 API 开发者门户的最佳实践。

> 查看 [apidocs.gallery](https://apidocs.gallery/) 来展示最好的 API 开发者门户！

## 伟大开发者门户的要素

*   [指南和教程](#guides-and-tutorials)
*   [客户端软件开发套件](#client-sdks)
*   [API 引用](#api-reference)
*   [API 浏览器](#api-explorer)
*   [用例](#use-cases)
*   [展示区](#showcase)
*   [变更日志](#changelog)
*   [支持](#support)
*   [状态](#status)

## 指南和教程

你的开发者门户应该有一个指南部分，用简单的英语描述你的 API 是做什么的。分享优势，而不是功能。用清晰的术语解释你的 API 的功能可以让决策者评估你的 API 是否满足他们的确切需求和用例。

[![](../Images/71118608ef91a7bd3b08d9786807d5c2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Fqa8Glw3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://apidocs.gallery/images/asana/1.png)

教程更详细:一步一步的说明和解释各种 API 资源是什么，以及如何操作它们来实现某个用例。教程应该力求清晰、简洁，并且各个步骤之间的间隔均匀。

[![](../Images/9b3fb7a5867dbb376eca7fbd78ccf82d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hetTV1WD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://apidocs.gallery/images/trello/1.png)

[![](../Images/0bc071ed412a5eb440a34fa1181edf3d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EDUdigr9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://apidocs.gallery/images/twilio/1.png)

指南也可以深入到高级主题。

[![](../Images/cd0472903a217cdd52d6813a2d6ca84b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BGWkihlx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://apidocs.gallery/images/digitalocean/1.png)

使用大量代码示例来鼓励复制/粘贴。

> 通过使用复制按钮将代码块的内容保存到用户的剪贴板上，可以使这个过程变得更加容易。

示例:

*   [Asana 开发者文档](https://asana.com/developers/documentation/examples-tutorials/overview)
*   [Trello API 入门指南](https://trello.readme.io/docs/get-started)
*   [数字海洋开发者指南](https://developers.digitalocean.com/guides/)

## 客户端 SDK

虽然为许多编程语言编写和维护健壮的、惯用的客户端库并不容易，但是它们对于想要使用您的 API 的大多数开发人员来说非常有帮助。拥有客户端 SDK 极大地减少了集成 API 所需的工作量，尤其是对于不熟悉特定编程语言的开发人员。

[![](../Images/0d0cd5226a7eec02498a5ad70a74c30e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sjZcGdPN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/IpNcxrQ.png)

提供开发者可以下载并立即开始使用的 SDK。

示例:

*   [条带 API 库](https://stripe.com/docs/libraries)
*   [数字海洋 API 库](https://developers.digitalocean.com/libraries/)

## API 引用

API 参考部分记录了每个端点:HTTP 路由、输入请求、输出响应，以及每个域对象及其属性的含义。

[![](../Images/500523f53da55eb93273ada9f357166c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6XajeXg0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://apidocs.gallery/images/clearbit/1.png)

您应该致力于表示客户端对多种客户端技术的 API 的使用，包括 cURL 和 web 和本地客户端开发人员使用的最流行的编程语言。这意味着展示多种编程语言的示例客户端代码:C#、Java、JavaScript、Go、Objective-C、PHP、Python、Ruby、Swift 等。

[![The Mailgun API docs allows you to pick the programming language code samples are shown in](../Images/97d7c4a9a55b5b95b0699fbc21c84dc7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9uTTjp6e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/s28y6Iw.png)

包含代码示例的目的是帮助弥合 API 调用的抽象概念和开发人员熟悉的语言的具体用法之间的差距。目标是让你的文档更容易被不同经验水平的开发者理解。

示例:

*   [Clearbit API 参考](https://clearbit.com/docs)
*   [格子 API 引用](https://plaid.com/docs/api/)

## API 资源管理器

API playground 是一个交互式 API 控制台，开发人员可以从它发出调用，并查看来自每个 API 端点的示例响应。

[![](../Images/8720347017afa69e3e1d8f7283bdffca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wpkrymHS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://apidocs.gallery/images/trello/2.png)

开发人员通过实践来学习。API 资源管理器提供实时测试设施来支持探索和发现。这允许开发人员立即投入并直接与系统交互，而不是阅读一页又一页的文档。

示例:

*   [Readme.io API Explorer](https://readmeio.github.io/api-explorer/)
*   [Swagger UI](https://swagger.io/swagger-ui/)

## 用例

当开发人员来到开发人员门户时，他们脑海中最先想到的问题可能是“为什么我关心这个 API？”你需要让他们一开始就知道你的 API 能做什么。

[![](../Images/0cdaee9578ba6bd429a51d7c585afdb3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1GiV5RVk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/t63U487.png)

展示用例将让开发人员的大脑开始思考各种可能性，并让他们对使用您的 API 可以构建什么感到兴奋。

示例:

*   [优步 API 用例](https://developer.uber.com/solutions)和[解决方案](https://developer.uber.com/solutions)
*   [Clearbit 用例](https://clearbit.com/marketing)

## 展柜

突出你的合作伙伴的一些工作，用额外的营销来传播消息，鼓励更多的开发者集成你的 API。

[![](../Images/d3ea1a31b0cfb50f05f14a1850003591.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---ftrNa5e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://apidocs.gallery/images/spotify/2.png)

示例:

*   [Spotify 开发者展示区](https://beta.developer.spotify.com/community/showcase/)
*   [优步 API 开发者展示区](https://developer.uber.com/showcase)

## 变更日志

不要让你的用户蒙在鼓里！让开发人员了解 API 的显著变化:突破性的变化、新的端点、改进等等。

[![](../Images/c0415fffbc639cf78ea8a9b72d726ac7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--U8aUBSnD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/h6LmLo5.png)

建立一个 RSS 提要或邮件列表，这样开发人员就可以得到变更通知。

示例:

*   [Github API 变更日志](https://developer.github.com/changes/)
*   [数字海洋变更日志](https://developers.digitalocean.com/documentation/changelog/)

## 支持

维护一个包含*常见问题解答*的专用支持网站，以及一个供用户发送问题的活跃渠道。

[![](../Images/9d73278ca32338f20c8cc00879c82ea9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AZLuG21V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://apidocs.gallery/images/twilio/3.png)

您还可以建立一个社区问答网站，让您的用户互相回答问题。

[![](../Images/8cfa36f4ef7b32fd66bacdc79f1934ed.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--G840FagZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/u0hVKmF.png)

示例:

*   [Twilio 支持](https://support.twilio.com/hc/en-us)
*   [优步开发者支持](https://developer.uber.com/support)

## 状态

公共运行时间监控部分激发了开发人员对 API 的可靠性和可用性的信心。

[![](../Images/7d69c9b3b2fdaac8b2c640f134acacb9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tLi4v7gi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/j9VNu3Z.png)

此页面显示:API 的各个部分的操作状态。此外，您可以显示事故历史记录部分，其中包含任何以前停机时间的事后分析。

示例:

*   [体式状态页面](https://trust.asana.com/)
*   [Github 状态页面](https://status.github.com/messages)

# 在关闭

拥有一个优秀的开发人员门户是 API 开发人员体验的关键部分，有助于推动采用。

感谢阅读！