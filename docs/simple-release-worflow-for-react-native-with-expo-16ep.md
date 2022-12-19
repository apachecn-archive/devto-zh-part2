# react native 与 expo 的简单发布工作流

> 原文：<https://dev.to/flexbox/simple-release-worflow-for-react-native-with-expo-16ep>

### 只需按一个按钮

#### 将 React 原生应用发布到 iOS 应用商店和 Android Play 商店需要多长时间

现在是下午 2 点，我的交付经理要求我将我们的 React 原生应用程序发布到 iOS 和 Android 商店。

> “太好了！我很高兴发布我们的应用程序，仅供参考，这将需要一整天的时间。”
> 
> “你说什么？这是**只是按下一个按钮**。
> 
> 我看不出为什么这么长？”

<figure>[![](../Images/a77ca91dadd590750ef88835e7362c0a.png)](http://bit.ly/expo-release) 

<figcaption>我给你画了一张画。我想现在你可以明白为什么这是一个漫长的过程😉</figcaption>

</figure>

首先，有 2 个主要的应用商店(苹果和谷歌)。每个版本的发布过程都有一点不同，测试工作流程也有所不同。在本文中，我将介绍一个特定的场景:**如何通过 expo** 的独立构建发布 React 本地应用程序。

### `create-react-native-app`对`react-native-cli`

如果你负责发布你的 react 原生应用，你需要理解用你的 CLI 生成的 2 个不同的应用架构[。](https://en.wikipedia.org/wiki/Command-line_interface)

#### 创建-反应-原生-应用

根据[官方文档，](https://facebook.github.io/react-native/docs/getting-started.html)这个方案是最简单的一个。您可以在任何没有构建配置的操作系统上运行您的应用程序。不需要 Xcode 或 Android Studio。你只需要安装另一个神奇的工具，叫做 [expo](https://expo.io/) 。

#### react-native-cli

如果您希望为每个平台使用定制组件，或者将 React 本机代码添加到您现有的应用程序中，您可能正在使用这种解决方案。

如果你正在寻找退出世博会后的工作流程，我强烈推荐传奇人物甘特·拉博德的两篇文章🦄

*   [Simple React 原生 iOS 发布](https://shift.infinite.red/simple-react-native-ios-releases-4c28bb53a97b)
*   [简单反应原生 Android 版本](https://shift.infinite.red/simple-react-native-android-releases-319dc5e29605)

### 用 expo 打造独立 app

现在是下午 2 点半，因为有咖啡休息时间，您已经准备好部署了。我们开始吧！

检查依赖项并运行构建:

```
yarn
exp build:ios 
```

第一项任务至少需要 30 分钟。我很幸运，在办公室连接很快。

> 我有一个想法，也许我可以同时运行 android 版本。

坏消息菜鸟。目前使用 expo，你不能同时为 iOS 和 Android 构建。

**35 分钟后**

耶！构建成功！

世博服务器上有应用程序，现在我需要下载。ipa 文件。我是一名开发人员，我不能被网站上的鼠标点击所困扰。让我们打开一个终端，用 curl 下载构建。

```
curl -o app.ipa “$(exp url:ipa)” 
```

**28 分钟后**

下载完成！

现在我需要上传了。带有[应用程序加载器](https://forums.developer.apple.com/thread/64041)的 ipa 文件加载到 apple store 服务器(因为我不需要使用 Xcode 进行独立构建)。

**34 分 55 秒后**

构建上传完毕！

猜猜看？我还不能按下按钮，因为我的应用程序是由机器人分析的。

<figure>[![](../Images/0560c5ece71542a2ca86d0e017779c63.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cjgm22FC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AMrEpUTCJ8sGp2Cq5ufWEDA.png) 

<figcaption>你的建造还不能用于你的 QA 团队</figcaption>

</figure>

**10 分 12 秒后**

我的建造刚刚从试飞中消失了！😱

听我说菜鸟，发布原生应用是一项复杂的任务。你不知道如何在世界上所有的 CDN 上复制你的版本！给我一些时间来处理你的构建。

好消息是，您可以在活动选项卡中跟踪进度。

<figure>[![](../Images/fd8c67837a66ec799153b1e1eac64ab8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bYiBhD_A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2APj6FGs79BSL3q2ggn6xk6g.png) 

<figcaption>仍在处理</figcaption>

</figure>

**大概不到 10 分钟后**

你的 app 又回来了！但是你有一些额外的小步骤要做:提供出口合规信息。很简单，*你只需要按一个按钮*。

<figure>[![](../Images/deb78c0775b573c721d9bbe728625871.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cnSroQ8c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/667/1%2AwORNuR3IXBGjTgsFyWMo9g.gif) 

<figcaption>再按一次 iOS 发布键</figcaption>

</figure>

在接受了合规性之后，你的应用程序就应该对你的测试团队可用了🎉

但是等一下。我不会每次都重复这个工作流程。

朋友们都知道我[是自动化机器](https://www.youtube.com/watch?v=7_CAqtqEaeo)。我写了这个小脚本，用独立的 expo build 来半自动地发布我们的 react 本地应用程序。

创建一个新文件并从终端运行这个脚本

```
./bin/ios 
```