# Vue.js SEO 友好的 spa:提示、工具和先决条件示例

> 原文：<https://dev.to/couellet/vuejs-seo-friendly-spas-tips-tools--prerender-example-2kmh>

[![Vue.js Tutorial: An Example to Build and Prerender an SEO-Friendly Site](../Images/a95d034a484558777a5cb1c95afeaf47.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--s3KrwROh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://snipcart.com/media/203838/vue-js-seo-prerender-demo-1.jpg)

你知道 Hulu 的 JavaScript 失败故事吗？

回到 2016 年，由于糟糕的 JS 处理，在线视频流媒体服务经历了 [56%的可见性下降。](https://www.linkresearchtools.com/case-studies/hulu-javascript-fail-visibility-drop/)

没有企业愿意经历的噩梦。

[![enter image description here](../Images/99fa3af86acc6b8f4ab35a48b7cd3d0e.png)T2】](https://i.giphy.com/media/Qa5dsjQjlCqOY/giphy.gif)

为了避免这样的灾难，在使用现代 JavaScript 框架时，您必须知道自己在做什么。

在 Snipcart 这里，[我们喜欢 Vue.js](https://snipcart.com/blog/progressive-migration-backbone-vuejs-refactoring) ，但是完全意识到 Vue.js 单页应用程序的 SEO 问题。

在这篇文章中，我想向 JS 开发者展示让 Vue SEO 友好是多么容易。

我要说的是:

*   你应该经常应用的一般 SEO 技巧。
*   具体 Vue.js SPA SEO 问题。
*   通过服务器渲染和预渲染来修复它们的工具。
*   使用`prerender-spa-plugin`的技术 Vue.js SEO 示例。

> 这是我们的 Vue.js SEO 资源的完全更新版本。第一篇文章之后是另外两篇关于 JS 框架 SEO 问题的文章，分别是[的 React](https://snipcart.com/blog/react-seo-nextjs-tutorial) 和 [Angular](https://snipcart.com/blog/angular-seo-universal-server-side-rendering) 。你可以在这篇文章的结尾的[找到这个内容的视频版本。](#demo)

阅读其余的帖子和技术教程[在这里](https://snipcart.com/blog/vue-js-seo-prerender-example)