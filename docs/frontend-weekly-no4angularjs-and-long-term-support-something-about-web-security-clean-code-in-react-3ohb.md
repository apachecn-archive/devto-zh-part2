# 前端+每周第 4 期:AngularJS 和长期支持，关于 Web 安全的一些东西，React 中的干净代码

> 原文：<https://dev.to/wxyyxc1992/frontend-weekly-no4angularjs-and-long-term-support-something-about-web-security-clean-code-in-react-3ohb>

[![周报封面 50.jpg](img/587abbf5a5907c7e537a3b35d57bcf97.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3m9Suu9M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://upload-images.jianshu.io/upload_images/1647496-158b53fefd7a44fa.jpg%3FimageMogr2/auto-orient/strip%257CimageView2/2/w/1240)

与前端开发相关的最佳文章、链接和新闻等，每周发布一次。本期所有参考资料均来自[媒体](https://medium.com/@384924552)、[黑客新闻](https://news.ycombinator.com/news)、 [Reddit](//reddit.com) 、 [Twitter](//twitter.com) 、 [MyBridge](//mybridge.co) 等。

更多问题可以在[前端+周库](https://parg.co/U9x)中找到。

## 新闻

*   [宣布 TypeScript 2.7](https://parg.co/UvA) :本周，TypeScript 2.7 发布了，这篇文章整理了一个快速列表来鸟瞰这个版本:更严格的类属性检查，明确的赋值断言，更容易的 ECMAScript 模块互操作性，独特的符号类型和常量命名属性，在观察模式下更干净的输出，更漂亮的输出，数字分隔符，固定长度元组，在操作符缩小和精确的实例以及更智能的对象文字推理。

*   [style-components 3.1.0 可用](https://parg.co/Uvd):该版本引入了新的 CSS 注入机制，这意味着生产中的客户端渲染速度更快，流式服务器端渲染支持实现了更快的首字节时间。styled-components v3.1.0 现在在生产中默认使用 insertRule，这是一种新的、广泛未知的 DOM API，基准测试应用程序的初始挂载时间减少了约 10 倍，重新渲染时间减少了约 20 倍！

*   [稳定的 AngularJS 和长期支持](https://parg.co/UvQ) : AngularJS 是一个非常稳定的构建 web 应用程序的框架，已经被数百万的 web 开发者使用。AngularJS 正在计划一个更重要的版本，版本 1.7，2018 年 7 月 1 日，它将进入 3 年的长期支持期。在 1.7.0 发布后，该团队不打算合并任何需要哪怕是微小的突破性改变的特性或修复。

*   V8 版本 v6.5 :每六周，我们都会创建一个新的 V8 分支，作为我们发布过程的一部分，这篇文章提供了发布前一些亮点的预览。此版本附带了 WebAssembly 代码的流编译，WebAssembly API 提供了一个特殊的函数来支持与 fetch() API 结合的流编译。此外，他们继续致力于拓宽 JavaScript 内置的快速路径，增加了一种机制来检测和防止被称为“去优化循环”的破坏性情况。

## 教程

*   [渐进式 web 应用简介](https://parg.co/ULc):渐进式 Web 应用(PWA)是使用 Web 技术开发移动应用的最新趋势。渐进式 Web 应用程序是一个使用某些技术开发的网站，这些技术使移动体验比普通的移动优化网站更令人愉快。在这篇文章中，作者谈到了渐进式网络应用程序的替代品，渐进式网络应用程序的功能等。关于这个的更多参考可以找到[牛逼 PWA 参考](https://github.com/wxyyxc1992/Awesome-Reference#production)。

*   [使用 Nuxt.js 的服务器渲染 Vue 应用中的路由和路由保护](https://parg.co/UvF):服务器端渲染(SSR)的概念和实现细节对于新手和有经验的开发者来说都是一个挑战。当您不得不做诸如数据获取、路由和保护认证路由之类的事情时，挑战变得更加艰巨。这篇文章将带你了解如何用 Nuxt.js 克服这些挑战。关于这一点的更多参考可以在 [Awesome Vue 参考](https://github.com/wxyyxc1992/Awesome-Reference#framework)中找到。

*   [使用 MobX 状态树进行状态管理](https://parg.co/Uvj):使用 MobX 状态树，我们有了另一个第一流的竞争者，它应该被放入管理状态的候选者中。虽然它不是完全独立的状态管理解决方案(它非常类似于 MobX)，但它与 MobX 有很大的不同，不能将其归类为“MobX”。这篇文章介绍了如何使用 MobX 状态树，并比较了 MobX。关于这个的更多参考可以在[牛逼 MobX 参考](https://github.com/wxyyxc1992/Awesome-Reference#framework)中找到。

## 工程实践

*   用惯用的 JavaScript 实现 Node.js 的现代化:JavaScript 的性能是一个巨大的领域，它比以往任何时候都更快，但仍有一个问题没有得到回答:我们如何决定哪种 JavaScript 更快？在过去，性能建议通常以反模式的形式出现，例如“不要使用 for-each”或“不要使用 try-catch”。快进到 2018 年——node . js 现在也可以由 ChakraCore JavaScript 引擎提供支持，V8 有一个新的优化编译器，所以 JavaScript 开发人员应该专注于编写习惯性、可读性和可维护性的代码，而不是关注底层的编译器。关于这个的更多参考可以在 [Awesome Node.js 参考](https://github.com/wxyyxc1992/Awesome-Reference#webframework)中找到。

*   [用户帐户、授权和密码管理的 12 个最佳实践](https://parg.co/U9A):帐户管理、授权和密码管理可能很棘手。对于很多开发者来说，账户管理是一个没有得到足够重视的黑暗角落。对于产品经理和客户来说，最终的体验往往达不到预期。谷歌云平台(GCP)带来了几个工具来帮助你围绕用户账户的创建、安全处理和认证做出好的决定。这篇文章将列出最佳实践，以确保您拥有一个安全、可伸缩、可用的帐户认证系统。关于这一点的更多参考可以在[令人敬畏的网络安全参考](https://github.com/wxyyxc1992/Awesome-Reference#websecurity)中找到。

*   那个盒子有多大？理解 CSS 布局中的大小调整:Flexbox 和 grid 布局的一个关键特性是它们可以处理在 Grid 和 flex 项目之间、周围和内部分配可用空间。在这篇文章中，我将与你分享一些关于在 CSS 中调整盒子大小的趣事。我从说明书中挑选了一些我认为对理解那个盒子到底有多大至关重要的东西。花些时间通读一下，我想你会发现 Grid 中的大小调整不再神秘！关于这个的更多参考可以在[牛逼 CSS 参考](https://github.com/wxyyxc1992/Awesome-Reference#syntax)中找到。

## 引擎盖下

*   [成为软件架构师的途径](https://parg.co/Uv2):你有没有想过开发人员有什么职业机会？开发人员可能有几种职业道路，本系列讨论如何成为一名软件架构师。这个系列有几个帖子:成为软件架构师的途径，软件架构中的利益相关者，软件架构师的类型，软件架构中的质量属性等。关于这一点的更多参考可以在[令人敬畏的网络安全参考](https://github.com/wxyyxc1992/Awesome-Reference#websecurity)中找到。

*   [如何阻止我从你的网站获取信用卡号码和密码](https://parg.co/Uvz):作者写了一篇文章，描述了我如何分发收集敏感信息的恶意代码。在这篇文章中，他提出了一些实用的建议:不需要尝试和避免第三方代码，在单独的 HTML 文件中捕获敏感信息，在 iframe 中显示该文件，从不同域上的静态文件服务器提供该文件。关于这一点的更多参考可以在[令人敬畏的网络安全参考](https://github.com/wxyyxc1992/Awesome-Reference#websecurity)中找到。

*   React 中的 Clean Code:Bara 的开发人员遵循 Clean Code 重构他的代码库，以使它们变得更好。在文章的最后，作者分享了一些想法:分解组件:每个组件只有一个抽象层次，分解功能:每个功能只有一个抽象层次，使用更具描述性的名称，等等。React 的更多参考可以找到[牛逼 React 参考](https://github.com/wxyyxc1992/Awesome-Reference#framework)

## 开源

*   Kap : Kap 是一个用 web 技术构建的开源屏幕记录器，目前只能在 macOS 上使用。

*   波尔卡:波尔卡是一种极简、高性能的 Express.js 替代品。是的，你说得对，快递已经超快了&没那么大🤔——但是波尔卡表明(不知何故)还有改进的余地！本质上，Polka 只是一个本地 HTTP 服务器，增加了对路由、中间件和子应用程序的支持。就是这样！

*   Callbag : Callbag 是 JS 回调的标准，支持轻量级的可观察对象和可迭代对象， [callbags-basic](https://github.com/staltz/callbag-basics) 是基于 Callbag 标准的微型库。我们也可以阅读安德烈·斯塔尔茨撰写的[文章](https://staltz.com/why-we-need-callbags.html)来了解更多信息。

*   [未声明](https://github.com/thejameskyle/unstated):未声明是另一个状态管理框架，它被设计成建立在 React 组件和上下文已经设定的模式之上。它有三个部分，容器，一个非常简单的类，看起来就像 React。组件，但仅具有与状态相关的位；Subscribe 允许我们传递我们的容器类，并在树和 Provider 中接收它们的实例，在内部存储我们所有的实例。