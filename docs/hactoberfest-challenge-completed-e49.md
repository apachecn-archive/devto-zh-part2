# 啤酒节挑战:完成

> 原文：<https://dev.to/subbramanil/hactoberfest-challenge-completed-e49>

## 感谢

*   感谢 DigitalOcean、Github、Twilio 组织#HacktoberFest 活动
*   Thanks to *Beautus S Gumede* for the repo

    ## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ sduduzog ](https://github.com/sduduzog) / [Slim-Launcher](https://github.com/sduduzog/slim-launcher) 

    ### Android Minimalist Launcher

    <article class="markdown-body entry-content container-lg" itemprop="text">

    # Small Launcher

    > All you need is less.

    [![GitHub](img/380368bfe2e00098e9f77dc97498fdb5.png) ](https://camo.githubusercontent.com/ecaf820540248b6680d480ee4f9a364569cda202/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f6c6963656e73652f73647564757a6f672f736c696d2d6c61756e636865722e7376673f7374796c653d706c6173746963) [ ![F-Droid](img/02375b94fbebb75f90102ab7491b2187.png) ](https://camo.githubusercontent.com/e641420437aad7c45fcd7370712ddbc05a809748/68747470733a2f2f696d672e736869656c64732e696f2f662d64726f69642f762f636f6d2e73647564757a6f672e736c696d6c61756e636865722e7376673f7374796c653d706c6173746963) [ ](https://camo.githubusercontent.com/692f63669a72123416878da6afa81668412dc47b/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f7461672f73647564757a6f672f736c696d2d6c61756e636865722e7376673f7374796c653d706c6173746963) [![GitHub stars](img/ac609c848fc0529d9d132849abd3c36e.png) t11]](https://camo.githubusercontent.com/7cdbd15c56168a939ca13d298b81b3bc1c33262c/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f73746172732f73647564757a6f672f736c696d2d6c61756e636865722e7376673f7374796c653d736f6369616c) 

    [![Get it on Google Play](img/f64bdd8338de03ece22d76480ef77d8f.png) ](https://play.google.com/store/apps/details?id=com.sduduzog.slimlauncher) [![Get it on F-Droid](img/a71be2f3fb8b16abd7cdd7399f18959c.png)](https://f-droid.org/packages/com.sduduzog.slimlauncher/) 

    ## Background

    > Slim Launcher only allows you to use fewer applications on your mobile phone. Give you more time to enjoy life. It is also a perfect application of minimalist lifestyle.

    If you think you can come up with a better description, pat and submit a question! I'm not good at marketing

    # Contributing to

    ## Problems

    **1** See what you don't like, or what you can do better. Create a problem for it. Of course, this won't be everything we may agree on. *It's like adding a yellow pop-up window* when the screen of a mobile phone is open, but I'm trying to reason with you and make sure that we are consistent in the development direction of the application.

    **2。** Honestly, I don't always have time or resources to solve the problems that arise …

    </article>

    [View on GitHub](https://github.com/sduduzog/slim-launcher)
*   感谢 Dev.to community，因为没有一天不在**# hatocoberfest**上发帖。这是一种鼓励和提醒。

我最初的计划是为一些存储库的文档提交 PRs。然而，后来我搬到了与 Android 相关的仓库，因为我可以贡献我的一些技能。当我在寻找一个回购时，我看到了这个[帖子](https://dev.to/sduduzog/how-to-write-tests-for-an-android-app-3041)，这个回购成了我参加啤酒节挑战的选择。

### 我为什么选择回购

*   首先，它是一个处于早期阶段的 Android 应用程序。它提供了一个在锻炼我的 android 技能的同时添加功能的机会。
*   我正在根据应用程序的想法开发一个功能。所以公平起见，我要回报一些东西。
*   这个应用程序是用 Kotlin 编写的，它帮助我理解并练习用 Kotlin 编写代码。在我的工作中，我们仍然处于从 Java 到 Kotlin 的早期阶段(不到 5%的进展)。
*   最后，对于帖子中提出的问题，以更实际的方式回答感觉很好。

### 我从中学到了什么

*   多一点 Kotlin，与 java 相比，做几件事是多么简单。
*   Espresso UI 测试:我们计划在工作中增加 Espresso UI 测试，它帮助我练习了一下。
*   持续集成以及如何为 Android 应用集成 [CircleCI](https://circleci.com) 。我以前试过 **CircleCI** ，所以第二次做起来没花多少时间。然而，这次我更加关注配置细节，并学习了一些新技巧。
*   我是通过一个资深同事了解到 [Codacy](https://www.codacy.com) 的，在用这个 app 练习的时候发现它在分析代码质量方面非常有用。
*   Codacy 通过不同的静态代码分析工具提供代码分析，如 PMD、CheckStyle。当我修复由 Codacy 提供的警告时，我有时需要参考 Java/Kotlin 文档来进行修复。我学到了更多关于 Kotlin [具体化类型参数](https://kotlinlang.org/docs/reference/inline-functions.html#reified-type-parameters)的知识。
*   我以前从未用过‘git squash’；我通常创建多个较小的提交，但是当我提交 PR 时，我尝试使用 git squash 将所有较小的提交压缩成一个。

### 我在 PRs 工作时的参考资料列表

*   [测试来自 Android 开发者的数据库](https://developer.android.com/training/data-storage/room/testing-db)
*   [ProAndroidDev 关于测试 Android 架构组件的文章:房间](https://proandroiddev.com/testing-the-un-testable-and-beyond-with-android-architecture-components-part-1-testing-room-4d97dec0f451)
*   [CircleCI 集成](https://circleci.com/docs/2.0/language-android/)
*   [关于 CircleCI 集成的 ProAndroidDev 文章](https://proandroiddev.com/circleci-with-android-continuous-integration-3ecd98f92bd)
*   [Espresso UI 测试](https://developer.android.com/training/testing/espresso/)

### 啤酒节计划

```
- [x] Submit PRs for slimlauncher and get 'hacktober' t-shirt
 - [x] PR-1: Initialize Testing Framework
 - [x] PR-2: CircleCI integration
 - [x] PR-3: UI Testing using Espresso Framework
 - [x] PR-4: Improve Code Quality using Codacy
 - [x] PR-5: ~~Firebase Test lab~~ Grid Layout for listing apps
 - Bonus: Fix Readme
 - Add Screenshots
 - Add Build status
        [![CircleCI](https://circleci.com/gh/subbramanil/slim-launcher.svg?style=svg)](https://circleci.com/gh/subbramanil/slim-launcher)
 - [x] Add comments in [original post](https://dev.to/sduduzog/how-to-write-tests-for-an-android-app-3041)
- [x] Write a post on sharing the HactoberFest experience and Thank the community 
```

### 我的计划从现在开始

这是我第一次通过 Github 为社区做贡献。通过这次经历，我享受了学习的过程和当你提出的改变被合并时的满足感。

我会留意 **Ben** 在*上的帖子，谁在寻找开源贡献者？*并且每月至少贡献一次。