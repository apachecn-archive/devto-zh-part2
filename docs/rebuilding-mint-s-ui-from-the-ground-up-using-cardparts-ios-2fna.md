# 使用 CardParts (iOS)从头开始重建 Mint 的用户界面

> 原文：<https://dev.to/croossin/rebuilding-mint-s-ui-from-the-ground-up-using-cardparts-ios-2fna>

不可能的！太有野心了！Mint 是一个多功能的 iOS 应用程序，自 2008 年推出以来一直没有显著变化，彻底改造它的用户界面似乎是一项艰巨的任务。我们的开发团队是如何完成这次重新设计的？简单。我们使用了自己构建的 UI 框架，名为 [CardParts](https://github.com/intuit/CardParts) 。
T3![Mint CardParts](img/f4b3551473d238f904c33326d4e1f914.png)T5】

### 什么是 CardParts？

CardParts 是一个反应式的、基于卡片的 UI 框架，构建在 Apple 的 UIKit 框架之上。CardParts 将 MVVM(模型-视图-视图模型)设计范式带入生活，并允许开发人员专注于构建功能性和交互式 UI，而不是每次需要构建新的 UI 时都重新发明轮子。

> “……充分利用流行音乐来区分展示特征，以及💯用于视图控制器包含。— [Paul Hudson，与 Swift 一起黑客攻击](https://twitter.com/twostraws/status/1013711249404780544)

CardParts 的主要目的是提供一个易于构建甚至易于测试的 UI 架构。开发人员可以将协议驱动的特性应用到他们的卡上，并利用 MVVM 设计范例将复杂的逻辑从视图控制器转移到视图模型中。创建这个视图控制器包含允许视图模型逻辑被容易地测试，并且与视图控制器分开。

### 我们做了什么？

现在我们有了一个可以让我们的工程团队快速行动的 UI 框架，我们开始改进 Mint 的用户体验。我们选定了一个设计，专注于向用户展示卡片，这将帮助他们更深入地了解自己的个人财务状况。当然，我们称这些洞察力卡片为 MintSights。

为了实现改善客户体验的目标，我们利用了 CardParts 提供的一切。我们采用了 MVVM 架构，并利用定制特征来完全定制用户体验，并在 MintSights 中提供各种各样的功能。让我们来看看 MintSight 卡的流程。

MintSight 卡是一种色彩鲜艳且统一的卡，能够吸引用户的注意力。每张卡片都包含相似的设计元素——图标、标题和描述。我们能够创建一个通用的视图控制器和视图模型来支持这个功能，并创建一个反应式 UI。以下是视图控制器和视图模型代码的示例:

用于 MintSights
的视图控制器代码示例