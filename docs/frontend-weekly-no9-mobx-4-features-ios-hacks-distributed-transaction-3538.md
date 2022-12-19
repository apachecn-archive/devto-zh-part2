# 前端+每周第 9 期:MobX 4 特性，iOS 黑客，分布式事务

> 原文：<https://dev.to/wxyyxc1992/frontend-weekly-no9-mobx-4-features-ios-hacks-distributed-transaction-3538>

[![周报封面 55.jpg](img/bcdb3b53815460d797b2b0cc765fb969.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--StI-v5zD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://upload-images.jianshu.io/upload_images/1647496-e1d5b6bb971ea4df.jpg%3FimageMogr2/auto-orient/strip%257CimageView2/2/w/1240)

与前端开发相关的最佳文章、链接和新闻等，每周发布一次。本期所有参考资料均来自[媒体](https://medium.com/@384924552)、[黑客新闻](https://news.ycombinator.com/news)、 [Reddit](//reddit.com) 、 [Twitter](//twitter.com) 、 [MyBridge](//mybridge.co) 等。

更多问题可以在[前端+周库](https://parg.co/U9x)中找到。

顺便说一句，很抱歉我的英语不好。

## 新闻

*   Firefox 59.0 发布版:在 Firefox for desktop 上，我们改进了页面加载时间，增加了注释和裁剪 Firefox 截图的工具，并使在 Firefox 主页上排列顶级站点变得更加容易。使用非主线程绘画(OMTP)为 Mac 用户改善图形渲染(OMTP Windows 版在 Firefox 58 中发布)在 Firefox for Android 上，我们增加了对使用 HLS 协议传输视频的网站的支持。

*   [让我们宣布 ACMEv2 和通配符证书支持](https://community.letsencrypt.org/t/acme-v2-and-wildcard-certificate-support-is-live/55579):我们很高兴地宣布 ACME v2 和通配符证书支持正式上线！凭借今天的新功能，我们将继续通过让每个网站更容易获得和管理证书，来打破 HTTPS 在整个 Web 上的采用障碍。ACMEv2 是我们的 ACME 协议的更新版本，它已经通过了 IETF 标准流程，考虑了来自行业专家和其他组织的反馈，这些组织将来可能会使用 ACME 协议进行证书颁发和管理。通配符证书允许您用一个证书保护一个域的所有子域。

*   [Stack Overflow 开发者调查结果 2018](https://insights.stackoverflow.com/survey/2018) :超过 100，000 名开发者在今年 1 月参加了 30 分钟的调查。DevOps 和机器学习是当今软件行业的重要趋势，开发人员总体上对人工智能提供的可能性持乐观态度，但对人工智能的危险持不同意见。在我们的调查中，Python 在编程语言中的排名已经上升，今年的受欢迎程度超过了 C#，就像去年超过 PHP 一样。Rust 连续第三年成为我们调查对象中最受欢迎的编程语言，紧随其后的是 Kotlin。

## 教程

*   MobX 4:更好、更简单、更快、更小:MobX 新的主要版本已经发布，在这篇博文中，我将重点介绍最引人注目的新特性。无 decorator 语法的 decorator，动态扩展可观察对象，wait when and flow 进一步简化异步流程，onBecome(Un)Observed 自动获取数据源，专用生产 build。更小更快的&。关于这个的更多链接可以在[牛逼链接](https://github.com/wxyyxc1992/Awesome-Links/blob/master/README-en.md)中找到。

*   [解除剪贴板访问](https://developers.google.com/web/updates/2018/03/clipboardapi):在过去的几年里，浏览器已经集中使用 document.execCommand 进行剪贴板交互。有一个被广泛支持的方法来将复制和粘贴集成到 web 应用程序中是很好的，但是这是有代价的:剪贴板访问是同步的，并且只能读取&写入 DOM。这是新的异步剪贴板 API，我们在 Chrome 66 中提供的文本部分。它取代了基于 execCommand 的 copy & paste，后者具有定义良好的权限模型，不会阻塞页面。关于这个的更多链接可以在[牛逼链接](https://github.com/wxyyxc1992/Awesome-Links/blob/master/README-en.md)中找到

*   [比较 APK 大小，对于 Flutter、React Native、Kotlin 和 Java 中的应用程序](https://android.jlelse.eu/comparing-apk-sizes-a0eb37bb36f):有很多方法可以开发移动应用程序，包括直接的平台特定开发。想知道这些库和框架如何影响你的应用程序的大小吗？让我们分析一些用这些方法编写的应用程序的 apk 文件。这些应用程序非常简单，顶部只有一个标题，屏幕中央有一段文字。这将显示，对于一个最少的应用程序，这些框架需要与 apk 一起打包才能运行。关于这个的更多链接可以在[牛逼链接](https://github.com/wxyyxc1992/Awesome-Links/blob/master/README-en.md)中找到

## 工程实践

*   [graph QL 如何取代 Redux](https://hackernoon.com/how-graphql-replaces-redux-3fff8289221d) : GraphQL 是一种服务器端查询语言。Redux 是一个客户端状态管理库。一个如何取代另一个？我们的大部分状态管理代码都是关于合并和改变来自离散 REST 资源的数据，使之适合我们的 UI(reducer、selectors、actions 等)。).我们许多最复杂的状态管理都试图管理异步的本质，为特定的路由(sagas、中间件、thunks 等)以正确的顺序获取所有数据。).关于这个的更多链接可以在[牛逼链接](https://github.com/wxyyxc1992/Awesome-Links/blob/master/README-en.md)中找到

*   [来自 Twitter 的最佳 iOS hacks:一月&二月版](https://parg.co/U2c):过去两个月社区分享了不错的调试技巧、关于改进 Xcode 性能的建议以及在 Swift 中使用功能的巧妙方法。如何提高 Xcode 的性能，如何让你的代码更加易读易懂的小技巧，等等。关于这个的更多链接可以在[牛逼链接](https://github.com/wxyyxc1992/Awesome-Links/blob/master/README-en.md)中找到

*   [加载第三方 JavaScript](https://parg.co/UTU) : Addy Osmani 在一本合适的指南中写了加载第三方 JavaScript 的基础知识，包括隐私、安全和性能问题。第三方脚本提供了广泛的有用功能，使 web 更具动态性、交互性和互联性。在本指南中，我们将介绍如何找到并修复与加载第三方 JavaScript 相关的问题:性能、隐私、安全性、不可预测的意外后果以及关键呈现路径。关于这个的更多链接可以在[牛逼链接](https://github.com/wxyyxc1992/Awesome-Links/blob/master/README-en.md)中找到

## 引擎盖下

*   [分布式事务和为什么要关注](https://parg.co/U2q):这篇文章是对分布式事务的一个很好的介绍，它包括以下内容:分布式数据模型、乐观者的数据模型和悲观者的数据模型；网络上的原子性、多版本并发控制和时钟同步；一致性，两阶段提交协议，PAXOS，RAFT 隔离、快照隔离、可序列化隔离、持久性等。关于这个的更多链接可以在[牛逼链接](https://github.com/wxyyxc1992/Awesome-Links/blob/master/README-en.md)中找到

*   在一个干净的架构代码库中的引导之旅。:最近我发布了一个开源的样例项目，叫做 MovieNight。在过去的几周里，我收到了很多关于应用程序架构的问题，所以我决定写这篇博客。我将描述不同的组件以及它们之间的关系，并讨论我在此过程中做出的一些架构决策。关于这个的更多链接可以在[牛逼链接](https://github.com/wxyyxc1992/Awesome-Links/blob/master/README-en.md)中找到

*   [JavaScript 如何工作:渲染引擎和优化其性能的技巧](https://parg.co/Uz5):然而，当你构建 web 应用时，你不只是编写独立运行的 JavaScript 代码。你写的 JavaScript 是和环境交互的。了解这种环境，它是如何工作的，它是由什么组成的，这将使你能够构建更好的应用程序，并为你的应用程序发布后可能出现的潜在问题做好充分准备。关于这个的更多链接可以在[牛逼链接](https://github.com/wxyyxc1992/Awesome-Links/blob/master/README-en.md)中找到

## 开源

*   Rough.js 是一个轻量级的(~8k)，基于画布的库，可以让你以一种粗略的，类似手绘的风格进行绘制。该库定义了绘制直线、曲线、圆弧、多边形、圆和椭圆的图元。它还支持绘制 SVG 路径。关于这个的更多链接可以在[牛逼链接](https://github.com/wxyyxc1992/Awesome-Links/blob/master/README-en.md)中找到

*   [TOAST UI 图表](https://github.com/nhnent/tui.chart):TOAST UI 图表是一个开源的 JavaScript 库，可以在包括 IE8 在内的遗留浏览器中使用。它很直观，无需任何其他学习就能轻松应用。如果您愿意，您可以使用许多选项和自定义主题来更改图表的细节。关于这个的更多链接可以在[牛逼链接](https://github.com/wxyyxc1992/Awesome-Links/blob/master/README-en.md)中找到

*   fkill-cli : fkill-cli 是一个跨平台的神奇进程杀手，它可以在 macOS、Linux 和 Windows 上工作。要终止端口，请在前面加一个冒号。比如::8080。不带参数运行以使用交互式界面。进程名不区分大小写。关于这个的更多链接可以在[牛逼链接](https://github.com/wxyyxc1992/Awesome-Links/blob/master/README-en.md)中找到