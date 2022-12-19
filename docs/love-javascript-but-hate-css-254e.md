# 热爱 JavaScript，却讨厌 CSS？

> 原文：<https://dev.to/dceddia/love-javascript-but-hate-css-254e>

[![Love JS, Hate CSS](../Images/8c820bbeac9f233098e5f67730188cdc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Jx-kpbA2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daveceddia.com/images/love-js-hate-css.png)

一位读者来信说，他在 JS 和 React 上玩得很开心，但当谈到造型时，他不知所措。

> 我喜欢 JavaScript，但讨厌 CSS。我只是没有耐心让一些东西看起来很好。

写代码很有趣。解决问题很有趣。当你最终让电脑做你想做的事情时，那种幸福的感觉？太棒了。

然后:*哦，废话，CSS* 。这款应用运行良好，但看起来很糟糕，没有人会把它当回事，因为它看起来不像苹果(Apple)。

## 你并不孤单

首先，我想说的是:如果你喜欢前端开发的一切，除了 CSS，你并不孤单。我认识一些真正专业的 UI 开发人员*，他们的工作*要么在设计上很垃圾，要么*可以做*，但是他们捏着鼻子，试图尽快完成。

几年前我也是这样。CSS 就像一个神奇的黑盒子，我在里面输入东西，至少 60%的时候，它会吐出一些看起来比我开始时更糟糕的东西。我用 Google 和 StackOverflow 解决了大多数 CSS 问题，并疯狂地希望有人以前遇到过我的问题(不知何故，他们通常会遇到)。

但是我已经从那个黑暗的地方走了出来，我可以说 CSS(以及将样式应用到页面的过程)是一项可以学习的技能。甚至*设计*也是可以学习的技能。郑重声明，它们是不同的技能。

## 造型不是设计

采用现有的视觉设计并编写 CSS 来转换一大块`div`以匹配视觉设计的过程被称为**样式**。

拿起一张空白的画布，设计出一个漂亮的网站的过程叫做**设计**。

你可以擅长(甚至非常擅长)其中的一项，但同时又非常不擅长另一项。

要成为一名前端开发人员，你需要一些样式(CSS)技能，但不一定是设计技能。

## 能避免 CSS 吗？

我希望我能告诉你，你可以忘记所有的 CSS，100%的时间都呆在 JS 的土地上。

但事实上，我做不到。如果你想做前端开发，你最终需要亲自动手，学习一些 CSS。

我可以根据经验告诉你，一旦你对 CSS 有了一点了解，它就没那么糟糕了。甚至可以很好玩！当我能得到一个布局恰到好处的页面，并且知道该调整哪些参数来使它看起来像我想要的样子时，我感到很满足。

## 做什么

虽然你不能完全避免 CSS，但是有一些事情可以让样式不那么糟糕。

### 框架

CSS 框架可以帮助你快速启动项目，甚至弥补设计技能的不足。它们通常作为 npm/yarn 的可安装库提供，或者来自 CDN。每一个都有自己的视觉风格，所以当你做出选择时，你需要权衡每一个的外观。CSS 框架帮助你构建一个好看的应用程序，而不需要过分关注样式。

以下是一些受欢迎的选择(我重点关注那些与 React 配合良好的选择):

非常受欢迎(阅读:SO 上的许多问题和答案)和体面的外表。最新版本(v4)看起来更现代，但旧版本最近看起来有点过时。你可以用你自己的 CSS 自定义它，或者使用免费和[付费的主题](https://themes.getbootstrap.com/)来改变外观。如果你正在使用 React，看看 [react-bootstrap](https://react-bootstrap.github.io/getting-started/introduction) 中的一些预制组件，比如模态对话框、弹出窗口、表单等等。

语义用户界面(Semantic UI)——另一个流行的带有 React 组件的 CSS 框架，它比 Bootstrap 多了一些可用的组件，而且(我认为)看起来更现代。

蓝图(blue print)-我认为蓝图看起来很棒，在我看来，比 Bootstrap 或语义 UI 更好。但是我自己没用过。Blueprint 突出的一点是它是用(并支持)TypeScript 编写的。它不需要*打字稿，但是如果你使用 TS，它可能值得一看。*

有大量的 CSS 框架可供使用。这里是[与 React 一起工作的更多列表](https://hackernoon.com/the-coolest-react-ui-frameworks-for-your-new-react-app-ad699fffd651)。

虽然框架可以帮助你避免接触太多的 CSS，但是接下来的两件事会让你更容易地直接使用 CSS。

### Flexbox

Flexbox 布局是一种使用 CSS 布局内容的现代方式，比过去的`float`更容易使用(或者你 5 分钟前在黑暗中做的随机穿刺)。它有[良好的浏览器支持](https://caniuse.com/#search=flexbox)，让一些传统上用 CSS 很麻烦的事情变得非常简单，比如**垂直居中的事情**。

看看这个:

看那个红色的小方块是多么的居中！带有灰色边框的外部框只需要这 3 行 CSS 代码就可以实现:

```
display: flex; /* turn flexbox on */
justify-content: center; /* center horizontally */ 
align-items: center; /* center vertically */ 
```

Enter fullscreen mode Exit fullscreen mode

如果你右击它并检查元素，你会看到它不止有这 3 行…但是它们并不负责将红框居中。这些额外的东西给了它灰色的边框，使它成为一个正方形，在这篇博文(`margin: 0 auto`)中水平居中，底部的空白给了它一些喘息的空间。

如果你有兴趣了解更多，CSS Tricks 有一个很棒的 Flexbox 完整指南。建议去查一下！Flexbox 确实帮助我掌握了 CSS，现在它是我解决大多数布局问题的首选工具。

### CSS 网格

网格是一种更现代的布局方式，比 flexbox 更强大。它可以处理二维(行和列)，而 flexbox 更擅长处理一个方向或另一个方向。浏览器支持[相当不错](https://caniuse.com/#feat=css-grid)。根据 CSS 技巧:

> 截至 2017 年 3 月，大多数浏览器都提供了对 CSS Grid 的原生、无前缀支持:Chrome(包括在 Android 上)、Firefox、Safari(包括在 iOS 上)和 Opera。另一方面，Internet Explorer 10 和 11 支持它，但这是一个带有过时语法的旧实现。现在是构建网格的时候了！

在我写这篇文章的时候，我只是稍微摆弄了一下 CSS grid 的布局。它比 flexbox 更强大，但也更复杂，我发现 flexbox 在大多数情况下都能很好地满足我的需求。虽然它在我的学习清单上！

你可以阅读 CSS 技巧的完整指南来了解更多。

### 一种合乎逻辑的方法

这是处理 CSS 的额外元“策略”。尽你所能，试着避免在黑暗中的随机插入和从 StackOverflow 复制粘贴，以使你的布局看起来正确。

尝试采取更有条理的方法。

*   将项目放置到位(flexbox、网格，或者绝对放置在相关容器中)
*   设置其边距和填充
*   设置边框
*   设置背景颜色
*   然后[绘制猫头鹰的其余部分](http://knowyourmeme.com/memes/how-to-draw-an-owl)——添加框阴影，设置:hover/:active/:focus 状态等。

[![Draw the rest of the owl](../Images/7407380e30da479956071e341a51b3bf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OgOLMyt0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daveceddia.com/images/draw-an-owl.jpg)

在某些方面，像 [DRY(不要重复)](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)和德米特的[法则这样的软件工程原则可以应用于页面上的样式元素。作为一个例子，考虑这样一种布局，用户的消息带有他们的头像:](https://en.wikipedia.org/wiki/Law_of_Demeter)

[![Layout for a message with user avatar](../Images/8133f36e6ac987d9bdf2967f9239eb36.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r5lWduLM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daveceddia.com/images/css-layout-dry-example.png)

请注意，所有东西都距离盒子的边缘 20 个像素。实现这一点的一种方法是将盒子中两个元素的`margin`设置为`20px`。

但是这也有一些缺点。首先，有重复:如果利润率需要改变会发生什么？必须在两个地方改变它！

第二，难道不应该是盒子的“责任”来决定它的元素嵌入多远，而不是让每个元素来决定它自己离边缘的距离吗？

一种更好的布局方式是将盒子的`padding`设置为`20px`，这样内容就不会知道它们应该放在哪里。这也使得在盒子里添加新元素变得更加容易——你不需要明确地告诉每个元素应该放在哪里。

这是一个很小的例子，只是为了说明这一点，即一点点深谋远虑和逻辑方法可以使造型进行得更顺利。

## 动作步骤！

1.  找到 3 个要复制的布局。这些可以是你使用的网站的小元素(一条 tweet，一张 Pinterest 卡，等等)，也可以是实体的东西，比如信用卡或书籍封面。
2.  阅读[Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)的完整指南。
3.  使用 flexbox 创建您在步骤 1 中选择的布局。

[热爱 JavaScript，却讨厌 CSS？](https://daveceddia.com/love-js-hate-css/)最初由戴夫·塞迪亚于 2018 年 3 月 15 日在[戴夫·塞迪亚](https://daveceddia.com)发布。

[代码项目](http://www.codeproject.com)