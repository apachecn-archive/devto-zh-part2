# 我从。Net 到 Salesforce。我的想法和目前面临的挑战。

> 原文：<https://dev.to/chris_bertrand/my-transition-from-net-to-salesforce-my-thoughts-and-the-challenges-so-far-2pcg>

[![Image result for microsoft logo](img/9e385a15ea488d64804bfddb879e5f6f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iDc1w-yL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blogs.microsoft.com/uploads/2012/08/8867.Microsoft_5F00_Logo_2D00_for_2D00_screen.jpg)

在微软的生态系统中呆了近十年，我决定是时候改变了。

我开发了一系列使用微软堆栈的网络应用和工具，使用了一些奇妙的、遥远的、现在已经过时的~~废弃的~~平台/技术，例如:

*   Web/Windows 窗体
*   WPF
*   银光
*   SQL Server
*   Web API
*   手动音量调节
*   VSTO
*   Powershell
*   TFS/ VSTS

这些都是可供开发人员使用的微软工具套件中的重要模块。当转向一项新技术时，您会自动关注以前技术中的应用知识是如何转化过来的，或者类似的框架是如何存在于您的新生态系统中的。

然而，情况并非总是如此，某些事物没有直接的映射，某些任务和问题没有直接的投射。事实上，大多数人都不知道。

数据库不再是一个独立的实体，安全性和基础设施已经大大减少，默认样式和用户交互已经定义。很多复杂性都被去除了，所以除了开始编码你的解决方案之外，真的没有那么多事情要做。是的，你的大部分控制被移除了，但是你通常必须经历的大量努力也消失了。SQL Server 不鼓励使用触发器，而 Salesforce 系统鼓励使用触发器，我们不要开始调试了！或者是缺少它。

> 那么，你如何改变你的大脑，让它以不同的方式思考呢？

你真的想这么做吗？或者你用这些知识为你的大脑创造一条新的路径。

[![Related image](img/f102ba1583bccbdfceddf418c2424484.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uBOE48OG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.soulseeds.com/wp-content/uploads/2012/06/make-your-own-path.jpg)

我认为在这个问题上还有很多要讨论的，但本质上是的，正如标题所示，我走的路是 Salesforce！

为什么我听到你问？目前有很多其他语言和框架非常有趣。JavaScript 框架风靡一时，Angular、React 和 Vue 正在全面流行，而 AWS/Azure 和 NoSQL 数据库云的崛起似乎是自然的趋势！微服务和事件驱动系统(如 Apache Kafka)的优势也非常酷，也绝对是未来。

### 我为什么选择 Salesforce？

[![Image result for salesforce.com logo](img/1763ab81a8ce6c7fe10a2e14460efc84.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LBGkCx8u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/en/thumb/8/83/Salesforce_logo.svg/1200px-Salesforce_logo.svg.png)

Salesforce 比大多数人想象的要复杂得多。Saleforce 是云，每个实例都托管在云中！AWS 是 Salesforce 的一个重大成功案例:

**https://www.salesforce.com/customer-success-stories/aws/**

Salesforce 也拥有 Heroku！

**[https://www . sales force . com/uk/products/platform/products/heroku/](https://www.salesforce.com/uk/products/platform/products/heroku/)**

你知道他们也有自己的人工智能框架吗？

**https://www.salesforce.com/uk/products/einstein/overview/**

我有没有提到，Salesforce 是目前世界上最适合工作的公司？2018!

**[https://www . sales force . com/blog/2018/02/sales force-fortune-100-best-companies-to-work . html](https://www.salesforce.com/blog/2018/02/salesforce-fortune-100-best-companies-to-work.html)T3】**

现在，我不直接为 Salesforce 工作，但了解他们的工具、方法和精神特质是我对他们使用他们的技术感兴趣的主要原因。我认为我在这里提到的所有事情都是选项，Salesforce 通过大量增强和更新不断改进他们的系统。

肯定有一些积极的，但也有一些消极的！

> 您相信基本的 Switch 语句刚刚(2018 年 5 月)被纳入 Salesforce 语言吗？

**[https://developer . sales force . com/blogs/2018/05/summer 18-rethink-trigger-logic-with-apex-switch . html](https://developer.salesforce.com/blogs/2018/05/summer18-rethink-trigger-logic-with-apex-switch.html)**

有些人可能无法克服这样的疏忽，但是看看我们可以得到的东西的范围，可以理解的是，有些东西可能并不总是在他们的清单的顶部！

### 开始使用。

对于任何新技术，你需要做的第一件事就是学会如何使用它。这通常需要在入睡前阅读无数的博客文章、技术文档和尽可能多的视频。

然后你需要安装软件/库/等等来让产品工作，然后试着做几个例子。

Salesforce 的入门非常简单。由于产品在云中，您只需请求一个环境，它就会为您构建。然后你可以直接在这上面工作。对于入门来说，这个框架出奇的快速和简单。是的，需要 IDE 和第三方工具/源代码控制来充分利用这个项目，但是你可以直接在浏览器中做任何你需要的事情。

另一个巨大的帮助是 Salesforce 实施的免费培训工具，它可以引导您通过这个工具可以做的事情的雷区。这让我想到了小道消息。

### 小道的起点

[![Image result for trailhead logo](img/52fa0c2245a598e972fb6d2c9d69053c.png)T2】](https://trailhead.salesforce.com/en/home)

Trailhead 为学习过程增加了游戏化。效果出奇的好。到目前为止，我已经获得了 37 个徽章和 48，037 个积分。这些都是通过完成路径获得的，路径包括模块，有选择题或动手练习。所有这些的真正好处是，Salesforce 通过直接在您的环境中运行单元测试来检查您的完整实现。因此，尽管读完某些部分可能会花更长的时间，但这比阅读或观看要有效得多。

## 下一步是什么？

所以是的，到目前为止还不错。我现在开始研究这个框架的本质，包括部署如何工作，以及如何最好地将更新的技术整合到这个庞大的 CRM 庞然大物中。我想知道未来几个月我的想法和感觉会有什么变化，Salesforce 是否会继续增长，以及 Lightning 和 Einstein 等新技术将如何塑造产品。

* * *

你现在在 Salesforce 工作吗？你从什么语言转换过来的？你的转变如何？

如果你有任何有用的建议要分享，我很乐意阅读，所以请在下面链接。