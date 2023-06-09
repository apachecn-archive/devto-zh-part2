# 将 Swift 代码分解成模块——模块化设计简介

> 原文：<https://dev.to/essentialdeveloper/decomposing-swift-code-into-modules--intro-to-modular-design-2cdp>

[https://www.youtube.com/embed/E0_3uTdCVew](https://www.youtube.com/embed/E0_3uTdCVew) [在 YouTube 上观看](https://www.youtube.com/watch?E0_3uTdCVew?list=PLyjgjmI1UzlT2jbk9WeqNrZhZyKpPPaWq)

在这一集里，我们从之前创建的系统的[组件级视图中缩小，并展示如何将它组织成模块。](https://www.essentialdeveloper.com/articles/composable-code-can-be-simple-intro-to-dependency-diagrams-and-composition)

## 学习成果

*   理解图中组件和模块关系的表示

*   从系统的图示中了解系统的具体程度或模块化程度

*   理解每一个系统都需要被定制，而不是适合一个预先定义的模板

*   理解抽象的过程需要渐进地应用

## 描述

我们首先将图中的类型名替换为它们所属的模块名。例如，`FeedViewController`可以属于`UI`模块，因为它负责渲染视图和收集用户交互，`RemoteFeedLoader`属于`API`模块，因为它负责[与网络](https://www.essentialdeveloper.com/articles/careful-with-singleton-lookalikes-way-too-common)通信。

此外，我们演示了各种组织和模块化级别，最后，我们检查了著名的 monolith 的情况，其中所有组件都属于一个模块，并讨论了可能的权衡。

理解每种设计带来的权衡和风险在我们系统的开发过程中是一笔巨大的财富。作为开发人员和风险管理人员，我们不应该寻找一劳永逸的解决方案(这是不存在的)；相反，我们可以根据可用资源和要求定制解决方案。

现在就订阅我们的 Youtube 频道，每周都能看到**的免费新剧集**。

* * *

我们一直在帮助敬业的开发人员从低薪职位晋升到高层职位——有时只需几周时间！为了做到这一点，我们不断开展并分享关于如何提高你的技能的免费市场研究，同时牢记**同理心、诚信和经济学**。如果你想在事业上更上一层楼，[现在就免费获取我们的最新研究](https://www.essentialdeveloper.com/courses/career-and-market-strategy-for-professional-ios-developers)。

* * *

最初发表于[www.essentialdeveloper.com](https://www.essentialdeveloper.com/articles/decomposing-swift-code-into-modules-intro-to-modular-design)。

## 我们来连线

如果你喜欢这篇文章，请访问我们在[https://essentialdeveloper.com](https://essentialdeveloper.com)的网站，获得更多像这样的深度定制内容。

关注我们:[YouTube](https://youtube.com/essentialdeveloper)[Twitter](https://twitter.com/essentialdevcom)[脸书](https://facebook.com/essentialdeveloper)[GitHub](https://github.com/essentialdevelopercom)