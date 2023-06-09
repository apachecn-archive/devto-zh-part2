# 首次致力于世界范围的项目，第 1 部分

> 原文：<https://dev.to/aniketsmk/first-commits-to-world-spanning-projects-part-1-29n4>

<figure>[![](img/51cfa2fce1cbd4b10c400d02ab0fe20e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--glGaUIHD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/838/1%2ALW2y6C3Ty2NLxCHLjmlQGA.png) 

<figcaption>一段经历[为 Mozilla 代码库贡献](https://developer.mozilla.org/en-US/docs/Mozilla/Developer_guide/Introduction)！</figcaption>

</figure>

我对 Firefox 的第一次承诺是不可避免的。在开发软件的过程中，通常会有很多道德雷区。Firefox 是一个突出的项目，它是一股真正可见的力量。

我曾经做过大规模的项目，至少有几千人在看，很难去做简单的项目。

这是做这件事的过程。

### 第 0 步

谷歌“贡献给火狐”，会带你到超级有用的页面[这里](https://developer.mozilla.org/en-US/docs/Mozilla/Developer_guide/Introduction)。

### 获取源代码！

大规模项目面临的第一个挑战是简单地将所有代码下载到你的机器上。我来自一个发展中国家，这里的互联网不是很好。

Firefox 是一个 monorepo(所有东西的所有代码都在一个 repo 中),在任何情况下都有相当多的共享代码。简单明了的说明会告诉你在克隆的时候去喝杯咖啡。

这在大多数第三世界互联网连接上是完全不可能的。如果网络中断，克隆不会恢复，而且对超时很敏感。幸运的是，mozilla 已经为你提供了解决方案，下载一个[包](https://developer.mozilla.org/en-US/docs/Mozilla/Developer_guide/Source_Code/Mercurial/Bundles)，这是一个克隆的压缩版本。这可以像一个普通的文件，更小，可以恢复。

### 把构建需求也排队！

在等了几个小时才获得 1.4Gb 的软件包之后，您最不想做的事情就是现在再等几个 Gb 来获得您需要的其他工具。

在我的例子中，我是为 android 开发的，没有 ndk，所以需要启动它。

### 做一个构建

现在您已经有了源代码，接下来要做的事情是尝试按照原样构建它。这将有助于确保你的工作底线。

### 碰到问题

就我而言，我马上遇到了一个问题。我跳上 [mozilla irc](https://wiki.mozilla.org/IRC) 询问这件事。irc 上一个非常积极参与构建系统 nalexander 的人给了我回复。

这是一个已知的[错误](https://bugzilla.mozilla.org/show_bug.cgi?id=1460355)在某些配置中，他们有一个解决方法。

### 为自己解决问题(因此是所有人)

我对巴洛克建筑系统有丰富的经验。我自己为一个跨平台的应用程序生成产品创建了一个。我查明了问题，验证了修复工作，调查了一下为什么它最初没有工作。因为它原来是谷歌的一个 bug，已经被修复，但还没有部署。

你必须非常小心地对某人说 bug 在你这边’。你非常需要一个失败的测试用例，否则，a:你可能是错的，b:你对修复没有贡献。c:你可能仍然是错的，但是现在你需要沟通来找出答案！

解决办法是直道。所以，先说说有没有更直接的方法！回 IRC 讨论。

经过更多的交谈，更多的尝试，经典的碰到其他问题，只有你这边，几天后，我有一个修复准备。

在我的下一篇文章第 2 部分中，我将更深入地讨论现在你已经准备好了一个补丁应该怎么做！