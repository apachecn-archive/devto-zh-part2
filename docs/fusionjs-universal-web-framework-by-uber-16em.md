# UBER 开源的 Fusion.js 通用 Web 框架

> 原文：<https://dev.to/bytegasm/fusionjs-universal-web-framework-by-uber-16em>

如果你正在读这篇文章，你已经知道这个城市有一个新的“框架”。这听起来像是 Javascript 世界里的日常新闻，但是在你翻白眼并在 twitter 上咆哮之前，让我们看看所有的*fusion*是关于什么的。

## **反应过来，棱角分明，Vue 和现在融合了吗？**

**不！** Fusion.js 不是 React 的替代品。准确地说，Fusion.js 是建立在 react & redux 之上的**功能丰富的样板文件**，它试图缓解现代单页面 Web 应用程序的主要痛点。

距离 fusion.js 的[发布已经过去了几个小时，所以这不是一个深度的探讨，而是一个简短的总结&对 fusion.js 的第一印象](https://eng.uber.com/fusionjs/)

### **服务器端开箱渲染**

SSR-ing 你的 SPA 总是有点棘手。有各种样板文件为您提供在服务器上渲染应用程序和在客户端上合成应用程序的设置。但这些设置相当固执己见，一段时间后就被遗弃了，让你独自一人。Fusion.js 支持开箱即用的服务器端渲染。也就是说，*同构通用 web 应用*在服务器上呈现，在浏览器和搜索引擎优化友好页面中融合，加载速度更快。

### **代码拆分& HMR 马上**

SSRed react-redux SPA 上的束分裂一直是前端开发人员的*春梦*。但是为它配置 webpack 并让它在所有主流浏览器中正常工作一直是 T2 的噩梦。另外，热模块重装对开发者来说是一件幸事，对吗？Fusion.js 支持这两者。

### **基于插件的架构**

单一入口点架构使 Fusion.js 能够在客户机和服务器上通用地运行它的代码。因此也使得插件具有通用性。它允许插件开发者根据代码所属的库，而不是代码运行的环境，来共同定位代码片段。

通过这张图可以更好地理解这一点:

[![plugins in fusion.js](../Images/c5b7c554081af90788677f5697307c76.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fPtXjcax--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AVst7vvHUUP-Fbzccb9PSxQ.png) 
插件逻辑基于它们的逻辑分组，而不是基于代码需要添加的位置。

### **键入依赖注入**

插件可以将定义良好的 API 作为服务暴露给其他插件，插件的依赖性在测试中很容易被嘲笑。当依赖关系负责与数据存储基础架构通信时，或者当它们与可观察性(例如，日志记录、分析和指标)相关时，这一点尤其重要。

[![dependency injection in  Fusion.js](../Images/f20f5f7aa988c713babdc9acc66f6067.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wlZW7UzD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A4isGmV2nTYUvKdNN-WM2UQ.png)

### **不再快递，欢迎 Koa**

大多数 SSR、代码分割解决方案都是基于 Express 的。Express 有一个 API，它鼓励副作用，这使得复杂的响应转换难以封装和测试。然而，Fusion.js 是基于 Koa 的**，它提供了一个更加**的单元测试友好的基于上下文的 API** ，以及一个优雅的轻量级抽象，用于基于下游和上游概念的请求生存期管理。Fusion.js core 将网络副作用与应用程序状态隔离开来，并利用 Koa 和 DI 来实现子系统之间的松散耦合。**

### **考验你的品味**

Fusion.js 支持现代测试工具，如 **Jest、Enzyme&puppet er**。除此之外，它还提供了测试插件的工具。fusion-test-utils 包允许模仿服务器本身，使得在插件和模仿的任何排列之间快速运行集成测试成为可能。

### **造型呢？**

Fusion.js 可以使用任何 CSS 框架或库。默认情况下，Fusion 支持 **[Styletron](https://github.com/rtsao/styletron)** ，这是一个由 Fusion.js 团队维护的高性能原子 CSS 库。

### **基于组件的路由**

融合应用可以使用**fusion-plugin-react-router**将路由功能集成到组件树中。该插件使用 react-router，并公开了一个类似的 API，允许您在组件树中的任何位置添加路由行为。

### **其他好东西**

*   安全性:在端点上自动设置 CSRF 保护。
*   数据获取:支持 RPC 驱动的可组合数据获取和 GraphQL/Apollo
*   质量指标:轻松使用服务器性能和浏览器性能日志、错误日志和通用事件流
*   I18N:自动设置高效的支持束分裂的翻译加载

### **我的 2 分钱:**

大公司的开源项目总是让我心里暖暖的。作为一名前端开发人员，在 react、redux & express 上设置 SSRed、代码分割设置是我面临的最大挑战之一。所以，看到这样的设置开源，这也是优步大规模使用，真的很好。

说到开源， [dev.to 将于 8/8/18](https://dev.to/ben/the-devto-codebase-will-go-open-source-on-august-8-2kia) 开始开源。耶！

我们已经有了惊人的现代网络设置，如 [Next.js](https://github.com/zeit/next.js/) 、[盖茨比](https://www.gatsbyjs.org/)、[电极](http://www.electrode.io/)，越多越好。我迫不及待地想在我的下一个项目中使用 Fusion，并了解它的效果。如何看待 Fusion.js？