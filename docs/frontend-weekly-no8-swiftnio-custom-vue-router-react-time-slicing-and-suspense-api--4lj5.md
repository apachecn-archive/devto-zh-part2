# 前端+每周第 8 期:SwiftNIO，自定义 Vue 路由器，React 时间分片和悬疑 API

> 原文：<https://dev.to/wxyyxc1992/frontend-weekly-no8-swiftnio-custom-vue-router-react-time-slicing-and-suspense-api--4lj5>

[![周报封面 54.jpg](../Images/63e1861729ccf3283b817bb748c49a9b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HvbsbQLG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://upload-images.jianshu.io/upload_images/1647496-9533a9cf0d76768e.jpg%3FimageMogr2/auto-orient/strip%257CimageView2/2/w/1240)

与前端开发相关的最佳文章、链接和新闻等，每周发布一次。本期所有参考资料均来自[媒体](https://medium.com/@384924552)、[黑客新闻](https://news.ycombinator.com/news)、 [Reddit](//reddit.com) 、 [Twitter](//twitter.com) 、 [MyBridge](//mybridge.co) 等。

更多问题可以在[前端+周库](https://parg.co/U9x)中找到。

## 新闻

*   [苹果开源 SwiftNIO](https://github.com/apple/swift-nio) :在最近的尝试！Swift 东京发布会，苹果公布了 SwiftNIO 项目，一个用 Swift 编写的类似 Netty 的非阻塞跨平台 I/O 框架。SwiftNIO 旨在成为一个异步事件驱动的网络框架，用于开发高性能服务器和客户端。目前在 macOS (10.12+)和 Linux (Ubuntu 14.04)上开发和测试。苹果方面表示:SwiftNIO 是一个跨平台异步事件驱动的网络应用框架，用于快速开发可维护的高性能协议服务器&客户端。

*   [节点 v8.10.0 (LTS) & v9.8.0(当前)](https://nodejs.org/en/blog/release/v8.10.0/):这个版本有很多大的变化。有对 V8、libuv 和 ICU 的更新，npm 中的一个错误修复，以及对 OpenSSL 1.1.0 构建的支持。

*   Chrome 65 中的新特性:CSS Paint API 允许你以编程方式生成图像。服务器定时 API 允许 web 服务器通过 HTTP 头提供性能定时信息。新的 CSS display: contents 属性可以让盒子消失！

## 教程

*   [GraphQL:你需要知道的一切](https://medium.com/@weblab_tech/graphql-everything-you-need-to-know-58756ff253d8):你构建和使用 REST API 已经有一段时间了，不久前才开始听说 graph QL——API 技术领域的一个新热点。本文的目的是强调与 GraphQL 相关的主要特性，并讨论与这一特定 API 规范相关的重要优缺点。更多相关链接可以在[牛逼网站参考](https://parg.co/UXa)中找到。

*   [从零开始构建 Redux 学习 Redux](https://parg.co/Uah):通过一步步构建来了解核心 Redux 库的教程。更多相关链接可以在[牛逼网站参考](https://parg.co/UXa)中找到。

*   JavaScript: Glob Matching 中的算法面试问题:令许多人懊恼的是，在科技面试中问算法问题的做法似乎不会有什么进展。据我所知，越来越多的公司允许人们用 JavaScript 回答算法问题。在本周的文章中，我将遍历一个常见的面试问题(glob matching)，并用 JavaScript 实现该解决方案。更多相关链接可以在[牛逼网站参考](https://parg.co/UXa)中找到。

## 工程实践

*   在 React 中构建项目和命名组件:因为 React 只是一个库，它并没有规定如何组织和构建项目的规则。这很好，因为它让我们可以自由地尝试不同的方法，并调整更适合我们的方法。另一方面，这可能会给 React world 中的开发者带来一些困惑。在这篇文章中，我将展示一些我已经使用了一段时间并且已经很好地扩展的方法。这些方法并没有再造轮子，它们只是把我们在市场上的东西放在一起并加以改进。更多相关链接可以在[牛逼网站参考](https://parg.co/UXa)中找到。

*   [让我们构建一个定制的 Vue 路由器](https://css-tricks.com/build-a-custom-vue-router/) : vue-router 非常出色，它为我们提供了将应用组件映射到不同浏览器 URL 路由所需的项目。但是，简单的应用程序通常不需要像 vue-router 这样成熟的路由库。在本文中，我们将使用 Vue 构建一个简单的定制客户端路由器。通过这样做，我们将了解构建客户端路由需要处理什么，以及哪里可能存在潜在的缺点。更多相关链接可以在[牛逼网站参考](https://parg.co/UXa)中找到。

*   [Web 缓存的隐藏组件](https://parg.co/UFt):缓存让你提高应用处理速度。存储先前获取的数据或计算结果的副本可以提高处理速度。这使得将来的请求能够得到更快的服务。这是一种有效的架构模式，因为大多数程序反复访问相同的数据或指令。它适用于从网络浏览器到网络服务器，从硬盘到 CPU 的所有东西。让我们采用自下而上的方法来理解缓存的各个层次。我们将关注数据可以缓存在哪里，而不是如何缓存。更多相关链接可以在[牛逼网站参考](https://parg.co/UXa)中找到。

## 引擎盖下

*   把你的网络流量变成一台超级计算机:这篇文章的主题是有争议的，因为它讨论了从网站访问者那里获取计算资源。目前围绕基于网络浏览器的加密货币挖掘有很多讨论。大多数人描绘了一幅这种行为的可悲画面；请记住，这些实践可以沿着非常理想的路径发展。我在这里不详细阐述这些论点，我只是描述一种利用资源的方法。更多相关链接可以在[牛逼网站参考](https://parg.co/UXa)中找到。

*   超越 React 16:时间切片和悬念 API : ReactJS 是一个 UI 库，是前端开发人员在构建 JavaScript 应用程序时非常强大的工具。在本文中，我将向您介绍 ReactJS 的一些特性。时间切片允许 ReactJS(现在运行在 React Fiber 上)在空闲回调期间将子组件上的更新计算分成块，渲染工作分散在多个帧上。暂挂特性的简单定义是，ReactJS 可以暂停任何状态更新，直到获取的数据准备好进行呈现。更多相关链接可以在[牛逼网站参考](https://parg.co/UXa)中找到。

*   8 React 条件渲染方法 : JSX 是 JavaScript 的强大扩展，允许我们定义 UI 组件。但是它不直接支持循环或条件表达式。在 React 中使用条件表达式的方式不止一种。而且，与编程中的大多数事情一样，根据您试图解决的问题，有些比其他的更适合。本教程涵盖了最流行的条件渲染方法:If/Else，防止使用 null、元素变量、三元运算符、短路运算符(& &)、立即调用函数表达式(IIFE)、子组件、高阶组件(hoc)进行渲染。更多相关链接可以在[牛逼网站参考](https://parg.co/UXa)中找到。

## 开源

*   Propel:Propel 为 JavaScript 中的科学计算提供了一个 GPU 支持的类似 numpy 的基础设施。JavaScript 是一种快速、动态的语言，我们认为它可以作为各种科学程序员的理想工作流程。Propel 既可以在浏览器中运行，也可以从节点本地运行。在这两种环境中，Propel 都能够使用 GPU 硬件进行计算。在浏览器中，它通过 deeplearn.js 使用 WebGL，在节点上，它使用 TensorFlow 的 C API。

*   这个应用程序提供了连接在 USB 上的 Android 设备的显示和控制。它不需要任何 root 访问权限。它可以在 GNU/Linux、Windows 和 Mac OS 上运行。

*   Xray:Xray 是一个实验性的基于电子的文本编辑器，它是根据我们在 Atom 发布后的四年中所学到的知识开发的。从短期来看，这个项目是一个试验台，可以快速迭代几个激进的想法，而不会危及 Atom 的稳定性。经过几个月的进展，这个库中的代码的长期前景将变得更加清晰。目前，我们的首要目标是快速迭代，尽可能多地学习。