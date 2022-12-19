# 前端+周刊第 7 期:WebAssembly 架构 for Go，React 悬疑，重新设计 Redux

> 原文：<https://dev.to/wxyyxc1992/frontend-weekly-no7-webassembly-architecture-for-go-react-suspense-redesigning-redux-k9p>

[![周报封面 53.jpg](img/8b6c595b5a71643ddb5ed5c0962eca9b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--a2xdK6iC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://upload-images.jianshu.io/upload_images/1647496-98fded5608f613d5.jpg%3FimageMogr2/auto-orient/strip%257CimageView2/2/w/1240)

与前端开发相关的最佳文章、链接和新闻等，每周发布一次。本期所有参考资料均来自[媒体](https://medium.com/@384924552)、[黑客新闻](https://news.ycombinator.com/news)、 [Reddit](//reddit.com) 、 [Twitter](//twitter.com) 、 [MyBridge](//mybridge.co) 等。

更多问题可以在[前端+周库](https://parg.co/U9x)中找到。

## 新闻

*   [WebAssembly architecture for Go](https://parg.co/UWu):该文档描述了 Go 编译器的新 web assembly 架构(简称“wasm”)的设计决策。我们的目标是在当前的开发周期中将其上游到官方的 Go 资源库，目标是 Go 1.11。WebAssembly 架构将允许 Go 成为 JavaScript 的替代品，用于编写在 web 浏览器中运行的代码。这种新的选择自由有望对整个软件工程生态系统产生积极的影响。

*   [宣布 Flutter beta 1:构建漂亮的原生应用](https://parg.co/Ua1):作为 2018 年世界移动通信大会的一部分，宣布了 Flutter 的首个测试版。Flutter 是谷歌的新移动 UI 框架，帮助开发人员为 iOS 和 Android 制作高质量的原生界面。Flutter 瞄准了移动开发的最佳点:本机移动的性能和平台集成，以及可移植 UI 工具包的高速开发和多平台范围。

*   [PhantomJS:归档项目，暂停开发](https://github.com/ariya/phantomjs/issues/15344):由于缺少主动贡献，PhantomJS 即将归档，停止开发。PhantomJS 版本 2.1.1 将保持最后一个已知的稳定版本，直到另行通知。Chrome 和 Firefox 获得无头模式是 Phantom 可能实现的最终目标，但它在需要的时候很好地实现了自己的目的。谢谢&再见。

## 教程

*   [谷歌发布机器学习课程](https://developers.google.com/machine-learning/crash-course/):作为谷歌向人工智能第一世界转型的一部分，该公司希望广泛开放这项技术。其最新举措包括一个拥有大量资源的新网站，以及之前的内部在线学习课程。“用谷歌人工智能学习”是一个新的教育项目，最初是在去年的 I/O 开发者大会上宣布的。近年来，该公司多次表示，其目标是使人工智能民主化，并让每个人都可以使用它的工具。更多的修改日志可以在[这个](https://parg.co/Ugb)中找到。

*   [探索 ES2018 和 ES2019](http://exploringjs.com/es2018-es2019/toc.html) :这本书讲的是 JavaScript 的两个版本:ECMAScript 2018 和 ECMAScript 2019。

*   [*预演* React 悬疑试玩](https://parg.co/UWr):冰岛 JSConf 上的异步 React 试玩不负众望:时间切片和 React 悬疑即将推出！在这个 300 多行电影搜索演示的演练中，我们学习了 React 悬念 API 的各个方面:简单缓存提供者。SimpleCache、simple-cache-provider . create resource、react DOM . unstable _ deferred updates 等。更多的变更日志可以在[这个](https://parg.co/Ugb)中找到。

## 工程实践

*   [鲜为人知的 CSS 怪癖&高级提示](https://parg.co/UW4):下面你会发现一些最奇怪的 CSS 特性，以及给高级 CSS 用户的提示和技巧。CSS 的另一个实践是 [30 秒的 CSS](https://atomiks.github.io/30-seconds-of-css) ，这是一个精选的有用的 CSS 片段的集合，你可以在 30 秒或更短的时间内理解。更多的变更日志可以在[这个](https://parg.co/Ugb)中找到。

*   [前端案例研究](https://github.com/andrew--r/frontend-case-studies):像“用[框架或技术名称]构建一个简单的待办事项列表”这样的文章不会教你框架或技术如何解决现实世界的问题并大规模工作。这个文档是一个关于现实世界中企业前端开发的技术讲座和文章的精选列表。更多的修改日志可以在[这个](https://parg.co/Ugb)中找到。

## 引擎盖下

*   [讲授纤程:增量协调](https://parg.co/UW5):这是对纤程实现的一个很好的深入探讨。Didact 是一个 DIY 指南来构建你自己的 React，在这篇文章中，我们将重写 didact 系列的大部分代码，以遵循 React 16 新架构。我们将尝试从 React 代码库中镜像结构、变量和函数名。更多的修改日志可以在[这个](https://parg.co/Ugb)中找到。

*   [重新设计 Redux](https://hackernoon.com/redesigning-redux-b2baee8b8a38) :状态管理现在不应该是一个已经解决的问题吗？直觉上，开发人员似乎知道一个隐藏的事实:状态管理似乎比它需要的更难。在本文中，我们将尝试回答一些您可能一直在问自己的问题:您真的需要一个用于状态管理的库吗？Redux 的人气值得吗？为什么或为什么不？我们能做出更好的状态管理解决方案吗？如果有，如何实现？更多的修改日志可以在[这个](https://parg.co/Ugb)中找到。

*   从 JS 追踪到 DOM，然后再追踪回来:在 Chrome 66 中调试内存泄漏变得简单多了。Chrome 的 DevTools 现在可以跟踪和快照 C++ DOM 对象，并显示 JavaScript 中所有可访问的 DOM 对象及其引用。这个特性是 V8 垃圾收集器的新 C++跟踪机制的好处之一。更多的修改日志可以在[这个](https://parg.co/Ugb)中找到。

## 开源

*   [Pandora.js](https://github.com/midwayjs/pandora) :Pandora.js 是一个可管理、可测量、可追溯的 Node.js 应用管理器，以阿里巴巴为代表，以 TypeScript 为动力。基于多年的企业 Node.js 应用管理经验，阿里巴巴的 Midway 团队最终开源了 Pandora.js。它是一个应用程序管理器，集成了许多功能，如监控、调试和弹性。

*   [just-dashboard](https://github.com/kantord/just-dashboard):just-dashboard 可以使用 YAML 或 JSON 文件生成仪表板。由于您的仪表板只是数据，您可以生成它，而不是重复自己。您可以自己生成 YAML 或 JSON 文件，或者在您的 YAML 文件中使用 jq 查询。和一个仪表板，其中包含一个组件，可以从其他 git 获取数据，并把它变成 3 个不同的图表。

*   [提示](https://github.com/terkelg/prompts):轻巧、美观、人性化的交互提示。prompts 没有很大的依赖性，也没有分成十几个只能很好地一起工作的小模块，它使用布局和颜色来创建漂亮的 cli 界面..

*   [filepond](https://github.com/pqina/filepond) :一个灵活有趣的 JavaScript 文件上传插件。