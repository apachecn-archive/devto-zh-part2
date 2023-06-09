# 将脑电波记录到 MongoDB

> 原文：<https://dev.to/ladvien/recording-brain-waves-to-mongodb-58fm>

# 描述

该项目从 MindWave Mobile 2+中获取脑电波读数，通过蓝牙 le 将其传输到 iOS 应用程序。iOS 应用程序调用远程节点服务器，这是一个最小的 REST API，传递脑电波样本。节点服务器将数据存储在 MongoDB 服务器上。然后，MongoDB 服务器向使用 MongoDB BI Connector 的商业智能应用程序公开。最后，使用 Tableau Professional Desktop 访问数据并创建可视化效果。

咻。

概括一下:

*   [MindWave Mobile 2+](https://www.sparkfun.com/products/14758)
*   [iOS App](https://github.com/Ladvien/MindWaveJournaler) (暂定名为 Mind Wave JournalerSwift)
*   [休息服务器](https://github.com/Ladvien/mind-wave-journal-server)(意念波记者；NodeJS)
*   [MongoDB BI 连接器服务器](https://www.mongodb.com/products/bi-connector)
*   [Tableau 桌面专业版](https://www.tableau.com/products/desktop)

最终的结果是一个系统，可以让远程脑电图分析师几乎实时地检查样本。

[![eeg-visualization](img/9e30352de96ad96694fce46de9e37d7c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LDx4NnVl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/eeg_poc_tableau_viz.png)

下面，我将展示我是如何设置这个系统的。但是，在那几句警告之前。

## 明白了

### 黑客的憎恨者

这不是一个黑客友好的项目。它依靠几个付费许可证，一个苹果开发者许可证(99 美元)和 Tableau 桌面专业版(10，000，000，000 美元左右)。当然，核心硬件 MindWave Mobile 也是 99 美元，但我认为这是公平的。哦！我们不要忘记，即使你购买了苹果开发者许可证，你仍然需要一台 Mac(或 Hackintosh)来编译应用程序。

然而，作为概念验证，我认为它是可靠的。希望一个优秀的黑客能够看到系统中的一些调整是如何让部署变得非常便宜的。

### 最小可行破解..呃，产品

这里提供的源代码是一个[最小可行的](https://en.wikipedia.org/wiki/Minimum_viable_product)。花里胡哨的话意，只有*基地的*功能被实现了。还有许多其他的事情可以做来改善系统的每一部分。

不是我犯傻，但是请不要指出来。这是我对提供这个*免费*信息的唯一要求。

我知道还有很多可以改进的地方。它们没有被制作出来的原因与我的无知无关(好吧，至少是其中的大部分)，而是我的时间限制。

### 我讨厌黑板

就是这样。我讨厌画面。

## 入门

在开始这个项目之前，让我们把需要的东西列一个清单。

*   Mac(编译 iOS 应用程序)
*   [MindWave Mobile 2+](https://www.sparkfun.com/products/14758)
*   一台运行 Ubuntu 16.04 的远程服务器(理论上本地服务器也可以，但你只能靠自己了)
*   [苹果开发者许可证](https://developer.apple.com/programs/)
*   [Xcode](https://developer.apple.com/xcode/) ，带 Swift 4.1
*   [可可布丁](https://cocoapods.org/)
*   运行在 Ubuntu 上的 MongoDB
*   [MongoDB BI 连接器服务器](https://www.mongodb.com/products/bi-connector)
*   具有远程连接能力的商业智能平台

关于商业智能平台——如果有人有免费的建议，请在下面的评论中留下。我想对整个系统的第一个改进是远离 Tableau。我说过我讨厌它吗？

好了，我们开始吧！