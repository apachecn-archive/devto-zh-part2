# 识别和减轻软件开发中的 90-90 规则

> 原文：<https://dev.to/ben/identifying-and-mitigating-the-ninety-ninety-rule-in-software-development-4ap3>

11 天前，我们完成了开发 iOS 应用程序初始版本的大约 90%。然后我意识到:我们还有 90%的工作要做。

这是一个如此普遍的现象，以至于它有一个名字和维基百科页面:[九十法则](https://en.wikipedia.org/wiki/Ninety-ninety_rule)。

> 前 90%的代码占开发时间的前 90%。剩下的 10%的代码占了开发时间的 90%。

尽管感觉我们从一开始就采取了最小的方法，但我们在方法上稍微偏离了基础，我看到了继续沿着这条路走下去的许多痛苦。因此，尽管*就要完工了，我们还是重新开始。*

我做了这个问题:

# [![GitHub logo](../Images/75095a8afc1e0f207cda715962e75c8d.png) 建议修改设计方向 #88](https://github.com/thepracticaldev/DEV-ios/issues/88) 

[![benhalpern avatar](../Images/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2018-11-01T18:19:53Z">Nov 01, 2018</time>](https://github.com/thepracticaldev/DEV-ios/issues/88)

我想提出一个设计调整，简化这个应用程序的初始版本将被释放。

我建议我们去掉标签，让它成为移动网络的一个更简单的外壳。所有的导航将通过该应用程序的网页版本。

我认为现有方法的最后 10%可能会导致另外 90%的工作。这并不是说我们不能推动它通过，但我可以预见它会导致某种维护负担，我们正试图避免。

因此，我认为我们应该使用一个精简的外壳，只提供 web 视图，web 导航到所有需要的内容，底部栏提供后退/前进和其他实用按钮(查看我当前所在的 URL？在 Safari 中打开？等等。)

[![image from ios](../Images/f95cc5d93f38b3cea5e63fdc5bd8ca28.png)T2】](https://user-images.githubusercontent.com/3102842/47870349-bcd01880-dddf-11e8-97c5-5eb0e46e0ab6.jpg)

我们专注于网络，我们已经在网络上拥有所有需要的功能。我认为我们应该采用一种更精简的方法，发布一些至少能像网络一样工作的东西。

至于本机功能，我认为我们应该支持深度链接、推送通知和其他便利，但主要是从实现的角度出发，尽可能简单地推出一些东西，我认为这就是我所描述的。

我知道我们在当前的方法中投入了大量的工作，但我不认为我们应该陷入沉没成本谬论。我认为我们已经学到了很多关于什么是困难的，什么是容易的，等等。

最后的想法:如果我们走这条路，反应原生或颤动可能是一个更好的方法。

[View on GitHub](https://github.com/thepracticaldev/DEV-ios/issues/88)

然后我编造了一个概念证明。

这个概念证明基本上就足够了。为了推出原生 iOS 应用程序，我们进行了简单的体验。它*利用了 iOS 的一些优点，重要的是缓解了一些令人沮丧的限制，这些限制只存在于 iOS 上的移动 Safari 领域。重要的是，这也意味着我们已经推出了一个发展平台。我们已经跨越了鸿沟，现在*可以学习*如何提供理想的本土体验。*

# **周五我们[向应用商店](https://itunes.apple.com/us/app/dev-community/id1439094790)** 提交了应用

我们知道快速启动比生活在原型炼狱中更重要。不太明显的是，扔掉大量工作并在一个晚上开始重写整个应用程序是多么简单。这是一个新的。

当前应用程序的几乎所有代码都位于一个文件中，简单地命名为`ViewController.swift`。

在我意识到我们需要一个转变的那个晚上，我实际上开始写这个应用的三个版本。一个在 Xcode/Swift，一个在 Flutter，一个在 React Native。我也鼓励其他人参加烘焙比赛。在这个过程中，真正本土的 Swift 方法总是为我赢得胜利。

我写了更多关于这种方法的文章，我认为我们做了一个很好的决定:

[![The DEV Team](../Images/6b3a9c79aeecf790d5144f3fe1881f50.png)![](../Images/fe64a787b888dfb20fc13ad1e466da3d.png)](/devteam) [## 为什么不反应原生？为什么不扑？为什么不是流星？为什么不是 NativeScript？

### 开发团队的 Ben Hal pern 10 月 26 日 182 分钟阅读

#meta #ios #reactnative #flutter](/devteam/why-not-react-native-why-not-flutter-why-not-meteor-why-not-nativescript-573h)

我还发起了一个关于沉没成本谬误的帖子，并得到了许多有效的反馈:

[![ben](../Images/fe64a787b888dfb20fc13ad1e466da3d.png)](/ben) [## 你有什么不好的“沉没成本谬误”的故事吗？

### 本哈尔彭 11 月 2 日 181 分钟阅读

#discuss](/ben/do-you-have-any-bad-sunk-cost-fallacy-stories-3dkm)

现在我们已经在 iOS 的土地上插上了我们的旗帜，是时候定居和扩张了。开始远没有中间重要。我们希望从这个开始阶段快速过渡到一个新的维护和发展阶段。

快乐编码

*安卓即将上市*🤖