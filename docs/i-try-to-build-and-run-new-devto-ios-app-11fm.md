# 我尝试构建并运行新的 iOS 开发应用程序

> 原文：<https://dev.to/yuyabu/i-try-to-build-and-run-new-devto-ios-app-11fm>

> DEV 现在有一个 iPhone 应用程序。仍然有很多工作在进行中，但是他们说如果你对你的第一个版本不感到尴尬，你等得太久了。对吗？
> 
> 让我知道你的想法:[https://t.co/CgWzdSuWtp](https://t.co/CgWzdSuWtp)[pic.twitter.com/RDG1z8WlYO](https://t.co/RDG1z8WlYO)
> 
> — Ben Halpern 🤗 (@bendhalpern) [2018 年 11 月 10 日](https://twitter.com/bendhalpern/status/1061323718058786822?ref_src=twsrc%5Etfw)

[https://github.com/thepracticaldev/DEV-ios/](https://github.com/thepracticaldev/DEV-ios/)

我试图建立和运行这个应用程序。

克隆存储库，并在 Xcode 上按下运行按钮。

[![error](img/b2587c7107b30a6f5d8b971ce1c1a2a3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ETiqG4t3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/Dr1ZyfDVsAEhNaE.jpg)

行动。构建错误。我需要“推送通知”

我读读。MD，有如何建立应用程序的信息。

> 派生并克隆项目。
> 安装迦太基。如果你使用家酿然后你可以安装迦太基运行 brew 安装迦太基。
> 现在在项目根目录下运行 carthage update。
> 在 XCode 中构建并运行项目。

我在 homebrew 上安装了 Carthage，并执行`carthage update`

之后，“推送通知”错误消失了。

[![iphone](img/79098dcf06534da1864b0cae40401dae.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_jr3KxLe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/Dr1brISVsAALiQH.png)

我在 Mac 上的 iPhone 模拟器上运行它。