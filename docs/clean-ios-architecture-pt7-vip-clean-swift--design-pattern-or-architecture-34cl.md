# Clean iOS Architecture pt . 7:VIP(Clean Swift)——设计模式还是架构？

> 原文：<https://dev.to/essentialdeveloper/clean-ios-architecture-pt7-vip-clean-swift--design-pattern-or-architecture-34cl>

[https://www.youtube.com/embed/AnUcZUMGVBI](https://www.youtube.com/embed/AnUcZUMGVBI) [在 YouTube 上观看](https://www.youtube.com/watch?v=AnUcZUMGVBI?list=PLyjgjmI1UzlSWtjAMPOt03L7InkCRlGzb)

今天我们将分析 VIP (Clean Swift)架构。而且，正如我们在之前的视频中对 [VIPER](https://dev.to/essentialdeveloper/clean-ios-architecture-pt6-viper--design-pattern-or-architecture-3f1f-temp-slug-2599990) 、 [MVC、MVVM 和 MVP](https://dev.to/essentialdeveloper/clean-ios-architecture-pt5-mvc-mvvm-and-mvp-ui-design-patterns-5ha5-temp-slug-7121127) 所做的那样，我们将决定我们是否可以称 VIP 为软件架构或设计模式。

干净的 Swift 架构，或者也叫做“VIP”，是由[clean-swift.com](https://clean-swift.com)介绍给世界的，就像 VIPER 和其他模式一样，该架构的主要目标是可测试性和解决大规模视图控制器问题。

VIP 这个名字可能会和 VIPER 混淆，有趣的是 VIPER [的创造者几乎称它为 VIP](https://mutualmobile.com/resources/meet-viper-fast-agile-non-lethal-ios-architecture-framework) ，但决定放弃这个名字，因为它可能代表“非常重要的架构”，VIPER 的创造者认为这是贬义的。

VIP 与 VIPER 非常相似，因为两者都源于鲍勃叔叔的干净建筑理念。

那么我们可以把 VIP 看作一种软件架构或者仅仅是一种设计模式吗？

VIP 图描述了它的主要结构，这解释了它的缩写定义:view controller/Interactor/Presenter 关系，或者他们称之为:VIP 循环。

[![The unidirectional VIP cycle.](../Images/80e33f8fefc397c32e63ba55b94acc2a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LveWSv7E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static1.squarespace.com/static/5891c5b8d1758ec68ef5dbc2/t/5b97ac750ebbe8b81e090c9e/1536666750484/the-vip-cycle.png%3Fformat%3D1000w)

单向 VIP 循环。

VIP 周期不同于我们之前视频中描述的 VIPER 关系模型。在 VIPER 中，交互者和演示者之间以及视图和演示者之间的通信是双向的。

[![The bidirectional VIPER cycle.](../Images/e406ad0ab23b407c1d851825e88cdbd4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cPJapK_m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static1.squarespace.com/static/5891c5b8d1758ec68ef5dbc2/t/5b97aca5cd8366c14a4dd945/1536666797140/viper.png%3Fformat%3D1000w)

双向毒蛇循环。

相反，VIP 遵循单向方法，其中 ViewController 与交互器对话，交互器与其合作者一起运行业务逻辑并将输出传递给 Presenter，Presenter 格式化交互器输出并将响应(或视图模型)提供给 ViewController，因此它可以呈现其视图。

这个单向通信模型定义了一个架构吗？另外，这三个组件(视图控制器、交互器和表示器)是否足以描述应用程序架构？

它可能看起来像一个架构，但是 VIP 循环是如此有限的应用程序前景，以至于我们认为它是一个设计模式。这种设计模式已经有了一个名字:MVP。不管我们叫它什么，MVP 设计模式不是一个软件架构。

然而，VIP 或“Clean Swift”不仅仅包括视图控制器、交互器和演示器，还包括数据模型、路由器和工作器。

像 VIPER 一样，Clean Swift 作者描述 VIP 可以根据需要有更少甚至更多的分离层。但是核心必须遵循 MVP 或者 VIP 循环！因此，VIP 循环听起来更像是一种组织设计模式，可以解决大量的视图控制器问题，并使您的代码更具可测试性，但它并没有描述大的图景或“软件架构”

VIP 或 Clean Swift，就像 VIPER 和其他模式一样，试图解决大规模视图控制器和可测试性等类依赖性问题，而不是模块化等模块依赖性问题。有了所描述的干净的 Swift 组件，我们可能最终得到可测试的代码，但却是意大利面条式的架构。

VIP 还鼓励使用模板来“促进”其实施。拥有模板可能非常方便，但同时，它可能会创建一个有限的思考框架。我们不相信有一种设计模式或“模板”可以解决所有问题，并且可以无限扩展。我们相信，不要试图将每个问题都放入一个模板中，每个软件架构都必须被精心设计以解决系统挑战。例如，一些系统会从事件驱动(生产者/消费者)流模型中获益更多，而其他系统则不会。

让我们来看看 [Clean Swift 示例项目(CleanStore)](https://github.com/Clean-Swift/CleanStore) 依赖关系图。

[![CleanStore sample project class and module dependencies diagram.](../Images/d43f64ac3b0def3e25a11996db74b4d2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yYrLiQgw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static1.squarespace.com/static/5891c5b8d1758ec68ef5dbc2/t/5b97ad101ae6cfbcdbd675a7/1536666904060/diagram-1.png%3Fformat%3D1000w)

[CleanStore 示例项目](https://github.com/Clean-Swift/CleanStore)类和模块依赖关系图。

正如你所看到的，到处都有箭头，跨越模块边界，所以对软件的更改可能会破坏多个模块(当然，还有许多测试...).

在 Clean Swift 示例应用程序中，应用程序在场景(或模块)中是分离的。有列出订单场景、创建订单场景和显示订单场景。对模块依赖关系的更高层次的观察表明，场景之间以及与其他系统组件之间是高度耦合的。

[![High-level modules dependencies diagram shows a highly coupled architecture.](../Images/b1466ab5a20ba6bdf1f671324c77ced4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DImjpUR2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static1.squarespace.com/static/5891c5b8d1758ec68ef5dbc2/t/5b97ad3a40ec9af58bd2391d/1536666949774/diagram-2.png%3Fformat%3D1000w)

高级模块依赖关系图显示了一个高度耦合的架构。

查看应用程序架构的另一种方式是以循环的形式检查其模块，这样我们可以看到模块之间的依赖关系。越靠近中心，模块越抽象，越独立。

[![High-level modules dependencies diagram shows a highly coupled, monolithic architecture.](../Images/efbfac508a96b9e30ca7410d69fbe3ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wu8ru--l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static1.squarespace.com/static/5891c5b8d1758ec68ef5dbc2/t/5b97ad6e575d1f202ba3b2b9/1536667007880/diagram-3.png%3Fformat%3D1000w)

高级模块依赖关系图显示了一个高度耦合的整体架构。

与鲍勃大叔的干净架构不同，我们可以在核心环中看到服务、框架和驱动程序。此外，在每个场景中，一些交互器可能包含业务逻辑，它们依赖于框架和其他服务。在整个代码库中，我们甚至可以注意到核心模型被用于所有抽象层次(UI/表示/业务逻辑/路由/服务/工人),这在 Bob 叔叔的干净架构中是不允许的。

VIP 示例应用程序架构是一个高度耦合的整体架构。如果我们想创造一个有点可测试的整体，它可能会工作得很好。然而，我们可能会很快发现，缺乏模块化会阻止我们扩展团队，快速移动，并阻止我们实现关键的业务度量。例如:

*   部署频率
*   估计准确度
*   变革的准备时间
*   平均恢复时间

正如在前面的视频中所解释的，我们认为软件架构更多的是关于组件如何相互通信，它们如何相互依赖，以及高级开发人员对以下问题的共同理解:什么是重要的部分？什么是耦合？什么是脱钩？什么很难改变，什么很容易改变？数据是如何在各层之间流动的？为什么数据是单向流动的？两个方向？多个方向？我们能否支持业务的短期和长期目标？

我们想强调的是，我们的代码库就像活的有机体，它们一直在变化。建筑也是如此。它在不断发展，没有现成的模板。

在 Essential Developer，我们确实相信软件架构与产品成功、产品寿命和产品可持续性有很强的相关性。我们建议专业开发人员学习鲍勃大叔的干净架构、VIP、VIPER、MVC、MVVM、MVP 和其他模式，但不要试图复制和粘贴解决方案。记住:每个挑战都是不同的，没有灵丹妙药。

VIP、VIPER、MVC、MVVM、MVP 作为设计模式，可以引导你走向更结构化的组件。然而，它们并没有定义大图或者软件架构。所以，小心使用它们！

欲了解更多信息，请访问[干净的 iOS 架构播放列表](https://www.youtube.com/watch?v=PnqJiJVc0P8&list=PLyjgjmI1UzlSWtjAMPOt03L7InkCRlGzb)。

现在就订阅我们的 Youtube 频道，每周都能看到**的免费新剧集**。

* * *

最初发表于[www.essentialdeveloper.com](https://www.essentialdeveloper.com/articles/the-importance-of-discipline-for-ios-programmers)。

我们一直在帮助敬业的开发人员从低薪职位晋升到高层职位——有时只需几周时间！为了做到这一点，我们不断开展并分享关于如何提高你的技能的免费市场研究，同时牢记**同理心、诚信和经济学**。如果你想在事业上更上一层楼，[现在就免费获取我们的最新研究](https://www.essentialdeveloper.com/courses/career-and-market-strategy-for-professional-ios-developers)。

## 我们来连线

如果你喜欢这篇文章，请访问我们在[https://essentialdeveloper.com](https://essentialdeveloper.com)的网站，获得更多像这样的深度定制内容。

关注我们:[YouTube](https://youtube.com/essentialdeveloper)[Twitter](https://twitter.com/essentialdevcom)[脸书](https://facebook.com/essentialdeveloper)[GitHub](https://github.com/essentialdevelopercom)