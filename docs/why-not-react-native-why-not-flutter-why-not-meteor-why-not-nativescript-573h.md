# 为什么不反应原生？为什么不扑？为什么不是流星？为什么不是 NativeScript？

> 原文：<https://dev.to/devteam/why-not-react-native-why-not-flutter-why-not-meteor-why-not-nativescript-573h>

我们不久前开始开发一款 iOS 应用，昨天刚刚发布了一款基本的、功能不全但可用的测试版应用。

[![The DEV Team](img/6b3a9c79aeecf790d5144f3fe1881f50.png)![](img/15e6599da1ea8b6f92e1b678e9347b2c.png)](/devteam) [## 帮助测试开发测试版应用程序！

### 开发团队的李佳薇 10 月 25 日 181 分钟阅读

#ios #meta](/devteam/help-test-the-dev-beta-app-4kme)

这是开源社区的努力。我在早期通过提供一些初步的方向来领导这个项目，但是后来我们让比我更好的人来负责这个项目。

但我确实是带着高水平的技术方向进来的。如果我对使用什么样的技术和架构没有起码的看法，这一切就永远不会发生。我们采用了我称之为“大本营法”的方法。web 视图顶部的本机外壳。

在我们的回购中，我包含了一个 Basecamp 资源的链接:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[forem](https://github.com/forem)/[DEV-IOs](https://github.com/forem/DEV-ios)

### 开发社区 iOS 应用程序

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Build Status](img/6dfcb486a52bd1b00a44be51502c12b3.png)](https://travis-ci.com/thepracticaldev/DEV-ios)[![GitHub License](img/0e96cc87ff2986e2d83874faceda1e97.png)](https://github.com/thepracticaldev/DEV-ios/blob/master/LICENSE)[![Language](img/a8ac7a556d99728dc99a818bd67917dd.png)](https://developer.apple.com/swift)[![Maintainability](img/8df03de5ddee5a39feabf717dc97de18.png)](https://codeclimate.com/github/thepracticaldev/DEV-ios/maintainability)[![Test Coverage](img/a6f10b19fa167c97acb9733714c3cd50.png)](https://codeclimate.com/github/thepracticaldev/DEV-ios/test_coverage)

# 开发 iOS <g-emoji class="g-emoji" alias="sparkling_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f496.png">💖</g-emoji>

这是对 iOS 应用程序[开发到](https://dev.to)的回购。

# 状态:

发布了第一个版本，更多信息:[https://twitter.com/bendhalpern/status/1061323718058786822](https://twitter.com/bendhalpern/status/1061323718058786822)

# 设计精神

随着时间的推移，我们将增加更多的本机代码，但目前我们采用的是*本机 shell/web 视图*的方法。这种方法在 iOS 时代很早就失宠了，但我相信这是一种非常有效的方法。它的灵感来自于 Basecamp 的工作方式。我们的技术有点不同，但理念是一样的。

[https://m . signalvnoise . com/base camp-3-for-IOs-hybrid-architecture-AFC 071589 c 25](https://m.signalvnoise.com/basecamp-3-for-ios-hybrid-architecture-afc071589c25)

[https://signalvnoise . com/posts/3743-hybrid-sweet-spot-native-navigation-web-content](https://signalvnoise.com/posts/3743-hybrid-sweet-spot-native-navigation-web-content)

[https://signalvnoise . com/posts/3766-hybrid-how-we-take-base camp-多平台小型团队](https://signalvnoise.com/posts/3766-hybrid-how-we-took-basecamp-multi-platform-with-a-tiny-team)

[https://www.youtube.com/watch?v=SWEts0rlezA](https://www.youtube.com/watch?v=SWEts0rlezA)

通过尽可能多地利用`wkwebviews`,我认为我们可以让这一切变得非常棒，并与我们的网络开发工作非常顺利地同步。在有意义的地方，我们可以完全原生地重新实现某些东西，或者构建完全原生的特性。生活是一次旅行，而不是目的地。

# 贡献的

1.  派生并克隆项目。
2.  在 XCode 中构建并运行项目。
3.  …

</article>

[View on GitHub](https://github.com/forem/DEV-ios)

但是为什么呢？

React Native 和 Flutter 等都是不错的选择。但我觉得，即使在这些平台上创建视图有跨平台的好处，我们仍然必须从根本上在原生的 web 上重新实现，并同步协调每一个变化。我们可以通过大量的艰苦工作和协调来克服这一点，但我们将一直在打一场艰苦的战斗。

为了继续利用网络的优势，保持我们的构建速度，并最终保持*更多的可选性*，我们采取了我们所做的方法。

我用过 React Native 和 Flutter，认为它们是很多项目类型的正确选择，但真的不是这个。几乎每次提到这个话题，我都会被问到“为什么不”这个问题。所以答案是:我们正在一个强大的、不断改进的 web 应用程序之上构建一个非常简单的原生包装器。手机越来越快，网络越来越强大。这是一个很好的选择。任何不利因素都是必要的权衡。

我不知道未来会怎样，但在一个持续不确定的领域，我觉得我们的立场目前相当不错。