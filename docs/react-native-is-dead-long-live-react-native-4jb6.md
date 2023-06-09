# 反击土著已死，反击土著万岁

> 原文：<https://dev.to/inthepocket/react-native-is-dead-long-live-react-native-4jb6>

> 这篇文章最初出现在口袋博客的[。](https://inthepocket.com/blog/2018/react-native-is-dead-long-live-react-native)

今天在推特圈和科技博客上有很多喧嚣和骚动。昨晚，Airbnb 在 Medium 上发布了五篇系列博文，涵盖了他们使用 React Native 的体验和发现，最后宣布他们将在今年年底告别 React Native。

> “由于各种各样的技术和组织问题，我们将关闭 React Native，并尽全力让 Native 变得令人惊叹。”

这一消息令人惊讶——因为 Airbnb 一直在继续努力推动 React Native 的开源项目和工作流工具的开发，例如上个月 React Europe 上的 Lona 公告——但在陷入末日推特和跨平台咆哮之前……

Airbnb 是 2016 年首批采用 React Native 的硅谷主要初创企业之一，这是在 2015 年年中发布后不久。当时，Airbnb 在移动设备上有原生的 iOS 和 Android 堆栈，在 web 上有 React 堆栈。鉴于 React 的良好体验，他们决定采用 React Native 来加快他们的本机开发。这种迁移的含义(没有立即变得明显)是它产生了大量的开销。他们不得不投入大量额外的精力来编写支持功能(例如，本机桥，包装代码，...)以便能够通过 Javascript 支持(现有的)原生功能。以及为每次 iOS、Android 和 React 更新维护这些库。对他们来说，除了技术挑战，这也是一个额外的组织挑战。

由于他们最初的意图是“作为一家公司更快地行动”，由于多种原因，使用 React Native 对 Airbnb 来说不再是一个可行的战略。这是否意味着我们应该背过身去，做出自然的反应？我们认为不是。我们可以涉及博客中提到的许多问题。但是 Airbnb 的运营规模(作为参考，他们有 100 名开发人员在开发他们的移动应用程序，在口袋里，一个产品团队通常由 4-10 名开发人员组成)和他们的投资深度以及他们应用程序的产品& UX 要求是他们决定放弃 React Native 的一个重要原因。与任何工具一样，您应该知道/预见到它的局限性。

## “混合应用很难。”

虽然 React Native 是一个 Javascript 框架，但是大部分 React Native 开发者至少了解两个平台(Android，iOS，React Native)。在过去一年半的时间里，我们在口袋中使用 React 本机应用程序时了解到，了解常见的本机模式(如导航..)是一项宝贵的资产，在某个时候，您最终将不得不在您的代码库中引入一些本机部分。是的，这带来了额外的复杂性——但最终，我们真的希望尽可能地坚持使用 Javascript 尽管在引擎盖下有原生集成。

> "...我们最终在三个平台上支持代码，而不是两个。”

在 Airbnb，他们在 React Native 中有 220 个屏幕，但另外他们在每个平台上有大约 880 个原生屏幕，这基本上意味着他们只有 20%的功能生活在 React 代码中。“我们遇到的许多困难都是由于我们采用的混合模型方法”也是我们在项目中结合本地代码和 React 代码时学到的经验之一。然而，当选择使用 React Native 作为平台时，这些困难不应该成为挫折。

> “当所有的东西都在一起时，许多特性都是如此，迭代速度、质量和开发人员体验都达到或超过了我们所有的目标和期望。有时，真的感觉我们就要改变移动开发的游戏了。”

尽管 Airbnb 现在已经决定在其移动产品中“淘汰”React Native，但这应该不会对 React Native 的未来产生负面影响。Airbnb 的主要开源项目(Lottie，react-native-maps，..)正在被移交给 React 本地社区。读完所有五篇博文后，我们可以有把握地说，考虑到可维护性和可持续性，React Native 不再适合 Airbnb 的应用规模。最终，他们证明了 React Native 是一个极其强大的框架，可以处理和扩展大型生产应用程序。但是像任何工具一样，它并不总是适合这项工作。

在口袋里，当谈到 React Native 时，我们仍然相信我们正处于移动应用程序开发的一些重大变革的黎明。这个社区非常活跃，脸书宣布他们正在进行大规模的重新架构，以使框架更加灵活，更好地与本地基础设施集成，像 Expo 这样的公司取得了重大突破，创建了像 T2 这样的库。我们正在继续努力弥合开发&设计之间的差距，使用基于 React 组件的工具，改进我们的工作流程和迭代速度，并通过这样做为我们的客户创造更多的价值。

反应土著没有死。

*感谢 [@bitcrumb](https://twitter.com/bitcrumb) 、[@ jodierckens](https://twitter.com/jodierckens)&[@ Thomas mons](https://twitter.com/thomasmons)帮我写这篇帖子。*