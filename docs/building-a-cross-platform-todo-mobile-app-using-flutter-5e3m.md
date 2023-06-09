# 使用 Flutter 构建跨平台 todo 移动应用

> 原文：<https://dev.to/runtime-revolution/building-a-cross-platform-todo-mobile-app-using-flutter-5e3m>

<figure>

[![Photo by Andrea Reiman on Unsplash](img/f590b8c22a4c1b3263dff3c4f2e344ae.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--c4kE5qee--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yfuqrhx5j462q3ieyv6d.jpeg)

<figcaption>Photo by Andrea Reiman on Unsplash</figcaption>

</figure>

我玩这个工具已经有一段时间了，但还不足以彻底理解它。所以，当我写这篇文章的时候，我实际上是在学习。这篇文章是关于一个框架，它可以用来使用一个单一的代码库为 iOS 和 Android 构建移动应用。“是反应原生的吗？”—你问。不，不是的。

## 飘起

> 动词。(指鸟或其他有翼生物)不稳定地飞行或快速而轻地拍动翅膀盘旋。

[Flutter](https://flutter.io/) 是谷歌制作的一款工具，用于构建共享相同代码库的 Android 和 iOS 应用。这是一个全新的工具(在撰写本文时，它还在发布预览版 2 中)，旨在与 [React-Native](https://facebook.github.io/react-native/) 、 [Xamarin](https://visualstudio.microsoft.com/xamarin/) 和其他工具竞争。

它比那些竞争对手更好吗？我其实不知道。我对这些工具的体验几乎是不存在的。但是，作为一个时髦驱动的开发方法的超级粉丝，我不得不尝试一下。

我第一次遇到 Flutter 是偶然的:我正在浏览我的 YouTube feed，作为谷歌开发者的 YouTube 频道的订户，我偶然点击了这个 1 分钟的视频建议。这个视频是关于 Flutter 的一个具体特征的，我不记得了。我记得的是，我立刻惊讶于为 Android 构建材料设计应用程序是如此简单。后来我才发现这在 iOS 上也是可行的，在 DartConf 上的一个[视频中。所以我成了 Flutter 最讨厌的布道者。](https://www.youtube.com/watch?v=w2TcYP8qiRI)

Flutter 和用 Flutter 构建的应用程序都是用谷歌开发的编程语言 [Dart](https://www.dartlang.org/) 编写的。他们说，如果你习惯了 JavaScript，Dart 会看起来很熟悉。我不太同意，但我知道什么？

好了，闲聊够了。敲鼓吧，我们开始吧。

首先，我们需要在我们的机器上安装 Flutter。这很简单:

[下载 Flutter SDK](https://storage.googleapis.com/flutter_infra/releases/beta/macos/flutter_macos_v0.9.4-beta.zip) 并解压到您选择的文件夹中。

接下来，我们需要使 flutter 二进制文件可访问。我们应该将它的父文件夹添加到我们的`$PATH`变量中，但是，如果你像我一样懒惰，你可以通过运行以下命令将一个*符号链接(symlink)* 添加到你的`/usr/local/bin`文件夹中:

```
$ ln -s /path/to/extracted/folder/bin/flutter /usr/local/bin/flutter 
```

就是这样！您刚刚在您的`/usr/local/bin`文件夹中创建了一个到 Flutter 二进制文件的*符号链接*。将一个二进制文件放在那个文件夹中，通过调用它的名字就可以在任何地方使用它。

现在，如果您在终端上运行 flutter doctor，flutter 将检查您是否拥有它工作所需的依赖项。它会告诉你安装 Android Studio 和 XCode，以及其他东西。你应该按照[平台指南](https://flutter.io/setup-macos/#platform-setup)安装一切。它还会寻找 IntelliJ IDEA/Android Studio 或 Visual Studio 代码，如果你使用它们中的任何一个，它会推荐为它们安装插件(看一看指南[配置你的首选编辑器](https://flutter.io/get-started/editor/#androidstudio))。

我们现在准备尝试颤振！在终端中，运行:

```
$ flutter create todo
$ cd todo 
```

现在，打开一个模拟器或连接一个设备并运行`flutter doctor`来检查它是否被检测到并准备好使用。如果是，运行

```
$ flutter run 
```

然后*瞧啊*，你有一个正在运行的 Flutter 应用程序。默认情况下，flutter create 命令会生成一个带有浮动按钮的样板应用程序，该按钮会在屏幕中央显示一个增量值。

<figure>

[![Flutter boilerplate counter app running on iPhone XS Simulator.](img/7ed37f10dee213cae3336198bd82fa15.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qGaE7u2g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AaEegE8iHOq6HNxpKhFr84Q.png)

<figcaption>Flutter boilerplate counter app running on iPhone XS Simulator.</figcaption>

</figure>

现在是时候看看我们的应用程序代码了。Dart 使用入口点，也就是说，一切都从这个`lib/main.dart`文件中的`main()`函数开始。打开看看。它应该是这样的: