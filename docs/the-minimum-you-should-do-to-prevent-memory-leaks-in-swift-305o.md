# 在 Swift 中防止内存泄漏的最低要求

> 原文：<https://dev.to/essentialdeveloper/the-minimum-you-should-do-to-prevent-memory-leaks-in-swift-305o>

[https://www.youtube.com/embed/Gg-2nZYRd2g](https://www.youtube.com/embed/Gg-2nZYRd2g) [在 YouTube 上观看](https://www.youtube.com/watch?v=Gg-2nZYRd2g&list=PLyjgjmI1UzlTtsImzQ4cT8bQ3_RXtkqLg)

在这一集里，我们继续[干净的 Swift 测试讨论](https://www.essentialdeveloper.com/articles/xctest-swift-setup-teardown-vs-factory-methods)，并且我们演示了一种通过自动化测试来防止内存泄漏的方法。

有一大类与内存泄漏相关的错误、安全威胁和用户体验问题(例如崩溃和高内存消耗)。虽然这是一个重要的话题，但为什么大多数应用程序(包括苹果)都无法防止内存泄漏呢？

**回答:许多开发人员依赖容易出错的手工检查。**

在早期阶段，手动检查似乎是一个不错的选择。然而，代码库扩大了，需求改变了，新的开发人员加入了团队，压力增加了，最后期限临近了。在这些紧张的条件下，人工检查很有可能因人为错误而失败。此外，可能的组合检查的数量太多了，而且呈指数增长，这使得人类不可能检查所有的逻辑分支。我们能避免这种情况吗？

**回答:是的，用** [**好的架构**](https://www.essentialdeveloper.com/articles/clean-ios-architecture-part-2-good-architecture-traits) **和** [**自动化测试**](https://www.essentialdeveloper.com/articles/ios-automation-testing-tools-vs-economics-is-it-worth-the-cost) **！**

现在就订阅我们的 Youtube 频道，每周都能看到**的免费新剧集**。

* * *

我们一直在帮助敬业的开发人员从低薪职位晋升到高层职位——有时只需几周时间！为了做到这一点，我们不断开展并分享关于如何提高你的技能的免费市场研究，同时牢记**同理心、诚信和经济学**。如果你想在事业上更上一层楼，[现在就免费获取我们的最新研究](https://www.essentialdeveloper.com/courses/career-and-market-strategy-for-professional-ios-developers)。

* * *

最初发表于[www.essentialdeveloper.com](https://www.essentialdeveloper.com/articles/the-minimum-you-should-do-to-prevent-memory-leaks-in-swift)。

## 我们来连线

如果你喜欢这篇文章，请访问我们在[https://essentialdeveloper.com](https://essentialdeveloper.com)的网站，获得更多像这样的深度定制内容。

关注我们:[YouTube](https://youtube.com/essentialdeveloper)[Twitter](https://twitter.com/essentialdevcom)[脸书](https://facebook.com/essentialdeveloper)[GitHub](https://github.com/essentialdevelopercom)