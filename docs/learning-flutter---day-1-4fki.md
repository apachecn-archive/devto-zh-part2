# 学习颤振-第 1 部分-简介

> 原文：<https://dev.to/rhymes/learning-flutter---day-1-4fki>

我今天通过 Udacity 的课程[用 Flutter 构建原生移动应用](https://www.udacity.com/course/build-native-mobile-apps-with-flutter--ud905)开始学习 Flutter。该课程使用 Android Studio，但我完全按照 Visual Studio 的代码进行，没有任何问题(尽管有些东西不工作，比如伟大的[小部件检查器](https://flutter.io/inspector/))。声明:到目前为止，我没有写过任何移动应用程序(原生或其他)，我对 Android 或 iOS 开发一无所知。在开发世界的这一方面，我完全是个新手。

*快速提示:前几天已经装了 Flutter。我不得不安装 Flutter SDK，Android SDK，Android Studio，为 Android 构建仿真器映像，为 Flutter 安装 XCode 和各种插件。这花了你可以想象的时间:-D*

本帖经过第一课 1 至 9 集(共 2 课):

[![](img/29613a8999feeeea65bdf51bf5102b4a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rY91wEk---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p3md37fmwqb4v8unsz26.png)

## 随机标注

移动开发可能比以往任何时候都快，但与 web 开发相比仍然很慢，甚至是前端 web 开发。

超级慢的东西是启动时间，热重装足够快，考虑到它必须在连接到 USB 的手机上重新绘制应用程序的一部分。初始化速度很慢:

[![](img/2c2a38d7025b978ab0ccccb7aa19e4c8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eu3_5OfG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o3a2q5rggvyomp7vlwvc.png)

从我输入`flutter run`到手机上的应用程序启动，超过 4 分钟。希望你只需要做一次，然后你就可以使用热重装。这是标准的 Flutter 应用程序在创建时生成的:一个递增计数器的按钮。

幸运的是，第二次，因为依赖关系已经被解决，所以*只需要* 45 秒就可以出现在物理设备上。还不错。我有一个 Nexus 5X 与安卓奥利奥(API 27)。

我还用安卓自己的模拟器(Nexus 5X API 28)测试过，它启动起来慢得可笑。一旦模拟器启动，那么它就可以被 Flutter 检测到，应用程序第一次出现需要 78 秒。最后，我用 iPhone 模拟器(iPhone XS Max)做了同样的事情，第一次用了 80 秒，第二次用了 25 秒。非常“科学”的结论:Android 模拟器启动慢，iOS 模拟器好很多，移动构建慢，我说过吗？不知道你们这些移动开发者每天是怎么做到的。长话短说:如果可以的话，使用设备而不是模拟器。

Visual Studio 代码有一个扩展，基本上嵌入了所有 Flutter 的工具，但要知道这个扩展只是 Flutter 自己的 CLI 工具之上的一个 UI。他们是如此强大。所以你可以保留你喜欢的编辑器来开发。

您还可以从 VS 代码(和命令行)启动测试，这非常酷。测试看起来像这样:

[![](img/108646e9785e6bd20b9c0813d376a018.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_3WiOmk8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bcpwtgxmgb7fgw3lt7yf.png)

我试过了，他们通过了。我已经开始上课了。

## 设置

我目前有开放的 Visual Studio 代码来输入内容，还有 Android Studio 来运行应用程序。这有点适得其反，但我还没有准备好接受 Android Studio，在它里面呆了五分钟后，我宣布它是一个很好的 IDE，但却是一个糟糕的文本编辑器。幸运的是，它们彼此同步得很好，因为它们检测到项目文件夹中的文件变化。

[![](img/217c296d717ec8797cb9fb6d896a2c09.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--E_jDvKvP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/59zp6635qjzra38ldtqu.png)

## 好的，坏的，不知道的

**好事:**

*   Flutter 使用反应式框架和“区分”来决定重画什么。热重装帮助很大，它的速度足够快速的反馈循环。它让你想起什么了吗？喜欢 Vue 还是用 webpack 的热重装:D 反应？
*   内置工具很棒。他们肯定收到了备忘录，2018 年你不能发布任何低于标准的东西。五秒钟的注意力持续时间。
*   Dart 已经接受了一种可能的格式，我认为这很好。我们读代码比写代码多得多，知道你会找到一种熟悉的编码风格可以节省你的时间。你的编程技能不是由空格、制表符和缩进来表示的，而是由你如何设计和编写代码来表示的。我们建立人工智能来帮助我们检测迷因中的猫，我不知道我们为什么还在争论代码风格，让机器去做吧。
*   Dart 看起来不错，但是(我知道我不应该这么说)我现在没有兴趣阅读它的手册。我现在的目标是学习如何构建第一个跨平台的原生移动应用程序，而不会掉进兔子洞或者搬起石头砸自己的脚。达特不会妨碍我，但我也是第一天踏上旅程，所以明天我可能会改变主意。
*   Flutter 的 [Widget inspector](https://flutter.io/inspector/) (不能通过 VS 代码获得)很厉害！您可以激活小工具选择模式，点击手机上的小工具，并在小工具树中查看它的位置，反之亦然:点击小工具树，并查看手机应用程序中选择的小工具(！！).你也可以跳转到声明小部件的代码部分，你可以检查应用程序的性能，进入你正在使用的小部件的源代码和其他东西。这也让我想起了 Vue 针对 Chrome/Firefox 的开发工具。
*   有一个代码库。我花了几分钟在 iOS 上测试同一个应用。这款应用没有做任何事情也是事实，但这仍然是一个巨大的卖点。我遇到过一些公司，他们都有独立的移动开发团队(或者至少是独立的代码库)来开发本质上相同的应用。对我来说，这太奇怪了。老实说，这是我迄今为止对移动开发不感兴趣的原因之一。Flutter 的承诺(我猜 React Native 的也是)也是节省大量时间和金钱的承诺之一(尽管我在这里和那里看到一些公司从 React Native 后退的文章)。只有时间能告诉我扑:D 会发生什么
*   你在一个项目中运行它，它升级软件包，你在一个项目之外运行它，它自己更新 Flutter

**“我不知道”是好是坏:**

*   Flutter 使用独立于主机操作系统的 2D 引擎来渲染窗口小部件，这意味着(或者至少我认为这意味着)它们将始终在原生窗口小部件之后(在 Android 上可能会更少，因为它们来自同一家公司，但我想每次 Android 或 iOS 有新东西时，它们都必须从头实现窗口小部件)。我相信如果 Flutter 成为主流，社区会有所帮助。
*   一切都是小部件，小部件是声明性的。我把这个放在这里，因为我在理论上非常喜欢它，但我从来没有开发过移动应用程序，所以我不确定当你是设计师等团队的一部分时，这有多好。“标准的”Android 或 iOS UI 开发是如何工作的？你有没有给设计师一些工具，让他们建立各种各样的屏幕，然后你给它附加行为？还是像在 web 开发中一样，开发人员将模型翻译成实际代码？如果是这样，那么 Flutter 的“声明性变宽”的想法绝对是明智的，否则它可能会使团队的生活变得复杂。
*   虽然 Flutter 支持 Android 和 iOS，但它似乎明显倾向于 Android 和材料设计，但我可能在这一点上是错误的(我也在看谷歌制作的课程，所以这将被考虑在内)。

**坏消息:**

*   某些功能已损坏(Widget 检查器中的一些按钮不起作用)。我用的是最新的 Flutter(测试版)和它附带的最新 Dart(测试版)。
*   Github 上有很多未解决的问题(不是质量的科学指标)
*   回到分号非常困难:-D

好吧。我今天到此为止。我花了几个小时(观看视频加暂停加实验加测试速度加同时写笔记)，我已经在我的智能手机上安装了一个~~有用的~~无用的应用程序。