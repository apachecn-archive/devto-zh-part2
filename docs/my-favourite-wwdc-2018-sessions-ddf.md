# 我最喜欢的 WWDC 2018 届

> 原文：<https://dev.to/onmyway133/my-favourite-wwdc-2018-sessions-ddf>

今年，我没有买到去 WWDC 的彩票，也错过了主题演讲直播，因为那天我正在奥斯陆峡湾外的克里斯蒂安·拉迪奇航行。幸运的是，所有的视频很快就可以在[苹果开发者网站](https://developer.apple.com/videos/wwdc2018/)上看到，我们现在可以在 Chrome 上观看，或者在 macOS 上观看[非官方的 WWDC](https://github.com/insidegui/WWDC) 应用。我推荐 [WWDC](https://github.com/insidegui/WWDC) macOS 应用程序，因为它允许标记收藏夹和过滤，还提供将播放速度调整到 1.25 或 1.5 的能力，节省了我一些观看时间。

今年，WWDC 非常关注隐私、稳定性和速度，这些都是我所希望的，非常感谢苹果的工程师们实现了这一点，安装所谓的更加稳定的 iOS 12 的决定是真实的。作为一名 iOS 工程师，我喜欢更多地关注对我来说必要的东西，即 Swift 编程语言、Cocoa Touch 的新变化、Xcode 的增强和测试技巧。我也喜欢探索更多关于 [机器](https://medium.com/flawless-app-stories/machine-learning-in-ios-turi-create-and-coreml-5ddce0dc8e26) [学习](https://medium.com/flawless-app-stories/machine-learning-in-ios-azure-custom-vision-and-coreml-645e93f35eee)的[，所以我很高兴苹果随着 Turi Create 和 Create ML 的推出，在这项技术上投入了更多。](https://medium.com/flawless-app-stories/detecting-avengers-superheroes-in-your-ios-app-with-ibm-watson-and-coreml-fe38e493a4d1)

对我来说，API 经常被弃用，知道它们很好，但最重要的是投资于你的编程、调试和测试技能，你可以在许多其他平台上应用这些技能。

从去年[收藏夹](https://medium.com/@onmyway133/my-favourite-wwdc-2017-sessions-a777fa5aabaf)列表继续，以下是我最喜欢的会议和个人笔记。事情没有特定的顺序被列出。希望你觉得有用。

### [站台国情咨文](https://developer.apple.com/videos/play/wwdc2018/102/)

如果没有时间，那就只看这一期吧。平台联盟就像是开发者的基调，因为它强调了重要的变化。

[![](../Images/a154b00307fa43bacf19076dd6105c84.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--z89Ea6aP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2A5zkWPlmZ9oYfY9-O.jpg)

*   隐私:苹果确认了其在隐私和安全方面的承诺，还在 iOS 12 上引入了密码管理功能和自动填充功能。生成强密码、与第三密码管理集成以及从 SMS 消息快速填充 OTP 字段从未如此简单。[看门人](https://support.apple.com/en-us/HT202491)也得到了一些改进，并开始要求应用程序进行公证。

*   iOS 12:性能上的巨大提升，Siri 通过快捷方式支持、FaceTime 中的群组通话和分组通知变得更加智能。同样针对 emoji 粉丝，推出了 Memoji。

*   macOS 10.14 Mojave:更多黑暗模式。他们大多在黑暗模式下用 Xcode 进行演示，看起来很酷。今年，which 班纳给出了关于 iOS 和 macOS 跨平台应用的暗示，这在一定程度上与[杏仁糖](https://www.imore.com/marzipan)有关，这是一种允许 iOS 应用在 mac 上运行的方法。

*   Xcode 10:改进了代码编辑和源代码控制，改变了工具栏指示器。使用内存调试工具进行调试、LLDB 性能增强，特别是使用并行任务在 Swift 中完全重写的新构建系统是令人兴奋的消息。

*   Swift 4.2:如果你关注 Swift 回购，那么 [Swift 4.2](https://github.com/ole/whats-new-in-swift-4-2) 可能并不令人惊讶。还发布了 Swift 5 计划。

*   机器学习:再也没有比这更热的了。今年，我们看到在机器学习方面的巨大投资，包括 Create ML、Turi Create、自然语言框架、CoreML 2、视觉中的新检测功能。

*   ARKit 2、watchOS 5、tvOS 12、AppStore Connect 和 AppStore Connect APIs 是一些你不想错过的其他重要新闻。

### [Swift 新功能](https://developer.apple.com/videos/play/wwdc2018/401/)

[![](../Images/ff0013f2b299aa1c5c9defd81fb834a0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0-yHV8Zq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AJQCScM5dRcK9dX-_.jpg)

在本次会议期间，我建议您阅读非常简洁的 Swift 4.2 摘要中的[新增功能。除了在复杂性和运行时间方面的改进，Swift 4.2 还提供了一些新的特性:可迭代枚举、综合等价和散列、方便的洗牌函数、随机生成。对我来说，需要显式处理隐式 unwrapped optional 也是一个合理的改变。](https://www.hackingwithswift.com/articles/77/whats-new-in-swift-4-2)

### [可可触新品](https://developer.apple.com/videos/play/wwdc2018/202/)

[![](../Images/2b77cf529e9937296a6f53ca5e82a166.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pTnjm99V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2An1X4HddAZ-4ZHpaf.jpg)

这是对即将到来的 iOS 12 的所有变化的简要介绍，以及如何成为一个好的 iOS 公民的提示。了解哪些因素会影响滚动体验和预取技术、内存消耗和自动后备存储，以及如何充分利用 UIImage 和 UIImageView。 [AutoLayout](https://developer.apple.com/videos/play/wwdc2018/220/) 引擎的性能得到了很大的提高，所以它不会再打扰你了。对我来说，最令人满意的是在嵌套类型下重组所有的 UIKit 通知和类，这使得代码推理非常容易。

### [在 Xcode 中充分利用游乐场](https://developer.apple.com/videos/play/wwdc2018/402/)

[![](../Images/cd0a7f2f1b73249e6dd1aca9b5a7417e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2cwpotwd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2Acl4xdnHojOq-PNBc.jpg)

我以前写过关于 Playground 的文章，我很高兴苹果也在这方面投入了很多。人们在操场上互动和训练模特[创造 ML](http://Introducing%20Create%20ML) 的方式令人着迷。人们可能会问为什么 Playground 在 session 的演示中工作得这么好，但我们无法抗拒 Playground 的新变化，如分步执行、标记渲染改进以及消费定制框架有多容易。我们现在也可以通过订阅来发布我们自己的[游乐场。](https://developer.apple.com/videos/play/wwdc2018/413/)

### [核心 ML 新功能](https://developer.apple.com/videos/play/wwdc2018/708/)

[![](../Images/84a2be4762d1f615df4f6d1142e6a2e2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---wlgQc2u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2A1-xUL2gFr-Ys8WQT.jpg)

苹果去年推出了 Core ML，开启了机器学习的潮流。由于 Core ML 功能强大，我们可能会感到兴奋和沮丧，但我们无法定制它。现在 [2](https://developer.apple.com/videos/play/wwdc2018/708/) [部分](https://developer.apple.com/videos/play/wwdc2018/709/)告诉我们如何实现自定义层和模型，技术来减少模型大小，如量化和灵活的模型。这为目标跟踪的[视觉](https://developer.apple.com/videos/play/wwdc2018/717/)的改进和[自然语言](https://developer.apple.com/videos/play/wwdc2018/713/)框架的推出奠定了基础。机器学习从未如此简单。

### [测试中的新内容](https://developer.apple.com/videos/play/wwdc2018/403/)

我不能错过任何测试，因为这是我每天工作的一部分。你的程序如何避免回归错误并在没有任何测试的情况下为重构做好准备？

本次会议展示了覆盖率的改进和 xccov 工具的引入，以帮助我们在覆盖率报告的基础上构建自动化。Xcode 10 中的并行分布式测试可以节省我们一些喝咖啡的时间。另一个好消息是，测试有多个顺序执行模式，以避免因隐式依赖而产生的 bug。

[![](../Images/13d16febb00477db860acbca5c8a5845.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ru4ToJ6r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2A7Gw1utFOl4C3PKqV.jpg)

### [测试技巧&招数](https://developer.apple.com/videos/play/wwdc2018/417/)🌟

[![](../Images/ccbb6d1ed71699b5bec2a4cee059fd69.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tZTIlIey--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AOHjJdWEhHj7Fc4rq.jpg)

这是我最喜欢的。本课程从单元集成和端到端测试类比解释的测试金字塔开始，然后是一些非常酷的提示和技巧。

*   测试网络请求:我喜欢用 URLSession 分离 APIRequest 和 APIRequestLoader，用默认参数进行依赖注入，以及在 URLSessionConfiguration 中定制 URLProtocol

[![](../Images/9e60eec2a33389a6b70538c29c6b1c12.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cUrAr1cB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2066/1%2AxJzqnw37CZKFhhMwym2HHQ.png)

[![](../Images/8eb3177475bf014c811f710a2ed1a9cf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AI8Ig-Bh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2684/1%2AVUxjS-DpOfee7d5zTA1vrg.png)

[![](../Images/54e701ce47d8ba09f8708d2193ec642f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3DVXSJjK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2326/1%2ArgRwQla_YK3ADRwxZ4xpkg.png)

*   测试通知:通知是系统范围的，我尽量避免它。这展示了如何使用默认参数注入依赖项，以及如何使用自己的 NotificationCenter 而不是 NotificationCenter.default 来简化测试

[![](../Images/7945c0d9dc9286aaa8747822abb72f3f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A8KgLkK2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2602/1%2AmXiNRzit8VcFM4_GNM3ebg.png)

*   测试位置:用 LocationProvider 和 LocationFetcher 构建抽象。如何在测试中使用自定义协议和委托协议进行模拟

*   测试定时器:如何使用和模拟定时器的运行循环行为

[![](../Images/463152056fc3219f88f939aecefb6150.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NsaFpVZ_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2Aobf6GcsXcHAbGFy-lUENNw.png)

### [用 Xcode 和 LLDB 进行高级调试](https://developer.apple.com/videos/play/wwdc2018/412/)

[![](../Images/bcef5aaf96a197d004e9715c3de71417.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D9RsmUXh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2A6c81NEXU7tKYPEbQ.jpg)

LLDB 已经得到了改进，可以调试可靠性体验，AST 上下文损坏、快速类型解析等问题已经成为过去。我们可以回顾如何使用一些带有方便参数的常见 LLDB 命令，以及如何最大限度地使用 Xcode 断点。

[![](../Images/6ed35499181bd300e9a401890e0ba141.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W50dDFTQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AZtVpOaDnmt4VsDTHRiB6Ww.png)

### [游览 UICollectionView](https://developer.apple.com/videos/play/wwdc2018/225/) 🌟

[![](../Images/97fe1d1b9d0b7e13fd747e4f79161439.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IFRh8bBp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AskJgSTHlTohIj_DT.jpg)

我开始更多地使用 UICollectionView 而不是 UITableView，它也有与 NSCollectionView 相同的代码，这是比可怕的 NSTableView 更舒适的解决方案。

*   UICollectionViewLayout 中的项目大小:我经常依赖于 UICollectionViewDelegateFlowLayout 来指定项目大小，但是在观看了这次会议之后，我觉得将与大小相关的代码移动到布局对象感觉更像是一个好方法

[![](../Images/87a229fdb70bd3f639e6112e84f392d9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r0opNply--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2718/1%2AFS6OykJZ_zIPTzaWPPYk6g.png)

*   马赛克布局:这不是新的，但很好再次观看。您将了解如何使用缓存的布局属性实现自定义布局

*   数据源更新:没想到苹果会提到这个，不过关于 UICollectionView 如何处理批量更新倒是个很好的教训。我以前在我的[用 diff 框架更新 Swift 中 UICollectionView 数据的更好方法](https://medium.com/flawless-app-stories/a-better-way-to-update-uicollectionview-data-in-swift-with-diff-framework-924db158db86)中写过这个，这篇文章吸引了很多人。在本节课中，我们需要记住* *排序在数据源更新中很重要，但在集合视图更新中不重要** ❗️❗️❗️

[![](../Images/9d60fc6c685509421e138e7a24a4eac7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9IxHgAkG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AVMqhNo3bXxI-79e-r9iNvA.png)

[![](../Images/2133b570deb37c4ec18dd22f937938cd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yYdjdp3x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AKiWef6geZ3z0-jRaWNuqRg.png)

### [斯威夫特仿制药](https://developer.apple.com/videos/play/wwdc2018/406/)

[![](../Images/623eb303784d4d75de10bb63b86a395b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_yZbDTe9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AKWRTeiOV8FBNK3Mu.jpg)

从一开始，Generic 就是 Swift 的核心特性，我们都知道，它帮助我们以一种类型安全的方式实现可用于多种类型的通用代码。这次会议提醒我，我从来没有学到足够的东西，尤其是背后的合理设计。

这些会议展示了集合协议及其协议继承:MutableCollection、BidirectionalCollection、RandomAccessCollection，以及如何将它们组合起来为 conformers 提供通用功能。每个协议中的 associatedtype 需求，尤其是索引和元素，非常少，并且有足够的约束条件让协议在其协议扩展中实现许多必要的功能，这让我大开眼界。我非常喜欢阅读开源，所以查看这些协议的源代码有助于我理解更多。

[![](../Images/fc3db3f71888c7f853f5ae3ac57b0e3a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2NYyu1uD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2102/1%2AQIepiAZqK4QmX2IbLFKz0w.png)

关于费希尔·耶茨洗牌算法的那一部分详细描述了我们如何提出必要的协议，同时仍然使它们通用

[![](../Images/9b4cb979ad6a876d14030a7d83eb3a5d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---TLYGJjj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AactyrEg4E4qSFYu--K6uzA.png)

当他们提到计数和映射时，请注意，您可以更多地了解每种具体类型如何与协议扩展中的定制点挂钩

[![](../Images/bc1df5c4d98d21caedd2a177923afebe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Q_Noyuff--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2704/1%2AIG15tCSq49SqgvgJrXHDWw.png)

最后学习 [Liskov 替换原则](https://en.wikipedia.org/wiki/Liskov_substitution_principle)与类继承中的协议。你还应该[有效使用收藏](https://developer.apple.com/videos/play/wwdc2018/229/)了解如何利用收藏中的便利功能。

[![](../Images/cc4fc793aa4366f313ff070bd60615f2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---NRSSb2p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AoIKcsw6-H37i2A1t.jpg)

### [你可以信任的数据](https://developer.apple.com/videos/play/wwdc2018/222/)

[![](../Images/d2bd806619378ccb924bdff61c182f55.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9-0nJdwW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AIv80a99O1g6k-oO4.jpg)

虽然 Codable 在数据完整性方面有很多优点，但了解这些对于确保您收到的数据确实是格式和结构正确的数据是很有好处的。CommonCrypto 也是新的 iOS SDK 的一部分，所以你不需要我的 T2 神秘库来处理你的应用程序中的加密和散列。

### [拥抱算法](https://developer.apple.com/videos/play/wwdc2018/223/)

[![](../Images/1ffce6e50421ac968c696fbe665a7ea5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ds3pDiQc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AqocdXtZ8cCAJ0mKV.jpg)

这是最令人愉快的观看，因为它就像一个发言者和想象中的经理 Crusty 之间的对话。在这里，我学会了如何意识到算法的复杂性，以及如何利用已经针对性能进行了优化的内置基础函数。

在这次会议之后，我情不自禁地立即去 Swift repo 阅读 [Algorithms.swift](https://github.com/apple/swift/blob/master/test/Prototypes/Algorithms.swift) 文件。

### [图像和图形最佳实践](https://developer.apple.com/videos/play/wwdc2018/219/)

[![](../Images/2624d5c760563b02303538ed9b49a0d9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zsNYqEEp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2A-qDMSLxL89PuOm-m.jpg)

了解图像编码和解码如何通过数据和图像缓冲区工作，以及如何影响内存和性能。有像下采样这样的技术可以解决这个问题。这也建议不要使用后备存储，而是使用 UIImageView

[![](../Images/d5e755370d4171b69a165db5b5829240.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VuiPA-ZM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2766/1%2Amt70wBlbwC9Kz18vXN5THQ.png)

[![](../Images/e6e834dcb8d6fa7a578754d99a2da65d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wE64O51---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AuV9Ata-pPnb-US3WrI6Y_A.png)

### [图瑞创建指南](https://developer.apple.com/videos/play/wwdc2018/712/)

[![](../Images/f8a473175532cb324b8838338c49f00c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6zcEWT8h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AyWr3HKYpIslcV-Zz.jpg)

我以前写过关于 Turi Create 的文章，但它只是触及了 Turi 提供的许多任务的皮毛。今年苹果发布了 [Turi Create 5](https://github.com/apple/turicreate/releases/tag/5.0b1) ，具有风格转换任务、视觉特征打印、GPU 加速和推荐器模型改进。我迫不及待地想探索。如果你看一下 Create ML framework 中的 MLDataTable，它看起来像是在引擎盖下有 Turi 的 SF frame。

就是这样。感谢阅读。今年你最喜欢的会议是什么？请在下面的评论区分享