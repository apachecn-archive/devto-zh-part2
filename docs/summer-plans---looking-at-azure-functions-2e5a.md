# 夏季计划-查看 Azure 函数

> 原文：<https://dev.to/raymondcamden/summer-plans---looking-at-azure-functions-2e5a>

我将在这篇文章中犯两个错误。呃，错误这个词可能有点过了。总的来说，我尽量避免制定“计划”,因为我几乎总是从承诺做比我最终完成的更多的事情开始。第二个错误是在我去度假之前制定了一个计划。由于个人原因，我在这里已经有点慢了，但我今天在想这个问题，我认为在给自己充足的时间的同时制定一些计划是很好的(嘿，“夏天”也可以指澳大利亚时间，对吗？)才能搞定。

说到无服务器，我开始花时间研究了 Apache OpenWhisk 和 IBM Cloud Functions。现在我在 Auth0，我的主要焦点是用[扩展](https://goextend.io/)，由 [Webtask.io](https://webtask.io/) 支持的无服务器可扩展性。我会把这些博文放到我们的扩展博客上:[https://goextend.io/blog](https://goextend.io/blog)。

所以在这个满是灰尘的旧博客上，我想我应该花点时间看看[Azure Functions]，微软的无服务器平台。我对 Azure 作为一个整体平台只有一些基本的了解，并且我已经使用过一些 Azure 函数，但是我想我应该对这个平台有一个更系统的了解。我的问题/兴趣/等。围绕以下几个方面:

*   开始，特别是新帐户，以及你从注册到“hello world”无服务器功能的速度。我也很好奇微软/Azure 在注册方面有什么要求。我已经有了几个帐户，其中一个可以通过公司的朋友访问资料，但我将使用一个新帐户进行测试。过了一段时间，IBM 做对了一件事，那就是允许你不用信用卡就可以注册。你必须在 30 天后提供一个，但是你至少可以在没有的情况下开始。如果更多的云提供商将这变成一种规范，我会*喜欢*，见鬼，简单地切换一个设置“如果我做的任何事情都要花钱，就关掉一切”。
*   如何轻松地使用我自己的编辑器和命令行界面？我已经在使用一个[编辑器](https://code.visualstudio.com/)，它有 Azure 函数的工具，但是我真的想“裸”体验它。Webtask 有一个*不可思议的*在线编辑器(好吧，我有偏见)，但总的来说，我很好奇“我有自己的工具”的方法将如何工作。
*   IBM Cloud Functions 做得很好的一件事是支持其他 IBM 云服务。我的意思是，在一天结束的时候，一个服务有一个 URL、用户名、密码，可能还有一个节点库，但是一个平台*可以*做更多的事情，使服务在它自己的平台内更容易使用。所以我的问题是 Azure 是否让在函数内使用他们自己的服务变得更容易了？
*   Azure 函数对非 HTTP 执行的支持程度如何？我知道我主要专注于为客户端应用程序构建 HTTP 端点(这也是 Webtask 做得非常好的事情，尽管我有偏见)，但我想看看将 Azure 函数连接到其他源有多容易，无论是基于时间的还是相关的。
*   调查报告。基本上-我的函数运行了多少次。平均执行时间是多少？一个函数会产生大量的错误吗？你能主动向我报告吗？

我想建造一些具体的东西，给我一些“练习”来完成:

*   重建[无服务器超人](https://twitter.com/serverlesssuper/) -一个基于 CRON 的 Twitter 搜索和发布模仿我建立的东西。(它看起来已经死了，所以我必须尽快把它拿回来。)
*   重建[随机漫画](https://twitter.com/randomcomicbook)谢天谢地*没死*。
*   构建一个简单的表单处理器。这是我关于用 Webtask 做这件事的[帖子](https://www.raymondcamden.com/2018/03/02/buidling-a-serverless-form-handler-with-webtask)。
*   建立一个图像处理器，哈哈，不，只是开玩笑，这已经做了一万次了。

所以是的-这就是我的想法。正如我所说的，我给了自己足够的时间来做这件事，正如我总是说的，我对建议持开放态度，所以请随时给我一些初步的反馈。