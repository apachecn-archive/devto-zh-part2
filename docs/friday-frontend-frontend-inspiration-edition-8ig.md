# 星期五前端:前端灵感版

> 原文：<https://dev.to/kball/friday-frontend-frontend-inspiration-edition-8ig>

我今天尝试了一些新的东西——为时事通讯录制一个简短的视频。这只是一个简短的 2 分钟，关于在前端世界中让我兴奋的事情。看看吧，让我知道你的想法，或者点击回复，或者更好的是在视频上留下评论。

[https://www.youtube.com/embed/wNWMmtRbEws](https://www.youtube.com/embed/wNWMmtRbEws)

尽情享受吧！

来自 ZenDev 的 KBall

#### CSS&SCS

##### [CSS 网格布局 vs CSS 框架辩论](https://www.sitepoint.com/css-grid-layout-vs-css-frameworks-debate/)

“现在我们有了 CSS Grid 和 Flexbox，学习/使用 CSS 框架还有意义吗？”这篇文章深入探讨了这个常见的问题，并且很好地解释了为什么学习和使用 CSS 框架仍然是有意义的。个人观点——我认为 CSS 框架的下一次迭代将利用 CSS Grid 的强大功能，使网格变得不那么结构化，更像组件，让您能够超快速地访问一系列最佳实践模式。

##### [深入了解 CSS 中的通用字体名称](https://www.impressivewebs.com/deeper-look-generic-font-names-css/)

当你在 CSS 中指定`font-family: Arial, Helvetica, sans-serif;`时，实际上发生了什么？像`sans-serif`这样的通用字体有哪些？浏览器如何决定为一个类属显示什么？如果你曾经问过类似的问题，这篇文章会帮你解决。另外，看看新出现的字体家族，如`emoji`和`math`！

##### [CSS 画图 API 的 polyfill，带有特殊的浏览器优化。](https://github.com/GoogleChromeLabs/css-paint-polyfill)

我以前写过一些关于胡迪尼的文章，这是一个即将出现的 API 星座，支持基于 JavaScript 的 CSS 属性定义。这就有可能达到这样一种状态，即我们永远不会有我们不能使用的 CSS 属性——当新的属性被定义时，它们可以被聚合填充到浏览器中，直到浏览器可以实现为止，就像我们如何使用 Babel 来实现新的 JavaScript 特性一样。即将发布的第一个胡迪尼作品是 paint API，目前只有 Chrome 版本，但是看这里，这是一个 API 的聚合填充！

##### [如何创建一个简单的 CSS 加载微调器&使其可访问](https://codeburst.io/how-to-create-a-simple-css-loading-spinner-make-it-accessible-e5c83c2e464c)

我是一直使用基于 gif 的旋转器的守旧派，不知何故，我没有将 CSS 动画现在得到足够广泛支持的点连接起来，以实现纯粹的基于 CSS 的旋转器。这真是太好了！创建一个可重用的 scss mixin 的一步一步的指南。

#### JavaScript

##### [在 JavaScript 中调用纯函数和函数组合](https://scotch.io/tutorials/wielding-pure-functions-in-javascript-and-function-composition)

我认为没有什么比学习函数式开发的原则更能提高我的编程水平了。纯度的概念是这种工作流的关键组成部分之一，这是一个很好的演示，向您展示了如何使用纯函数来创建更强大和可重用的 JavaScript。

##### [添加`v-model`支持自定义 Vue.js 组件](https://scotch.io/tutorials/add-v-model-support-to-custom-vuejs-component)

添加 v-model 支持是创建“感觉”像本地 Vue 组件并易于其他人开箱即用的组件的关键部分。即使您没有构建要共享的组件，了解 v-model 如何在幕后工作也会帮助您编写更好的 Vue 代码。

##### [第一眼:棱角分明的常春藤](https://www.telerik.com/blogs/first-look-angular-ivy)

我个人并不像 React 和 Vue 那样密切关注 Angular，但这个名为 Ivy 的新渲染器有一些有趣的东西。提前编译并且只发布任何特定应用程序所需的框架部分的能力非常酷。这篇文章很好地介绍了新的渲染器，对于那些更熟悉 Angular 的人来说，Angular 深度博客上还有一个很棒的[对](https://blog.angularindepth.com/ivy-engine-in-angular-first-in-depth-look-at-compilation-runtime-and-change-detection-876751edd9fd)的[帖子](https://blog.angularindepth.com/angular-ivy-change-detection-execution-are-you-prepared-ab68d4231f2c)。

##### [如何反应和解工作](https://css-tricks.com/how-react-reconciliation-works/)

对 React 的虚拟 DOM 实现实际工作方式的深入探究。本文将帮助您编写更多的 performance React 代码，并在事情不尽如人意时进行调试。额外收获:Vue 的虚拟 DOM 实现非常相似，所以如果你也在使用 Vue，你在这里学到的概念应该会对你有所帮助。

##### [数据伪造](http://www.data-forge-js.com/)

我一直将 python 作为数据处理的首选语言，但是遵循阿特伍德定律，任何可以用 JavaScript 编写的东西最终都会用 JavaScript 编写，这里我们有一套看起来超级强大的用 JavaScript 编写的数据争论和分析工具。

#### 其他牛气

##### [如果 JavaScript 赢了呢？](https://medium.com/@anildash/what-if-javascript-wins-84898e5341a)

围绕 JavaScript 生态系统发生的一些社区和网络效应的精彩展示。我们已经在许多领域——社交网络、城市、市场——看到了网络规模增长的复合回报，导致了失控的增长和“赢家通吃”的动态。编程语言和 JavaScript 也在发生同样的事情吗？我不是 100%确信，但我认为这是一个很值得理解和考虑的想法。

##### [你好张量流](https://meowni.ca/posts/hello-tensorflow/)

这是我见过的最简单、最容易理解的机器学习介绍之一，浏览器中有现场演示，带注释的示例代码都是用 JavaScript 编写的。谢谢 Tensorflow.js！

##### [Internet Explorer 的缓慢死亡和渐进增强的未来](http://alistapart.com/article/the-slow-death-of-internet-explorer-and-future-of-progressive-enhancement)

我们正慢慢走向一个完全常青的浏览器的未来，越来越多的公司完全停止了对 Internet Explorer 的支持。也就是说，我有点喜欢为旧版本 IE 的用户提供完全没有 CSS 和 JS 的网站的想法。你的网站还会像那样工作吗？用户还能阅读你的内容吗？值得考虑...

##### [管理 SVG 与指针事件属性的交互](https://www.smashingmagazine.com/2018/05/svg-interaction-pointer-events-property/)

SVG！正如长期读者所知，我非常喜欢 SVG。本文详细介绍了如何使用“pointer-events”属性来管理 SVG 文档或元素的哪些部分可以接收来自鼠标、触控板或手指等定点设备的事件。

##### [理性 vs 打字稿——第一印象](https://lorefnon.tech/2018/05/13/reasonml-vs-typescript-first-impressions/)

最近，我在谈话中有几次提到了理由，但我还没有真正深入了解，但这篇文章改变了这一点。它看起来真的是一门很棒的语言，尤其是对于那些喜欢函数式编程的人来说！本机函数 currying、不变性和管道操作符足以说服我…看起来与 JS 包互操作也很简单…现在我只需要一个项目来尝试一下。:D

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这样，请务必注册，以便每周五将这些简讯直接发送到你的收件箱中！在这里报名:[https://zendev.com/friday-frontend.html](https://zendev.com/friday-frontend.html)