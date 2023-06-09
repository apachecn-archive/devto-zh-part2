# 干净的 iOS 架构 pt . 6:VIPER——设计模式还是架构？

> 原文：<https://dev.to/essentialdeveloper/clean-ios-architecture-pt6-viper--design-pattern-or-architecture-1gog>

[https://www.youtube.com/embed/CkylrfKvf1A](https://www.youtube.com/embed/CkylrfKvf1A) [在 YouTube 上观看](https://www.youtube.com/watch?v=CkylrfKvf1A&list=PLyjgjmI1UzlSWtjAMPOt03L7InkCRlGzb)

今天我们将分析 VIPER iOS 架构，并决定我们是否可以将其称为软件架构或设计模式，就像我们在之前的视频中谈到的 MVC、MVVM 和 MVP 设计模式一样。

[VIPER 于 2014 年](https://mutualmobile.com/resources/meet-viper-fast-agile-non-lethal-ios-architecture-framework)发布，其架构的主要目标是可测试性和解决大规模视图控制器问题。后来，[在其中一期 objc.io 上发布了一篇更详细的文章](https://www.objc.io/issues/13-architecture/viper/)。

这篇详细的文章解释了使用 VIPER 的动机:

*   易测性
*   修复大量视图控制器问题
*   基于用例的应用程序设计
*   很好地适应平台框架(如 UIKit)
*   将功能构建为独立模块

那么我们可以认为 VIPER 是一种软件架构还是仅仅是一种设计模式呢？

VIPER 图定义了一个结构，它看起来像一个架构，但可能会创建一个有限的应用程序前景。尽管 VIPER 是作为一种解决“MVC”限制(其中组件只能被视为模型、视图或控制器)的架构而提出的，但 VIPER 可能也有同样的限制(其中组件只能被视为视图、交互器、呈现器、实体或路由器)。

实际上，作者说 VIPER“主要”分为这些类型，但根据需要可以有更少甚至更多的分离层。不幸的是，许多开发人员最终试图遵循“缩略词模板”(就像 MVC 设计模式一样)。

VIPER 可以解决海量的视图控制器问题，让你的代码更具可测试性，但是它没有解决“软件架构”问题。通过遵循 VIPER 模板，我们可能会得到干净的代码，但却是意大利面条式的架构！

例如，让我们看看[待办事项示例项目](https://github.com/mutualmobile/VIPER-SWIFT)的依赖关系图。

[![Todo List sample project class and module dependencies diagram.](img/95efb0bce4aa78df8032439902735d56.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TyyWnYhv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static1.squarespace.com/static/5891c5b8d1758ec68ef5dbc2/t/5b8d029d21c67c87037fea62/1535970073504/diagram-1.png%3Fformat%3D1000w)

[待办事项示例项目](https://github.com/mutualmobile/VIPER-SWIFT)类和模块依赖关系图。

如你所见，到处都有箭头指向。甚至跨越模块边界。依赖关系看起来失去了控制，因此对软件的更改可能会破坏多个模块(当然，还有多个测试)。

VIPER 是作为鲍勃大叔的干净架构的 iOS 实现而推出的。如果我们比较两者，他们有相似之处，特别是在他们的层名称。然而，仔细观察就会发现，至少通过分析 VIPER Todo List 示例，它们并不共享相同的结构和目标。在 Bob 叔叔的干净架构中，框架和驱动程序(例如 UIKit 和 CoreData)只是业务逻辑层的“细节”或“插件”。相反，在常见的 VIPER 实现中，框架最终位于应用程序的中心。(还有一些关键的数据流差异， [Paul Stringer 在本文](https://medium.com/stringer-s-theory/the-clean-er-architecture-for-ios-apps-96379577d9dd)中解释。)

[![VIPER Todo sample project “Onion Layers” diagram separate by features and dependencies with the frameworks at the center](img/ce68b68aa3b4802cf4b19f2224855e34.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1yZ_lGsx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static1.squarespace.com/static/5891c5b8d1758ec68ef5dbc2/t/5b8d02ff40ec9a2651e86d8c/1535968086482/diagram-3.png%3Fformat%3D1000w)

VIPER Todo 示例项目“洋葱层”图以框架为中心，通过特性和依赖关系进行分隔。

我们相信软件架构与你的职责关系不大，更多的是关于组件如何相互通信以及它们如何相互依赖。什么是耦合，为什么？什么是解耦，为什么解耦？什么是难以改变的，为什么？什么是容易改变的，为什么？数据是如何在各层之间流动的——数据是单向流动的吗？两个方向？多个方向？我们能支持短期和长期的业务目标吗？

不管我们是否遵循某种命名的架构，如 VIPER，我们的软件是由许多可以耦合或解耦的组件构成的。这个软件结构或组件结构就是我们的架构。

我们的代码库就像活的有机体，它们一直在变化。建筑也是如此。它是不断变化的。它可以正向或负向移动。

例如，模块化是一个很好的架构特性，但是并不是所有的架构都是模块化的。如果我们的系统没有模块化，并不意味着我们没有架构。然而，我们可能会很快发现，缺乏模块化会阻止我们快速前进，并阻止我们实现关键的业务指标。例如:

*   部署频率
*   估计准确度
*   变革的准备时间
*   平均恢复时间

在 Essential Developer，我们的主要驱动力是同理心、诚信和经济。商业指标是经济学的一部分。当我们做出系统架构这样的决策时，我们必须考虑经济因素。我们确实相信软件架构与产品成功有很强的相关性。产品寿命。产品可持续性。

根据我们的经验，如果小心使用，MVC、MVP、MVVM 和 VIPER 都是很好的设计模式。然而，它们不足以定义我们系统的“软件架构”。

欲了解更多信息，请访问[干净的 iOS 架构播放列表](https://www.youtube.com/watch?v=PnqJiJVc0P8&list=PLyjgjmI1UzlSWtjAMPOt03L7InkCRlGzb)。

现在就订阅我们的 Youtube 频道，每周都能看到**的免费新剧集。**

* * *

最初发表于[www.essentialdeveloper.com](https://www.essentialdeveloper.com/articles/clean-ios-architecture-pt-6-viper-design-pattern-or-architecture)。

我们一直在帮助敬业的开发人员从低薪职位晋升到高层职位——有时只需几周时间！为了做到这一点，我们不断开展并分享关于如何提高你的技能的免费市场研究，同时牢记**同理心、诚信和经济学**。如果你想在事业上更上一层楼，[现在就免费获取我们的最新研究](https://www.essentialdeveloper.com/courses/career-and-market-strategy-for-professional-ios-developers)。

## 我们来连线

如果你喜欢这篇文章，请访问我们在[https://essentialdeveloper.com](https://essentialdeveloper.com)的网站，获得更多像这样的深度定制内容。

关注我们:[YouTube](https://youtube.com/essentialdeveloper)[Twitter](https://twitter.com/essentialdevcom)[脸书](https://facebook.com/essentialdeveloper)[GitHub](https://github.com/essentialdevelopercom)