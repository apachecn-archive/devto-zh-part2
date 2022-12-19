# Vue.js 插件入门[生产就绪示例]

> 原文：<https://dev.to/thatfrankdev/getting-started-with-vuejs-plugins-production-ready-example-52j4>

[![Getting Started with Vue.js Plugins [Production-Ready Example]](../Images/b290b3263519fa4c84bc46ed29c50a8c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nxfQeRUJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://snipcart.com/media/203866/vue-js-plugin-1.jpg)

我们< 3 Vue.js 不是秘密。

以至于它是我们自己的[产品重写](https://snipcart.com/blog/progressive-migration-backbone-vuejs-refactoring)的关键部分。

在过去的几个月里，我们已经了解了很多关于 Vue 的知识。从建立 [SEO 友好的温泉](https://snipcart.com/blog/vue-js-seo-prerender-example)到制作[杀手博客](https://snipcart.com/blog/vuejs-blog-demo)或者玩[过渡&动画](https://snipcart.com/blog/vuejs-transitions-animations)，我们已经彻底地试验了这个框架。

但是这里有一个缺失的部分:

大多数框架的支持者将不得不使用 Vue.js 插件。

为了我自己的快乐(希望也是你的快乐)，我制作了一个自定义插件，在一步一步的教程中向你展示它是如何完成的。

我也会用这篇文章来回答重要的 Vue 插件问题:

*   什么是插件？
*   它们有什么用？
*   有哪些流行的 Vue.js 插件？

魔术时间到了！

## vue . js 插件小故事

[![vue-js-plugin](../Images/b290b3263519fa4c84bc46ed29c50a8c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nxfQeRUJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://snipcart.com/media/203866/vue-js-plugin-1.jpg)

### 插件到底是什么？

插件并不是 Vue.js 特有的，你通常会在很多软件中发现它们。根据定义，它们表示提供了一个接口来允许可扩展性。

换句话说，这是一种为应用程序添加全局功能的方式。

在 Vue.js 中，插件应该公开一个带两个参数的`install`方法:

1.  全局`Vue`对象。
2.  包含用户定义选项的对象。

好消息是他们没有那么令人生畏。Vue.js 的基础知识会让你马上开始摆弄插件。

→点击阅读完整的帖子和技术教程