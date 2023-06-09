# 使用 Swift 的生成艺术

> 原文：<https://dev.to/rockarts/generative-art-using-swift-4aa5>

几周前，阿里·斯皮特尔(Ali Spittel)在我的博客上发了一篇帖子，展示了如何使用 Javascript 创作艺术作品:

[![aspittel image](img/45a8d86fe0ff981bf7cdb45b471a3c5c.png)](/aspittel) [## 生成艺术导论

### 阿里·斯皮特尔 9 月 25 日 186 分钟阅读

#art #frontend #javascript #showdev](/aspittel/intro-to-generative-art-2hi7)

这篇帖子有一些很好的灵感来源，我开始将 Javascript 示例转换成 Swift 并在我的 iPhone 上显示。在这篇文章中，我将把来自创成式艺术教程的[平铺线条 Javascript](https://generativeartistry.com/tutorials/tiled-lines/) 转换成 Swift。我们会让它更快一点！

首先:在 XCode 中创建一个单一视图应用程序，然后跟着做！

## 在 Swift 中绘图

为了在 Swift 中绘图，我们需要一个自定义的 UIView，可以添加到我们的 ViewController 中。让我们创建一个名为 DrawView 的，它将从 UIView 继承。我们将添加两个 init 方法，覆盖 draw 并添加一个方法存根来绘制一行，如下所示: