# 前端+每周第 5 期:React 上下文 API &模式进化，用 Webpack 优化 Web，人工智能生成页面

> 原文：<https://dev.to/wxyyxc1992/frontend-weekly-no5-react-context-api--patterns-evolution-web-optimize-with-webpack-page-generation-by-ai-39ac>

[![周报封面 51.jpg](img/d2ab4af817b6c7e989a9ff023fef084b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--39KIrrWG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://upload-images.jianshu.io/upload_images/1647496-f28849fccd2129fa.jpg%3FimageMogr2/auto-orient/strip%257CimageView2/2/w/1240)

与前端开发相关的最佳文章、链接和新闻等，每周发布一次。本期所有参考资料均来自[媒体](https://medium.com/@384924552)、[黑客新闻](https://news.ycombinator.com/news)、 [Reddit](//reddit.com) 、 [Twitter](//twitter.com) 、 [MyBridge](//mybridge.co) 等。

更多问题可以在[前端+周库](https://parg.co/U9x)中找到。

## 新闻

*   [Chrome 将所有 HTTP 网站标记为“不安全](https://parg.co/UXK):谷歌 Chrome now 宣布，从 Chrome 68(将于 2018 年 7 月重新发布)开始，浏览器将把不安全的网站(HTTP)标记为“不安全”，标志着非 HTTPS 网站的终结。开发人员可以使用 Lighthouse 的最新 Node CLI 版本，这是一个用于改进网页的自动化工具，来查找网站使用 HTTP 加载的资源，以及那些资源中的哪些资源可以通过简单地将子资源引用更改为 HTTPS 版本来升级到 HTTPS。

*   [Rollup 现在有了代码拆分](https://parg.co/UXS):从 0.55 版本开始，Rollup 这个 JavaScript 模块捆绑器终于有了代码拆分，这是长久以来最受欢迎的特性请求。长期以来，Rollup 一直是库作者的首选工具——它支持 React、Vue、D3、Moment、Three.js、Ember、Angular、Babylon、Jest、Bootstrap、Leaflet、appellite 和数以千计的其他工具——但代码拆分意味着 Rollup 现在可以成为各种规模应用程序的绝佳选择。

*   [欢迎渐进式网络应用进入微软 Edge 和 Windows 10](https://parg.co/UXq) : Edge 17 将配备服务人员和推送通知，微软商店将开始通过手动提交来列出渐进式网络应用，这是使网络应用像原生应用一样可用的一大进步。在本帖中，我们将快速介绍渐进式网络应用——它们是什么，它们解决的问题，以及我们将如何在 Windows 10 上实现它们。

## 教程

*   [React 16.3 新特性](https://parg.co/UX4) : React 16.3-alpha 刚刚打了 npmjs，可以下载添加到你的项目中，本帖会对这个版本的新特性做一些观察。新的 context API，新的 API 可访问为 React.createContext()，为我们创建了两个组件；删除了新的生命周期方法 componentWillMount、componentWillUpdate、componentWillReceiveProps，引入了 getDerivedStateFromPropsStrictMode 确保您的代码遵循最佳实践，React 开发人员工具的新版本。关于这个的更多参考可以找到[反应参考](https://parg.co/UXI)。

*   服务人员:渐进式网络应用背后的小英雄:服务人员是渐进式网络应用的核心。它们允许缓存资源和推送通知，这是迄今为止让原生应用与众不同的两个主要特征。这篇文章将涵盖后台处理、离线支持、安装期间的预缓存资产、缓存网络请求、安装和基本使用等。关于这方面的更多参考可以在 [PWA 参考](https://parg.co/UX1)中找到。

*   React 的⚛️新上下文 API :这篇文章将介绍 React 中新的上下文 API，它由三个主要部分组成:React.createContext，它被传递初始值，返回一个带有提供者和消费者的对象。Provider 组件在树的更高层使用，它接受一个名为 value 的属性(可以是任何值)。消费者组件用在树中提供者之下的任何位置，并接受一个名为“children”的属性，该属性必须是一个接受值的函数，并且必须返回一个 react 元素(JSX)。关于这个的更多参考可以找到[反应参考](https://parg.co/UXI)。

## 工程实践

*   [GraphQL 应用中的五个常见问题(以及如何修复)](https://parg.co/UXd):在本文中，我们将学习释放 graph QL 的力量，同时避免其缺点。作者谈到的问题包括:模式重复、服务器/客户机数据不匹配、多余的数据库调用、糟糕的性能和样板文件过量。对于每一个问题，我将描述这个问题，然后解释我如何在 Vulcan 中解决它，这是一个 React/GraphQL 开源框架，我在过去的一年中一直致力于这个框架。关于这个的更多参考可以在 [GraphQL 参考](https://parg.co/UX2)中找到。

*   Redux 模块和代码分割 : Twitter Lite 使用 Redux 进行状态管理，并依赖于代码分割。然而，Redux 的默认 API 不是为在用户会话期间增量加载的应用程序设计的。这篇文章描述了我如何在 Twitter Lite 中添加对增量加载 Redux 模块的支持。它相对简单明了，并且经过了几年的生产验证。关于这个的更多参考可以在 [Redux Reference](https://parg.co/UXT) 中找到。

*   Addy Osmani 使用 webpack 优化 Web 性能:现代 Web 应用程序通常使用捆绑工具来创建产品“捆绑”文件(脚本、样式表等)。)经过优化、缩小，您的用户可以在更短的时间内下载。在 webpack 的 Web 性能优化中，我们将介绍如何使用 webpack 有效地优化站点资源。这可以帮助用户更快地加载你的站点并与之交互。Webpack 是当今最流行的捆绑工具之一。利用其优化现代代码的功能，将脚本代码分为关键和非关键部分，并剥离未使用的代码(仅举几个优化例子)，可以确保您的应用程序具有最小的网络和处理成本。关于这个的更多参考可以在 [Webpack 参考](https://parg.co/UXz)中找到。

## 引擎盖下

*   React 中的进化模式:让我们仔细看看 React 生态系统中出现的一些模式。这些模式提高了可读性、代码清晰性，并将您的代码推向组合和可重用性。这篇文章将涵盖条件渲染，传递道具，解构道具，提供者模式，高阶组件，渲染道具等。关于这个的更多参考可以找到[反应参考](https://parg.co/UXI)。

*   [前端面试手册](https://github.com/yangshun/front-end-interview-handbook):与典型的软件工程师工作面试不同，前端面试不太强调算法，更多的问题是关于领域的复杂知识和专业技能——HTML、CSS、JavaScript，仅举几个领域。虽然有一些现有的资源可以帮助前端开发人员准备面试，但它们不像软件工程师面试的材料那样丰富。在现有的资源中，最有用的题库可能是前端开发人员面试问题。而这个帖子几乎是对“前端工作面试问题”的完整回答。更多关于这方面的参考可以找到[牛逼网参考](https://parg.co/UXa)。

*   [你如何训练一个 AI 将你的设计模型转换成 HTML 和 CSS](https://parg.co/UXR) :三年内，深度学习将改变前端开发。它将提高原型开发速度，降低构建软件的门槛。去年，当托尼·贝尔特拉梅利介绍了[的 pix2code 论文](https://arxiv.org/abs/1705.07962)和 Airbnb 推出[的 sketch2code](https://airbnb.design/sketching-interfaces/) 时，这个领域开始腾飞。目前，自动化前端开发的最大障碍是计算能力。然而，我们可以使用当前的深度学习算法，以及合成的训练数据，现在就开始探索人工前端自动化。在这篇文章中，我们将教神经网络如何根据设计模型的图片编写一个基本的 HTML 和 CSS 网站。关于这一点的更多参考可以找到[牛逼的网络参考](https://parg.co/UXa)。

## 开源

*   [Automerge](https://github.com/automerge/automerge) : Automerge 是一个用于在 JavaScript 中构建协作应用的数据结构库，或者是一个类似 JSON 的数据结构，可以由不同的用户并发修改，并自动再次合并。构建 JavaScript 应用程序的一种常见方法是将应用程序的状态保存在模型对象中，例如 JSON 文档，Automerge 的使用方式类似，但最大的区别是它支持自动同步和合并。

*   [winamp2-js](https://github.com/captbaritone/winamp2-js) :这是 Winamp 2.9 在 HTML5 和 JavaScript 中的重新实现。它包括这些功能:解码你的浏览器中的实际皮肤文件，这样你就可以加载你最喜欢的 Winamp 2 皮肤，加载本地音频或皮肤文件通过拖放，弹出按钮，或“选项”按钮(左上角)等。

*   [Rekit #JavaScript#](https://github.com/supnate/rekit) : Rekit 是一个用 React、Redux 和 React-router 构建可伸缩 web 应用的工具包。这是用于创建现代 React 应用程序的一体化解决方案。它帮助你专注于业务逻辑，而不是处理大量的库、模式、配置等。Rekit 使用固执己见的文件夹和代码结构创建应用程序。通过使用面向特征的架构，每个文件模式一个动作，它被设计成可伸缩、可测试和可维护的。这确保了应用程序逻辑被很好地分组和解耦。

*   [WebAssembly studio](https://github.com/wasdk/WebAssemblyStudio):onne studio 学习、教学、工作、玩 web assembly，支持 C++和 Rust。用户可以在浏览器中构建和运行 wasm 项目，也可以运行本地副本，这需要在计算机上安装 node.js 和 webpack。

## 工作生活

*   [N26 如何用 JavaScript &微服务构建一个现代银行软件。](https://parg.co/UXc):帕特里克·库阿(Patrick Kua)，N26 的 CTO，来自德国的成功银行创业公司。Patrick 是一名微服务专家，他在 ThoughtWorks 工作了 13 年，然后决定领导一家现代银行公司的技术团队，该公司已经为超过 500，000 名用户提供服务。这篇文章旨在了解 Patrick 的团队如何选择他们使用的语言和框架，他们如何处理测试、DevOps &持续集成以及他们如何开发微服务。