# 引导您的 React 本机应用程序

> 原文：<https://dev.to/kashishgrover/bootstrapping-your-react-native-app-40i8>

[![](img/64c506b239afa8797c6c721141eb4b40.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TWJp6jsn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2Ajmmgs9zRg8P6-wCR4YYmxA.png)

你好。这是我在 Dev.to 的第一个帖子，我很高兴成为你们社区的一员。😇

我玩 React Native 已经有一段时间了，我很喜欢它。我没有一天不在思考这个混合应用的世界已经变得多么酷。目前是 0.55 版本！

你会问，生产应用是围绕 0.55 版本的东西构建的？是啊。所以很多公司都在用。Instagram，Myntra，Treebo，Airbnb，优步，当然还有脸书。但这有什么大不了的？为什么有这么多的炒作，为什么我今天在这里给你写信？

* * *

# 为什么反应土人？

最重要的是，你的应用程序将通过一个单一的代码库隐式支持 iOS 和 Android。你节省了编码的时间，也节省了雇人的钱。

不仅如此，如果你的生态系统已经包含了 React，你可以确保你的大部分应用，无论是 web 还是原生的，看起来和工作起来都是一样的。

[![Everything.](img/36246e3d813c73fc34994683b1eca37a.png)T2】](https://i.giphy.com/media/l0HlxFuQwoTDkwuPe/giphy.gif)

另外，在开始使用 React Native 之前需要了解 React 是一个误区。我从 React Native 开始。:)

# 我就不告诉你怎么做 app 了。

[![Figure it out.](img/0c9afd1d0142c12292b08a09edcc9cf4.png)T2】](https://i.giphy.com/media/xThuWiF9uce739ZX8s/giphy.gif)

好吧，想想办法。即使在今天，我也一直在努力与这么多人相处。Google it，了解如何做事情，访问 Stack Overflow，GitHub 问题，甚至与开发人员一起加入 slack 频道，他们可能会帮助您找出为什么您会陷入困境以及您可以做些什么。和你的同龄人谈谈，因为你的问题有一半是和 JS 有关的。

很多时候，当你在 React Native 中编写应用程序时，你会意识到，你的问题可能还没有正确的答案。但我想在我看来，这是有趣的部分。对于您正在尝试解决的问题，您是否经常看到几周前的问题和几小时前的评论？你并不孤单。

与此同时，世界各地令人惊叹的开发人员正在为您构建一些最令人惊叹的库。以 React 导航为例。它曾经是一个令人讨厌的库，几个月前它还在 0.something.beta27 上，现在它几乎已经是第二版了，几乎包含了你导航时可能需要的所有东西。

这不是很疯狂吗？

* * *

# 我来帮你做决定

[![You see...](img/12a1e184c194003d2a2ed24d2a3a409a.png)T2】](https://i.giphy.com/media/l0MYKzvI7xx5vyhvW/giphy.gif)

在真实的世界里发生了如此多的事情，以至于很难跟踪每一件事情。作为一个刚刚起步的人，你可能会问我——我到底从哪里开始呢？

我只有一个建议——从今天开始，营造舒适的环境。很快就会变得很容易。你会发现自己会经常去脸书的 React Native docs 网站，所以首先要做好标记。

# 开机

**方式一:**安装**世博会的 XDE** 来初始化和运行你的项目——只要访问【https://expo.io/】的[他们就会帮助你。他们的文档是我读过的最好的文档之一。](https://expo.io/)

您还可以全局安装 create-react-native-app (CRNA)和 do

```
$ create-react-native-app my-app
$ cd my-app
$ yarn start 
```

就是这样！你可以继续使用 Expo 的 Android/iOS 应用程序，或者在你的机器上使用模拟器来运行你的新项目。

[![](img/4bd95daafa4c84922fea11b54817983a.png)T2】](https://i.giphy.com/media/jKaFXbKyZFja0/giphy.gif)

深入到你的应用程序开发中，有一天，你可能会意识到你想要构建的这个新功能还没有任何可用于 react native 的 API，你想为 iOS 和 Android 编写自己的原生代码。那一天，你可以随时选择`eject`你的项目。你可以在这里找到完整的教程。🙂

**方式二:裸机**

为了降低依赖性，您还可以使用 **react-native-cli** 启动您的应用程序。

1.  安装 react-native-cli
2.  `$ react-native init AwesomeProject`
3.  `$ cd AwesomeProject`
4.  `$ react-native run-ios`或`$ react-native run-android`
5.  改代码，玩得开心

# **Android/iOS 何去何从？**

当你刚刚起步的时候，不要去想你的目标平台。先想想自己的商业逻辑。但是，即使你没有积极关注这两个平台，也要试着一起构建它们。

不要假设它是跨平台的，你会先做 iOS，后做 Android。的确，它是跨平台的。但是仍然有很多小事情，比如 iOS 和 Android 处理边距的方式不同，或者 Android 使用仰角而不是方框阴影。

尽管这些可能是小事，但同时处理它们才是正确的做法。但归根结底，这取决于你有什么设备。如果你使用 linux/windows/android 生态系统，而没有 Mac 或 iPhone，你显然会为 android 开发。:P

* * *

# 您的应用程序的组成部分

[![](img/31ee5354896d8e7555796a4f4c11c805.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3sPNvaz8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A8GrTZB9vk0NjKoaZA3S_HA.png)

## 组件

从 React Native 自带组件入手，大量使用。你有视图、文本、按钮、可触摸控件、滚动视图、键盘避免视图、网络视图、平面列表——基本上是你的应用程序需要的所有类型的组件。所有的 UI 库都是基于这些组件的。

就像 Bootstrap，Material UI 等。对于 web，您会发现 React Native 也有一些可用的库。其中最受欢迎的就是[原生基地](https://nativebase.io/)。我们 Treebo 也在为 React 和 React Native 构建一个完整的库。我们称它为 [Leaf UI](https://github.com/treebohotels/leaf-ui) ，它是我们为社区准备的开源礼物。

这些 UI 库的全部意义在于，它们简化了应用某种组件的工作——比如 Toast 通知。如果你试着自己做，你可能会花费很多时间。

但是永远记住，不管怎样，简单是关键。依赖越多，跟踪就越难，你也不想陷入依赖地狱。这就是为什么最佳实践是**拥有自己的辅助组件**。例如，创建自己的按钮，该按钮从 React Native 或 Native Base 内部返回 TouchableOpacity。明天如果你找到一个更好的解决方案，你只需要改变一个文件。

[![](img/d3c3b2193b8bccb077000b8beb685330.png)T2】](https://i.giphy.com/media/3VydkbG6UOcUg/giphy.gif)

## 处理图像

图像可能是你的应用程序的一大部分，React Native 有一个 *Image* 组件，可以轻松渲染本地和远程图像。你应该记住的一点是，SVG 没有本地支持，这是一件非常令人难过的事情。:(

我们确实有一个名为 [react-native-svg](https://github.com/react-native-community/react-native-svg) 的可用库，它允许您编写定制的 svg。但是这将对你有很大的限制，因为你不能渲染本地存储的或者远程的 SVG，你必须自己编写它们。渲染 SVG 的一个不太好的替代方法是使用 WebViews。这会影响性能，所以这里最好使用 png 或 JPEGs。

难过？我找到了一个适合我需求的 SVGs 解决方案。这是我不久前发现的一个很棒的 CLI，它可以将 SVG 文件转换成 React Native 组件，你可以在 react-native-svg 中使用。我不知道这种方法的可扩展性如何，也不知道在多少情况下它会工作得很好。但是我用得相当多。:)

对于**图标**，很多人使用字体。你有一个使用字体的名为 react-native-vector-icons 的库。你也可以使用自己的字体！看看 [Icomoon](https://icomoon.io/) 。

## 导航

导航是任何应用程序最重要的方面之一。这也是 React Native 中没有提供给你的东西。

从一开始就这样做是非常重要的，因为你的应用程序的整个框架将依赖于导航。我强烈推荐 [React 导航](https://reactnavigation.org/)。我记得当它还处于测试阶段时，使用它是多么困难。文档也非常混乱，也许对初学者来说仍然如此。从那以后，人们对它做出了巨大的贡献，最近，它发布了候选版本 2。

要从一个屏幕导航到另一个屏幕，只需在组件之间传递`navigation`作为道具，并执行`this.props.navigation.navigate('CoolScreen');`

以 Expo 给你的样板文件为例。

[![](img/843bb1e12f45514925cfcd0f63aab772.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zldRxvUM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AF1L123In07sC-BSNN0kCSA.png)

在这里，你可以看到三个屏幕和底部的一个标签栏。TabBar 是控制这三个视图的导航器。在 React 导航中，您可以嵌套导航器。同样，在任何导航器中，您可以挂载任意数量的屏幕。

所以代码看起来像这样:

[![](img/37dbe4390e6509079c8b7ce3d48d3706.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XJOSMTM8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m5o2x3fpofd6bmcen6tj.png)

在这里，您的 TabNavigator 被用作 RootNavigator 中的一个屏幕。您可以像这样或以任何符合您逻辑的方式嵌套它们。:)

如果你觉得这个库不适合你，还有其他几个选项如下:
—[React Native Router Flux](https://github.com/aksonov/react-native-router-flux)
—[React Native Navigation](https://github.com/wix/react-native-navigation)
—[React Router Native](https://github.com/ReactTraining/react-router/)

## 状态管理

有两种类型的数据控制一个组件:`props`和`state`。`props`由母公司设置，在组件的整个生命周期内是固定的。对于将要改变的数据，我们使用`state`。

你的整个应用程序可以围绕组件特定的状态构建，这些状态通过 props 在孩子和父母之间进行交流，或者你可以从像 **Redux** 和 **MobX** 这样的库获得帮助。还有其他选择，但这两个是最受欢迎的。

这完全取决于你，以及你的应用程序在功能数量上有多大。使用内部状态很容易理解和使用，但是很难大规模地跟踪。想象一下，一百个屏幕使用一百个组件。现在想象用内部状态。这就是 Redux 或 MobX 之类的产品被创造出来的原因。这些位于你的整个应用之上，为你提供商店和动作，你的任何组件都可以在需要的时候使用它们。

MobX 和 Redux 我都喜欢，使用其中任何一个都没问题。使用适合您的使用案例的产品。

## 数据存储

Redux 和 MobX 都为您提供了存储，这些存储基本上是在您调用 API 时存储您的数据的实体。这个存储是跨组件共享的，确保您需要时所有的数据都可用。它们保存在内存中，不会持久。

对于持久数据，您可以照常使用数据库或文本文件。React Native 的 AsyncStorage 是一个工作在数据库之上的 API。它在系统中存储键值实体。你会发现自己大量使用它来存储像认证令牌、持久应用状态、
等东西。例如，跟踪应用程序首次运行时显示的介绍屏幕。

## 造型

造型相当简单。想到 CSS 但是在`CamelCase`而不是`kebab-case`。这可以通过内置的**样式表 API** 获得。

如果你的行动计划还包括使用 react 创建一个 web 应用程序，你可以使用一个流行的库，名为 [Styled Components](https://github.com/styled-components/styled-components) 。这让您可以使用带标签的模板文字和 CSS 轻松编写样式。这导致了样式和组件之间 1:1 的映射，使得跟踪变得更加容易。

样式表道:

[![<br>
](img/173b9feabce4470ea576424a8d289fbd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--a85WtaZ2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0dvfrd0c2lcrg1rf109k.png)

这种风格的组件道:

[![<br>
](img/9f686c331861af758c8472e72dd9eac0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dVS-iiFG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mudbwnu9r4rsvb1tr558.png)

你觉得哪个扩展性更强？😉

## 联网

要在你的应用程序中加载来自互联网的数据，你需要 API 与之对话。React Native 隐式地为您提供了对`fetch`和 WebSockets 的支持。点击阅读完整教程[。](https://facebook.github.io/react-native/docs/network.html)

# 我们来谈谈 DEV 体验

我非常喜欢使用 React Native 构建，主要是因为我的生态系统。在我的主显示器上，我有 VS 代码和模拟器。在我的第二个显示器上，我有我的日志和调试器。

## 项目结构

在你开始开发应用程序之前，弄清楚它的项目结构是很重要的。有两种众所周知的替代方案，它们都有各自的优势和使用案例。

*   **按功能分类的文件夹**

    当你谈论可伸缩性时，这应该是你的首选项目结构。如果你的应用有 50 个特性，你最好把与这些特性相关的文件放在不同的文件夹里。这使得
    查找文件变得超级容易。

[![<br>
](img/e9f1d90d7c4eb73e7ee7932cc8bc2d5a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bRQJC7fo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gi0ghztgm02uhn9o168h.png)

*   **按类型分类的文件夹**

    这是你将在
    样板文件中看到的最常见的结构。为什么？因为样板文件没有足够的代码来通过
    特性分割它。所以在这里，你可以把你的文件分解到组件、容器、
    屏幕、资产等等。所以总的来说，这对于功能较少的小项目非常有用。

[![<br>
](img/630db37adbe4e87d153a5edb3191b5af.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2I0zsbe8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8qzywzwexpzmy4kmuehr.png)

## Bug 追踪

[![<br>
](img/63e7f55125273f815887686134fb226d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Vq1vdnJf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://devhumor.com/content/uploaimg/February2018/DVsMqp0X4AA1Qky.png)

不能掉以轻心地跟踪生产中的 bug。我推荐 React Native 的**哨兵**或者 **Bugsnag** 。甚至 Play Store 也会给你 bug 报告，但是最好有一个地方来跟踪你所有的 bug。

## 调试

[![](img/8a08934dcf51f67fde1706c96d5c8395.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--g9pHBSWd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://devhumor.com/content/uploaimg/July2017/debugging.jpg)

要使用应用内开发工具，你可以在这里浏览文档[中给出的教程。](https://facebook.github.io/react-native/docs/debugging.html)

### VS 代码和 React 原生工具

React Native Tools 对于 VS 代码来说绝对是一个惊人的扩展。所以很多人干脆把 VS 代码当做代码编辑器。有了这样的工具，它可以成为一个调试发电站。

使用 React 原生工具，您可以通过放置断点或监视来轻松调试代码，从命令面板快速运行`react-native`命令，并使用智能感知来浏览 React 原生 API 的对象、函数和参数。

## 林挺

帮你自己一个忙，用棉绒。我用的是 **eslint** 。是的，有时会很烦人，但随着时间的推移，你会自动开始编写正确的代码。这只会防止你犯愚蠢的错误。它会告诉你什么时候你应该把一个组件写成一个功能组件，或者什么时候你定义了一个变量却从来没有使用过它。

保持你的代码库整洁，并成为明天开始与你合作这个项目的新开发人员的好主人。

* * *

有太多的反应是自然的。在这个不起眼的帖子里，我几乎没有试图触及表面。我试着用总结的方式谈论我所知道的事情。我希望这能让你对可能性有所了解，并且这对刚刚起步的人来说已经足够了。

我不是 React Native 方面的专家，在这篇文章中我可能错过了很多东西，但我只想分享我的经验。:)

如果你有任何问题，一定要问我，我会尽力回答你。

## 感谢，人类。现在让我们开始吧。

[![](img/37c6ef59a41cd2eca0c4086db14e8226.png)T2】](https://i.giphy.com/media/U1rlk8zdcAwbm/giphy.gif)