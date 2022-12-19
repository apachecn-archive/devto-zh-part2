# 服务器端渲染的角度搜索引擎优化[现场演示]

> 原文：<https://dev.to/couellet/angular-seo-done-right-with-server-side-rendering-live-demo-1k32>

[![SEO-Friendly Angular SPA: Universal Server-Side Rendering Tutorial](img/50433b0856258a5af8eeee70cd8199f7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cPoSzXyb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://snipcart.com/media/203638/devdensm-2x.png)

谷歌的 JavaScript 爬行和渲染仍然是一个有点模糊的问题。

网上到处都是相互矛盾的陈述和实验。

那么这意味着什么呢？

作为一名开发者，你需要为 SEO 优化用流行的 JS 框架构建的网站。

记住这一点，下面是我们正在进行的 JavaScript 框架 SEO 问题系列的第三部分:

1.  [构建预渲染 Vue.js 应用](https://snipcart.com/blog/vuejs-tutorial-seo-example)
2.  [用 Next.js 应用 React SEO](https://snipcart.com/blog/react-seo-nextjs-tutorial)。
3.  在这里，我将解决角搜索引擎优化。

更准确地说，我们将使用 Universal 制作一个服务器渲染的 Angular 电子商务 SPA。

以下是我们实现这一目标的步骤:

1.  建立一个角度项目。
2.  创建角度组件。
3.  在我们的 SPA 上启用电子商务功能。
4.  使用 Universal 使我们的 Angular 应用程序搜索引擎优化友好

这应该很好玩！

## 何必纠结棱角分明的 SEO？Angular 不是谷歌支持的吗？

[![angular-seo](img/40362e1c8b2124ad59fded3232eb5eaa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EYIBzYGN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://snipcart.com/media/203693/angular-logo.png)

[Angular](https://angular.io/) 是 Google 工程师在 2010 年开发的开源 JS 框架。

完成你的[无头栈](https://snipcart.com/blog/headless-angular-ecommerce-app)或者任何 [JAMstack](https://snipcart.com/blog/jamstack-clients-static-site-cms) 都是很棒的事情。但是它仍然存在 JavaScript 框架中常见的 SEO 问题。

JS 单页面应用使用 JavaScript 动态地向页面添加内容。这对于 SEO 来说不是最佳选择:爬虫很可能不会运行 JS 代码，因此不会遇到页面的实际内容。

→点击阅读完整的帖子和技术教程