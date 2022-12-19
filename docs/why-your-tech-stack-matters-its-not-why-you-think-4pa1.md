# 为什么您的技术堆栈很重要(不是您认为的原因)

> 原文：<https://dev.to/gabek/why-your-tech-stack-matters-its-not-why-you-think-4pa1>

[![What’s_New_in_Swift_-_402-480x253.png](../Images/ff65234abaf1c45bb11d2de86b963083.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qbw8U2xM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static1.squarespace.com/static/50ce21f9e4b0a7200de38642/t/5a762fc453450ac9093ce48a/1517694953069/What%25E2%2580%2599s_New_in_Swift_-_402-480x253.png%3Fformat%3D1000w)

最近，我收到了许多招聘人员的邮件，他们正在寻找一名 iOS 开发人员来开发 React Native。虽然我通常不关心人们选择什么技术来构建他们的产品，但我认为这是一个值得深入探讨的主题。但首先声明:我有偏见，我只代表我自己，不代表我现在或曾经是其中一员的任何团队。

在这篇文章中，我将重点讨论移动开发，但它和桌面开发一样糟糕，甚至更糟。用网络技术编写的糟糕的电子应用到处都是，今天[微软宣布](https://blogs.windows.com/msedgedev/2018/02/06/welcoming-progressive-web-apps-edge-windows-10/)网络应用将成为微软 Windows 商店中仅次于本地应用的一等公民。

我在不同的移动开发选项上的经历，得到了选择它的团队的善意支持。举例来说，我目前所在的团队，我认为是最优秀的团队，他们走的是 React Native 路线，很好地融合了实际和本地的元素。

我发布的 iOS 应用程序:

1.  仅 Web(Web 视图)
2.  混合框架( [Phonegap](https://phonegap.com/) ， [Titanium](https://www.appcelerator.com/Titanium/) )
3.  迅速发生的
4.  目标-C
5.  游戏引擎(C++)
6.  [Xamarin](https://www.xamarin.com/) (C#)
7.  [React Native](https://facebook.github.io/react-native/) (Javascript)

虽然我不会深入讨论上面每个栈的细节，但我想使用其中的两个解决方案 Xamarin 和 React Native 作为例子来讨论使用它们的实际情况。

Xamarin 是一个开发环境，它允许你用 C#编写应用程序，同时仍然使用原生的 Apple 控件和框架。业务逻辑、网络、持久性等只需编写一次。C#是一种普遍受欢迎的语言，有很多特性。虽然与苹果的语言非常不同，但在语言层面上没有什么是你真的不能做的。

使用 React Native，视图层被额外抽象。它对一切都有自己的版本，只有幕后的原生框架和库才为人所知。通常它们根本不被使用，取而代之的是您所期望的 React 本地特定实现。无论是从业务逻辑还是 UI 的角度来看，对于多个平台来说，大多数东西只需编写一次。一般来说，当问及 Javascript 时，开发人员社区会有不同的反应。

那么我们就利用这两个开发环境，来说说苹果在 iOS 中添加的一个虚构的新 UIKit 类，姑且称之为 UITriangleView。

第一次电视采访将在 WWDC 举行。将会有专门针对它的会议，包括它的使用示例、在 Swift 和 Objective-C 中以高性能方式使用它的详细信息，以及接下来您可以去哪里阅读更多关于它的内容。

UITriangleView 随后将在 iOS 测试版中提供。所有的 iOS 开发者都将下载带有新 SDK 的新 XCode 并试用。博客文章将会讲述绘制三角形的所有优点，并且会深入探讨三角形在 iOS 上的未来。在整个过程中，Xamarin 和 React 本地开发人员都在耐心等待，因为这两个环境都不知道 UITriangleView。根据这个新类的复杂性，这些组织的发布时间表和测试，我们正在讨论从几周到几个月的时间将它添加到 Xamarin 或 React Native。当它出现时，很可能会出错，因为他们也只是刚刚了解它。

在寻找文档时，你会从苹果公司找到很多。它很详细，会让你快速移动。但是对于您的非本机实现，您不会找到此文档。使用 React Native 你根本不能直接访问这个类，所以苹果的文档对你来说毫无用处。有了 Xamarin，希望你可以自己将类的用法翻译成 C#，只要它没有使用任何原生的 Apple 语言特性，或者你在 C#中有不同的做事方式。

一些新的非常酷的第三方库可能会建立在 UITriangleView 之上。也许 FUTriangleManager 是用 Swift 写的，利用了新类。Xamarin 和 React Native 永远不会知道这个库中的 API，因此必须完成一个称为[桥接](https://facebook.github.io/react-native/docs/native-modules-ios.html)(或[绑定](https://developer.xamarin.com/guides/ios/advanced_topics/binding_objective-c/walkthrough/)，或链接)的过程，其中希望利用该库的开发人员必须编写代码，将非本机 C#或 Javascript 链接到本机 iOS 或 Android 代码。对于开发人员想要使用的任何库、SDK 或第三方代码，都必须完成这个过程。然后，必须在应用程序的整个生命周期中维护此桥代码，并且在第三方代码每次更新时更新。考虑到你写的这个第三方桥只适用于一个平台，你想在任何地方运行相同代码库的梦想就破灭了。

所有这些，以及更多，经常导致使用非本机开发的人推迟新的 API，要么是因为他们必须这样做，要么是因为这太麻烦了。这没关系。你不需要生活在危险的边缘。但有时这甚至与利用新技术无关。您的合作伙伴是否出于任何原因需要您集成第三方代码的 SDK？这意味着**任何第三方代码**。分析、合作伙伴 SDK、营销等。有时您的环境有一个特定的版本，有时您可以桥接它，有时从头开始编写自己的实现更有意义，这样您就可以尝试在多个平台之间共享它。从头开始编写第三方 SDK 可能不会符合你“更容易”或“更快”的想法。

这些是我认为的围绕 iOS 开发的铁一般的事实。技术、实施和 it 的实际日常使用。你花了多少时间通过邮件 Xamarin 和搜索 React Native 的 Github 问题试图让它工作。**但我认为更重要的是软事实。**

围绕这些技术有哪些类似的社区活动？招聘时，找到一个有使用非本地人经验的人有多难？你会雇佣本地开发人员，并在他们加入时对他们进行培训吗？一年、两年后，当他们被排除在苹果令人兴奋的新 API 之外时，他们会有多高兴呢？当他们去寻找下一个职位，并不得不解释他们使用替代选项构建，并且已经有一段时间没有使用支持的苹果技术编写时，他们会有多高兴？

当然，这对我来说有点“多愁善感”,但作为一个已经成为苹果生态系统一部分的人，我觉得我是某个事物的一部分。当有新的东西出现时，我们都会一起谈论它。我们有休闲室和论坛，在那里我们发布问题，在咖啡店聚会，以及由使用这些技术的公司主办的活动。每年一次，我们从世界各地来到 WWDC。这是一个真实的，有形的东西，你可以感受和体验。你是这个世界的一部分。这不仅仅是一个技术选择。对所使用的工具有一种热情。许多有着悠久历史的工具和开发人员会追溯到下一个的[，我碰巧认为这很酷。](https://en.wikipedia.org/wiki/NeXT)

当然，Javascript 人也有激情。但是你可以反驳 [*他们不得不*](https://www.quora.com/Why-does-JavaScript-have-so-much-hype-nowadays/answer/Richard-Kenneth-Eng?utm_content=buffer680b5&utm_medium=social&utm_source=twitter.com&utm_campaign=buffer) 。它通常不是对设备和应用程序的热情，而是对网络和 DOM 的热情。

虽然“写一次，在任何地方运行”的圣杯可能会吸引你，但这是一个一次又一次很少实现的承诺，我个人看到过许多失败的例子。不仅要考虑那些硬事实(第三方集成、新 API、性能、兼容性、长期维护)，还要考虑软事实(开发人员的快乐、社区、招聘)。

有时候你只是想建立一个快捷的应用程序，有时候你想做一些更大的事情并成为其中的一部分。