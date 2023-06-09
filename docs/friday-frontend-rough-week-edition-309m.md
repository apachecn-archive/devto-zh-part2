# 周五前端:粗糙的一周版

> 原文：<https://dev.to/kball/friday-frontend-rough-week-edition-309m>

对于我们这些美国人来说，这是艰难的一周，我怀疑对于美国以外的一些人来说也是如此。一些非常非常黑暗和混乱的东西在国家舞台上上演。如果学习前端的东西有助于分散你的注意力，希望你喜欢本周的链接。如果你现在感觉情绪过于紧张，甚至不想去想这些事情，请知道我就在你身边。

花些时间照顾自己。如果你还没有被正在发生的事情所触动，那么向你生活中已经被触动的人伸出援手。

希望你有一个愉快的周末！

来自 ZenDev 的 KBall

#### CSS&SCS

##### [不要为你的设计系统使用空的或低含量的网格示例](https://css-tricks.com/dont-use-empty-or-low-content-for-your-design-system-grid-examples/)

这有点让我吃惊。探索基于 CSS-Grid 的系统不仅可以再现我们以前拥有的规则的、受控的布局分布，而且是一种迷人的新型系统，其中网格大小和空间是由其中的内容决定的。非常非常酷！

##### [把东西放在其他东西上面](http://tellthemachines.com/stacking-contexts/)

对“堆叠上下文”概念的有趣探索。我之前主要接触的是 z-indexes，如果您对 z-index 没有达到预期效果感到沮丧，这将对您有所帮助，但本文还探讨了堆叠上下文的其他影响，以及它如何影响混合等内容。

##### [什么是模块化 CSS？](https://spaceninja.com/2018/09/17/what-is-modular-css/)

现代 CSS 命名方案和方法背后的历史和思考。解释了 OOCSS、BEM 和 SMACSS 等系统是什么，但也介绍了这些思想。从 CSS(而不是 JS 框架)的角度对“面向组件”的编码进行了很好的概述。

##### [【3D 表情小镇(纯 CSS)](https://codepen.io/GeorgePark/pen/MqVoYP)

完全被这个吓到了。一个完整的动画城镇，完全由 html 和 css 构建，唯一的 javascript 是一个滑块上的事件处理程序，允许你在 3d 中旋转城镇。哇哦。

#### JavaScript

##### [JavaScript 闭包初学者指南](https://blog.bitsrc.io/a-beginners-guide-to-closures-in-javascript-97d372284dda)

闭包是从一开始就存在的语言特性之一，是真正开始理解并很好地使用 JavaScript 的关键。这篇文章不仅让你了解它们是如何工作的，还深入探讨了它们工作背后的“为什么”。

##### [用 JavaScript 写出更好的条件句的 5 个技巧](https://scotch.io/tutorials/5-tips-to-write-better-conditionals-in-javascript)

这是一篇非常好的“如何编写更好的代码”的文章。一些具体的技巧可以帮助你写出更清晰、可读性更强的条件语句。

##### [一个最小的 JavaScript 设置](https://css-tricks.com/a-minimal-javascript-setup/)

本文展示了作者参与编写的 JavaScript 库/miniframework，但这并不是有趣的部分。这篇文章的大部分是对框架需要做出什么决定的分析。它分解了现代 JavaScript 应用程序的组件，以及这些组件在框架中是如何表现的。这对理解这些事情如何运作的大背景是非常有价值的。

##### [使用 Vue.js 中的作用域插槽来抽象功能](https://css-tricks.com/using-scoped-slots-in-vue-js-to-abstract-functionality/)

我通读了几遍才完全理解这一点，但本质上这是在展示如何使用 Vue 的`scoped slot`功能来创建在 React 世界中被称为带渲染道具的高阶组件。本质上是添加特定功能的包装组件，但是可以用于任意的子组件。

##### [使用 React 进行条件渲染的 3 种反模式](https://frontarm.com/articles/react-anti-patterns-conditional-rendering/)

由于 JSX“只是 javascript”，有很多很多方法可以达到同样的目标。本文深入探讨了一些可能用于条件呈现的机制，以及这些特定方法的缺点。

#### 其他牛气

##### [内容和设计是不可分割的工作伙伴](https://medium.com/user-interface-22/content-and-design-are-inseparable-work-partners-a21e76b644d9)

这在设计中很突出，但我发现这对于开发也是如此。您使用不代表实际数据的纯“虚拟数据”进行的任何开发充其量是不完整的。要构建一个真正健壮的系统，您需要了解系统中的真实数据。

##### [【开发者体验】偷梁换柱](https://infrequently.org/2018/09/the-developer-experience-bait-and-switch/)

对于我们继续将越来越多的东西放入 JavaScript 的讨论，有一个非常重要的贡献。很多时候，以“开发人员体验”的名义进行权衡(例如，JS 中的 CSS)，我们基于关于迭代速度的争论而放弃了用户成本。这些论点可能正确，也可能不正确，但是正如本文所呈现的，我们既没有验证它们，也没有建立系统来确保我们保持开发者动机和用户动机一致。

##### [一个 web 性能框架](https://adactio.com/journal/14355)

沿着与上一篇文章相似的路线，但不是争论我们应该重视什么，而是讨论我们如何去测量 web 性能。有什么相关的东西要看，如果我们要优化，我们应该如何确定什么将是最有价值的。

##### [精确测量网上版面](https://nolanlawson.com/2018/09/25/accurately-measuring-layout-on-the-web/)

继续性能主题，本文着眼于我们如何测量 JavaScript 性能，特别是操纵 DOM 的 JavaScript。作者认为，真正的性能度量必须包括浏览器作为 JavaScript 的结果所做的格式化和布局工作，并研究了一些度量机制。

##### [AMP 项目的开放式治理模式](https://amphtml.wordpress.com/2018/09/18/governance/)

如果你认为 AMP 在谷歌搜索结果之外发挥了重要作用(我认为很有可能——这是对移动性能问题的另一种看法),那么这是一个非常好的迹象。项目的技术负责人宣布通过技术指导委员会转向开放的治理模式。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】