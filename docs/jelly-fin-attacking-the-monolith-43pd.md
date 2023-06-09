# 果冻鳍:攻击巨石柱

> 原文：<https://dev.to/sublimegeek/jelly-fin-attacking-the-monolith-43pd>

嘿果冻！

到目前为止，这是一次多么愉快的经历啊！各行各业的人都对 Jelly Fin 有着惊人的兴趣，能认识有兴趣参与其中的新人真是太棒了。

正如你可能知道的，我们最初是使用 React Native 构建一个移动原生应用程序，我拼命想通过一个简单的冒烟测试获得一个绿色版本。

我小小的“你好，世界”已经超越了它本身，我们作为一个团队，需要退后一步进行评估。我们正在寻找我们的牵引力和重构，以使一切都令人满意。

### 攻击巨石柱

所以，既然我们有一个 GitHub 组织，我借此机会将我们的整体结构分解成几个独立的部分:

*   **果冻鳍**——主项目。将成为 React Native，代表 iOS 和 Android 的原生应用
*   果冻鳍-api -我们的后台。这将包含我们的 FireBase 函数和其他与 API 相关的项目。
*   jelly-fin-web -我们的网络客户端。这将是一个 VueJS 网络应用程序，它将有一个响应式的设计，但也可以作为大型运营的枢纽。

### 我们将推出网络版！

好吧，我对这个有点激动。我本来打算等到我们有了移动版本之后，但是想了想，Jelly Fin(它的核心)可以在两个前沿都玩。

我认为最好的方法是 VueJS。我在过去的工作中使用过 Vue，很容易上手。它借鉴了 Angular 和 React，所以如果您在这两方面都有经验，应该很容易学会。如果你两者都没有经验，Vue 有一些最好的文档。

此外，在几秒钟之内，我就能够启动项目，并拥有林挺、测试甚至预提交格式所需的一切！我已经错过了 JavaScript 工具。

我们仍在积极寻找贡献者，所以如果你想从底层开始，就来看看我们吧！

主要项目

## ![](img/375dfcc32199b4dedf2b526645c27ff7.png) [果冻鳍](https://github.com/jelly-fin) / [果冻鳍](https://github.com/jelly-fin/jelly-fin)

### 预测理财的简单方法。我们应该让我们的钱自动化，而不是让它让我们自动化。

<article class="markdown-body entry-content" itemprop="text">

# 果冻鳍

[![Discord](img/bf8def81f5f6dfa4dd4a20e0d945e47f.png)](https://discord.gg/xveZ3FT)[![Dependabot Status](img/94bd21df2579a5584a03abe562a243fc.png)](https://dependabot.com)[![PRs Welcome](img/0693df776702fb305cf04c68313485c9.png)](http://makeapullrequest.com)[![contributions welcome](img/91facb8c3238bb48c2d06f204f9f4300.png)](https://github.com/jelly-fin/jelly-fin/issues)[![Inline docs](img/f2c655e66aea63967dd96ec6735bc68c.png)](http://inch-ci.org/github/jelly-fin/jelly-fin)[![Build Status](img/5ce9486d2302f941ff02738196df7436.png)](https://travis-ci.com/jelly-fin/jelly-fin)[![codecov](img/d60e2a0922e4863240ff908656054103.png)](https://codecov.io/gh/jelly-fin/jelly-fin)

* * *

财政困难。这是每个人不得不面对的首要问题之一。所以，让我们把它变得简单和自动化。几年来，我和妻子一直用预测方法跟踪我们的财务状况，而且都是在电子表格中完成的。我想采用这个概念，并使用无服务器架构和简洁的设计使其可移动的时候到了。

## 入门指南

这些说明将为您提供一个项目的副本，并在您的本地机器上运行，用于开发和测试目的。有关如何在活动系统上部署项目的说明，请参见部署。

### 先决条件

*   节点 v8.x.x 或使用 [NVM](https://github.com/creationix/nvm#installation)
*   [一个消防基地项目](https://firebase.google.com/console)

**iOS 版**记住，你需要有 Mac OS 才能开发 iOS 版

*   [Cocoapods](https://guides.cocoapods.org/using/getting-started.html) (仅适用于 Mac OS)
*   [Xcode](https://developer.apple.com/xcode/) (仅用于 Mac OS)
*   Firebase 项目的 [GoogleService-Info.plist](https://firebase.google.com/docs/ios/setup#add_firebase_to_your_app) 文件

**安卓系统**

*   …

</article>

[View on GitHub](https://github.com/jelly-fin/jelly-fin)

新闪亮宝贝网络项目

## ![](img/375dfcc32199b4dedf2b526645c27ff7.png) [果冻鳍](https://github.com/jelly-fin) / [果冻鳍网](https://github.com/jelly-fin/jelly-fin-web)

<article class="markdown-body entry-content" itemprop="text">

# jf 网络

## 项目设置

```
npm install 
```

### 为开发进行编译和热重新加载

```
npm run serve 
```

### 为生产进行编译和精简

```
npm run build 
```

### 链接并修复文件

```
npm run lint 
```

### 运行您的单元测试

```
npm run test:unit 
```

</article>

[View on GitHub](https://github.com/jelly-fin/jelly-fin-web)

[![Fork Me](img/5114c2ce5611917c99314858a8073158.png)T2】](https://github.com/jelly-fin/jelly-fin)