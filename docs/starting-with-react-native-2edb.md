# 从 React Native 开始

> 原文：<https://dev.to/nedimb86/starting-with-react-native-2edb>

因为我是 React 开发人员，所以我检查 React Native 只是时间问题。我第一次接触它并不顺利。我不是说写代码，而是开始演示项目。

我承认。一切都是我的错。试图表现得太聪明，仅仅浏览文档就足以浪费几个小时。需要说明的是，我试图在 Windows 的 Android 模拟器上运行 react-native-cli 生成的“AwesomeProject”。如果您试图使用“最新”版本，这个简单的任务就不那么简单了。

在从一个老朋友那里得到建议的时候，我看到了一个引用官方[文件](https://facebook.github.io/react-native/docs/getting-started.html)的评论，声明我需要使用 **Android 6.0** 。我受够了，回到了根本，严格按照指示去做，信不信由你，一切都很好。

几个月后，我发现自己试图帮助其他人从 React Native 开始，于是就有了这篇文章。那么，应该如何开始呢？

我发现了三种不同的方法，我将在这里介绍:

1.  创建-反应-本机-应用程序
2.  反应-本机-cli
3.  ignite-cli

### 创建 React 原生 App

对于想了解 React Native 基本概念的初学者来说，这已经足够了。你所需要的只是你机器上的[节点](https://nodejs.org/en/download/)和你设备上的 [Expo](https://expo.io/) 客户端应用程序，你就可以开始了。下一步是运行 npm i -g create-react-native-app。一旦这样做了，你将需要运行

```
create-react-native-app MyReactNativeApp

.........

cd MyReactNativeApp npm start 
```

快到了。在你的设备上启动 Expo(确保你的机器和设备在同一个 IP 地址上)扫描二维码。恭喜你！你刚刚进入了 React Native 的世界。

那么有什么问题呢？

嗯…由于没有构建任何本机代码，您可以做的事情有一些限制。不要担心，你将能够做相当多的事情，并且一旦你需要提高等级，你可以“弹出”项目并创建本地构建。这带来了必须安装 Xcode(用于 iOS)和 JDK 以及 Android Studio(用于 Android)的代价，此外还有一些不同于 macOS、Windows 和 Linux 的其他要求。

### 反应原生 CLI

再来一次。确保遵循[文档](https://facebook.github.io/react-native/docs/getting-started.html)中提到的说明。因此，一旦您完成了“特定于操作系统的安装”并通过运行 npm i -g react-native-cli 在全局范围内安装了 cli，您就可以创建您的第一个项目了。

```
react-native init MyReactNativeApp

.......

cd MyReactNativeApp

----------

react-native run-ios

------or--------

react-native run-android 
```

就这么简单。在运行 Android 应用程序之前，请确保您的 Android 模拟器正在运行或者您的设备已经连接。

### 点燃 CLI

即使对于 [Ignite](https://github.com/infinitered/ignite) ，您也需要完成与 react-native-cli 相同的设置。创建项目比使用 react-native-cli 要复杂一些。首先在全局范围内安装 npm i -g ignite-cli。那就让它发挥它的魔力吧

```
ignite new ILoveIgniteApp 
```

系统会提示您几个是/否的问题，让您选择要安装的内容。

那么，你为什么会选择看起来更复杂的东西呢？

对于初学者来说，ignite 附带了一组样板文件，您可以像运行 ignite new MyNewAppName -b '样板文件的名称'一样简单地进行安装。此外，ignite 还可以轻松添加许多插件(npm 包)。对于初学者来说，它可以成为学习如何组织项目的来源。Ignite 会生成代码，并明确说明如何修改代码，以便应用程序能够正常工作。对于高级用户来说，在添加与本机相关的依赖项(如 google maps)时，使用一些复制粘贴过程可以节省时间。

### 结论

你迟早需要设置环境，要么用原生模块扩展你的应用，要么为 AppStore 或 PlayStore 或两者构建它。即使您可以使用 Expo 和物理设备来创建-反应-本机-应用程序，您也可以使用模拟器来运行您的应用程序。因此，不要推迟这个无痛、耗时的过程。

对于我来说，我喜欢使用 create-react-native-app 来尝试任何与非本机模块相关的东西。说到项目，我更喜欢 ignite-cli，因为它可以加快我的工作速度。但是，当第一次使用本机模块或使用它们时，我喜欢用 react-native-cli 创建一个新项目，以确保我不会错过 ignite 将为我做的任何事情。

只要你选择了任何一种方法，你选择什么方法都没关系。