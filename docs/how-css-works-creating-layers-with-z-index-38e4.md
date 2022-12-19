# CSS 的工作原理:使用 z 索引创建图层

> 原文：<https://dev.to/bnevilleoneill/how-css-works-creating-layers-with-z-index-38e4>

[![](../Images/fd9a92dfec2b3dd04da497c196b2972f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yZH2HTIv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AtGY4JI6xd_e3irIrrrmqiw.jpeg)

### 简介

这篇文章是“CSS 如何工作”系列的第三篇，在这个系列中，我们深入研究 CSS 的基本构建模块，这些模块有时感觉像是魔法。不管你如何创作你的 CSS，知道你的样式表的“运行时间”总是好的，这样你就可以写高效的，可伸缩的 CSS。

以下是该系列的前两篇文章:

1.  [CSS 如何工作:解析&在关键渲染路径中绘制 CSS](https://dev.to/bnevilleoneill/how-css-works-parsing--painting-css-in-the-critical-rendering-path-h4j):深入了解从浏览器请求 CSS 文件到用户实际看到屏幕上的像素之间 CSS 引擎中发生了什么。
2.  [CSS 如何工作:理解层叠](https://dev.to/bnevilleoneill/how-css-works-understanding-the-cascade-3bag):概述“CSS”中的“C”以及层叠算法如何将各种 CSS 规则解析为在浏览器中显示的实际样式。

今天我们将深入 CSS 的另一个“黑魔法”部分。玩弄 z-index 通常感觉就像把一个更高的数字扔向墙壁，看看有什么粘在一起。

但是如果我们剥开 z-index 上的层(双关语😂)并看一看管理它的规则？

我们会发现它并不像我们想象的那么可怕。

[![](../Images/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/?cid=banner_a)

### Z-Index 到底是什么？

归根结底，我们在浏览器中使用的屏幕是二维平面，有一堆像素。但是，如果你曾经在网上呆过一段时间，你可能经常会有“三维”的体验。例如，您可以单击一个按钮并打开一个模态，或者让工具提示显示在触发器的“上方”。

如果你假设用户正在“看着屏幕”,并想象屏幕是一个进入三维世界的入口，你可以开始想象浏览器中的三维空间是什么样子。首先，如果我们把这个空间想象成一个房间，那么“最远”的“墙”就是“画布”。

当在屏幕上绘制像素时，浏览器确保最靠近画布(或离我们最远)的像素首先被绘制。然后，它逐渐继续绘制更接近用户的元素，覆盖先前绘制的像素。

z-index 属性指的是元素在三维浏览器中的绘制顺序。默认情况下，所有元素的 z 索引都为 0，浏览器按照 DOM 顺序绘制。然而，z-index 实际上为我们提供了对何时绘制元素的细粒度控制。通过指定一个较高的 z-index，我们可以使元素以这样一种方式绘制，即它“更接近”用户，而指定一个较低的(或负的！)z-index 让我们将元素绘制得更接近画布。

在深入研究 [CSS 位置&布局规范的时候，](https://www.w3.org/TR/css-position-3/#layered-presentation)我发现了这个有用的图表，可以直观地向用户展示 z 索引层的外观。

[![](../Images/9a68b44a9ffcceb6be8cbff3b753b0f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oNuXW2bu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/265/1%2AaUbku-PjO32LMzy5cXd7fQ.png) 

<figcaption>具有较高 z 索引的元素看起来“更靠近”用户</figcaption>

如果我们只看 z-index 的这种行为，很容易被诱惑认为它是 100%直接的:更高的 z-index 等于更接近用户，更低的 z-index 则更远。然而，z-index 的一些细微差别会引起很多混乱。

附加到 z-index 的第一个警告是，它*必须在定位元素*上才能生效。这意味着 z-index 只能用于改变堆叠顺序，如果你已经设置了一个静态以外的位置。在任何没有在元素上设置位置的情况下，z-index 都没有任何作用。

### 介绍堆叠上下文

当我们考虑 z-index 的第二个警告时，z-index 周围的水域变得更加模糊:它只适用于位于*堆栈上下文*中的元素。

堆叠上下文涉及一个 HTML 节点及其所有子节点。堆栈上下文根级别的 HTML 元素可以称为*堆栈根。*

文档的默认堆叠上下文(或“根堆叠上下文”)将 html 标签作为其堆叠根，默认情况下所有元素都属于该堆叠上下文。然而，任何 HTML 节点也可以是“本地堆栈上下文”的根元素。

有几种方法可以将元素指定为新的本地堆栈上下文的根:

1.  在元素上设置`position: absolute`或`position: relative`以及除 auto 之外的任何 z 索引。
2.  在元素上使用`position: fixed`或`position: sticky`。
3.  在元素上设置小于 1 的不透明度。
4.  在元素上使用 transform 或 will-change。

如果您想查看创建新堆栈上下文的更全面的方法列表，请查看 MDN 上的[这篇文章。](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Positioning/Understanding_z_index/The_stacking_context)

作为一个例子，让我们想象三棵树的 HTML 节点，可视化为三个堆栈。在下图中，每个堆栈的底部是父 HTML 节点，子元素堆叠在顶部(有点像 HTML 树的“颠倒”表示)。让我们假设这些元素都是 body 标签的直接子元素。

[![](../Images/d23e43fa2fd77891cabcbe5961778e82.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ktP5TOCd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A1w1e98DrXWdF4fEDFJwLQQ.png) 

<figcaption>三叠元素，父元素都在最下面</figcaption>

如果我们在浏览器中预览这些 HTML 元素，它看起来会像这样:

[https://codepen.io/benjaminj6/embed/oyoyrd?height=600&default-tab=result&embed-version=2](https://codepen.io/benjaminj6/embed/oyoyrd?height=600&default-tab=result&embed-version=2)

注意到什么有趣的事了吗？

首先，为什么 blue-child-2 在其他任何东西下面？它的 z 指数是一百万，所以它应该在上面，对吗？

此外，为什么 green-child-1 显示在所有内容的顶部，尽管它的 z 索引为 2？我们不是说过较高的 z 索引“赢”过较低的 z 索引吗？

让我们从绿色儿童 1 号开始。因为它的父元素——green-parent——的 z 索引为 999，所以我们希望 green-parent 也高于其他元素。但是如果我们回想起使用 z-index 的第一个警告，它对静态定位的元素没有影响。这意味着 green-child-1 是根堆叠上下文的一部分，它是相对于根堆叠层中仅有的另外两个元素 red-parent 和 blue-parent 来衡量的。它的 z 索引比这两个都高，所以显示在顶部。

理解红色父代和蓝色父代各自创建它们自己的本地堆栈上下文也有助于我们弄清楚为什么蓝色子代-2 出现在红色父代的下面，尽管它的 z 索引*远高于*。由于 z-index 仅控制元素在其本地堆栈上下文中的位置*，blue-child-2 将肯定位于 blue-parent 的所有子元素之上。但是红色父元素比蓝色父元素具有更高的 z 索引，*红色父元素中的所有元素都将显示得比蓝色父元素高，不管它们的* *z 索引是什么。**

为了让 blue-child-2 显示在 red-parent 之上，我们实际上必须改变我们的 HTML 结构，将 blue-child-2 从其当前的本地堆栈上下文中取出&放入根堆栈上下文中(或者至少放入 red-parent 之上的堆栈上下文中)。

在一个较大的应用程序中，像这样的提取常常是棘手的(甚至是危险的)，尤其是当您试图管理语义 HTML 结构、可访问性和模块化组件架构之类的东西时。

您经常会看到许多组件库通过将元素附加到 body 标签而不是组件所在的位置来实现它们的重层 UI(工具提示和模态之类的东西)——这在很大程度上是为了避开这些堆栈上下文“陷阱”。这样做保证了严重依赖层的组件上的 z-index 总是引用根堆叠上下文，因此它们可以设置类似 1000 的 z-index，并相信它会显示在大多数元素的顶部。

### 结论

在某些情况下，使用 z-index 可能很棘手，但是当我们知道它在幕后如何工作的规则时，我们会发现它的行为实际上是完全可以预测的。我猜想，围绕 z-index 的 99%的困惑源于对我们讨论的两个警告的误解。

作为一个快速的复习，它们又出现了，在 TL 中；时尚博士。

1.  z-index 仅适用于除 static 之外还有位置值的元素。
2.  z-index 仅适用于元素在其所属的*堆栈上下文*中的位置。_

我希望了解 z-index 的内部知识能让你在接近多层 ui 及其各种陷阱时有信心。我知道研究 z-index 和堆叠层对我感觉我进行 UI 开发的方式很有帮助——知道给*分配什么数字*比只是把 z-index 值扔向墙壁是一个很大的进步。

* * *

### Plug: [LogRocket](http://logrocket.com) ，一款适用于网络应用的 DVR

[![](../Images/d56be9e9e36d8fa98c6959f7097b7787.png)T2】](http://logrocket.com)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[CSS 如何工作:用 z 索引创建图层](https://blog.logrocket.com/how-css-works-creating-layers-with-z-index-6a20afe1550e/)最先出现在[日志博客](https://blog.logrocket.com)上。