# 前端+周刊第 6 期:Webpack 4.0.0，保护你的 GraphQL API，为什么去中心化很重要

> 原文：<https://dev.to/wxyyxc1992/frontend-weekly-no6-webpack-400-securing-your-graphql-api-why-decentralization-matters-2dfl>

[![周报封面 52.jpg](img/176c8876860a0f61e176780888f4b7d3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cpOQ6kpS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://upload-images.jianshu.io/upload_images/1647496-2555c0980f838e48.jpg%3FimageMogr2/auto-orient/strip%257CimageView2/2/w/1240)

与前端开发相关的最佳文章、链接和新闻等，每周发布一次。本期所有参考资料均来自[媒体](https://medium.com/@384924552)、[黑客新闻](https://news.ycombinator.com/news)、 [Reddit](//reddit.com) 、 [Twitter](//twitter.com) 、 [MyBridge](//mybridge.co) 等。

更多问题可以在[前端+周库](https://parg.co/U9x)中找到。

## 新闻

*   [Webpack 4.0.0 发布](https://parg.co/U1f):本周，Webpack 4 (Legato)上市；webpack 4 中有如此多的新东西，而且速度很快(最多快 98%)。Webpack 4 已经弃用并删除了 CommonsChunkPlugin，并替换为一组默认的和容易重写的 API，称为 optimize.splitChunks。它还为您的配置引入了一个新的属性，称为 mode，用于构建大小(生产)优化或构建时间(开发)优化。更多的修改日志可以在[这个](https://parg.co/U1N)中找到。

*   Parcel v1.6.0 发布版 : Parcel v1.6.0 是在 v1.5.0 发布仅 3 周后发布的一个巨大版本，包含超过 15 个新特性和大量的错误修复和改进。亮点包括:零配置 ES6+ transpilation with Babel，自动 JSX 支持 React 和 Preact，节点和电子目标，生产捆绑统计，节点 6 支持，W3C WebManifest 支持。

*   npm v5.7.0 版本:这个版本带来了一系列令人兴奋的新特性和错误修复。Package-lock git 合并冲突解决方案允许 npm install 修复包含合并冲突的 package-lock.json 和 npm-shrinkwrap.json 文件，而无需编辑它们。新的 npm ci 命令仅从您的锁定文件安装。如果您的 package.json 和您的锁文件不同步，那么它将报告一个错误，它也快得多(2-10 倍！)比 npm 安装的时候不用 node_modules 启动。它的工作原理是扔掉你的 node_modules 并从头开始重新创建它。

## 教程

*   [关于 CSS 变量你需要知道的一切](https://parg.co/UIJ):大多数编程语言都有对变量的支持。但可悲的是，CSS 从一开始就缺乏对原生变量的支持。网络发展很快。我很高兴地告诉大家 CSS 现在终于支持变量了。在本指南中，我将向您展示变量如何在 CSS 中自然地工作，以及如何使用它们来使您的生活变得更加轻松。最后，我将向您展示如何构建 3 个展示 CSS 变量及其易用性的项目:使用 CSS 变量创建组件变体，使用 CSS 变量创建主题样式，构建 CSS 变量展台。更多相关链接可以在[牛逼网站参考](https://parg.co/UXa)中找到。

*   [2018 年 JavaScript 测试概述](https://parg.co/U14):本指南旨在让你了解 2018 年 JavaScript 测试最重要的推理、术语、工具和方法。它结合了许多优秀文章中的信息，这些文章在底部链接，并添加了我们自己在 Welldone 软件解决方案方面的经验，我们多年来为不同的产品实施了不同的测试解决方案。他使用 Jest 进行单元和集成测试，使用 TestCafe 进行 UI 测试。关于这个的更多链接可以在[牛逼 JavaScript 参考](https://github.com/wxyyxc1992/Awesome-Reference#javascript)中找到。

*   [面部识别:使用 React-Redux 和 Kairos 来识别自己](https://parg.co/U1M):我们正处于 Face ID 时代，面部识别不再是一个可能或简单的好功能。这已经成了镇上的热门话题。因此，作为一名开发人员，我的好奇心被试图创建一个基于人脸识别的应用程序并测试这个显然已经赋予人类的令人敬畏的功能的前景所增强。关于这个的更多链接可以在[数据科学思维导图](https://parg.co/Ual)中找到。

## 工程实践

*   [Streaming Server-Side Rendering and Caching at Spectrum](https://zeit.co/blog/streaming-server-rendering-at-spectrum):React 16 引入了 Streaming Server-Side Rendering，它允许你在渲染 HTML 的同时以块的形式发送 HTML。这使得第一个字节和第一个有意义的绘画对用户来说更快，因为初始标记更快地到达浏览器。在这篇客座博文中，他描述了通往高性能服务器的旅程——使用流响应和分布式缓存呈现 React 应用程序。关于这个的更多链接可以在 [React 参考](https://parg.co/UXI)中找到。

*   [保护您的 GraphQL API 免受恶意查询](https://parg.co/U1t):使用 GraphQL，您可以随时准确地查询您想要的内容。这对于使用 API 来说是惊人的，但是也有复杂的安全隐患。恶意行为者可能会提交一个昂贵的嵌套查询来使您的服务器、数据库、网络或所有这些过载，而不是请求合法、有用的数据。在这篇客座博文中，他描述了他们如何保护自己的 GraphQL API 免受攻击者的攻击。关于这个的更多链接可以在 [GraphQL 参考](https://parg.co/UX2)中找到。

*   [事件源 vs CRUD](https://parg.co/U1V) :在许多情况下，事件源与领域驱动设计(DDD)和设计模式 CQRS 相结合，但它与这两个概念只是部分相关。事件源是存储数据的特定程序。与使用关系数据库的传统方法不同，事件源并不持久保存记录的当前状态，而是将单个更改存储为一系列增量，这些增量导致了一段时间内的当前状态。关于这个的更多链接可以在[牛逼软件架构参考](https://github.com/wxyyxc1992/Awesome-Reference#softwarearchitecture)中找到。

## 引擎盖下

*   承诺不够中立:JavaScript 中的承诺会产生影响整个生态系统的问题。在这篇博文中，我将解释其中的一些问题。承诺的基本目的是代表一种最终可以实现的价值。它可能在下一个事件循环或接下来的几分钟内变得可用。基本上承诺不够中立，因为它们引入了 4 种观点:渴望，而不是懒惰；没有取消；从不同步；then()是 map()和 flatMap()的混合。关于这个的更多链接可以在[牛逼 JavaScript 参考](https://github.com/wxyyxc1992/Awesome-Reference#javascript)中找到。

*   CSS Keylogging:CSS key logging 包含一个 chrome 扩展和 Express 服务器，利用 CSS 的 key logging 功能。这个攻击真的很简单。利用 CSS 属性选择器，可以在加载背景图像的前提下向外部服务器请求资源。使用一个简单的脚本就可以创建一个 css 文件，为每个 ASCII 字符发送一个定制请求。更多相关链接可以在[牛逼网站参考](https://parg.co/UXa)中找到。

*   [为什么去中心化很重要](https://parg.co/UIk):在互联网的第一个时代——从 20 世纪 80 年代到 21 世纪初——互联网服务建立在由互联网社区控制的开放协议之上。在第二个互联网时代，从 2000 年代中期到现在，盈利性科技公司——最著名的是谷歌、苹果、脸书和亚马逊(GAFA)——开发的软件和服务迅速超越了开放协议的能力。相比之下，分散式平台经常推出半成品，没有清晰的用例。加密网络是开发社区拥有的网络的一种强有力的方式，并为第三方开发者、创作者和企业提供了一个公平的竞争环境。关于这个的更多链接可以在[牛逼参考](https://github.com/wxyyxc1992/Awesome-Reference)中找到。

## 开源

*   使用你的文本编辑器在你的浏览器中写作。您在编辑器中键入的任何内容都会在浏览器中立即更新(反之亦然)。

*   Proton Native :在移动设备上，React Native 为我们提供了一种无缝的方式来构建用户界面和管理代码中的状态，所有这些都是跨平台进行的。Proton Native 对桌面的作用与 React Native 对移动设备的作用相同。构建面向桌面的跨平台应用，同时不离开 React 生态系统。Redux 等流行的 React 包仍然有效。

*   [vue-以太坊-ipfs](https://github.com/redacademy/vue-ethereum-ipfs) :是一个分布式应用启动器，有 Vue，以太坊，ipfs。以太坊是一个分布式虚拟机，它向运行你的智能合约的矿工支付 eth 作为回报。IPFS 是一种分布式内容分发网络。Vue 是一个用于构建客户端 webapps 的 javascript 框架。通过将状态保存在以太坊中，并使用 IPFS 来交付 HTML，webapps 可以变得几乎不可破坏！

*   [basics scroll](https://github.com/electerious/basicScroll):带 CSS 变量的移动和桌面独立视差滚动。basicScroll 允许你根据滚动位置改变 CSS 变量。直接在 CSS 中使用变量来制作你想要的动画。受到 skrollr 和带有 CSS 变量的反应式动画的高度启发。