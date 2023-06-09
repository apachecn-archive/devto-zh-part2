# 如何用两行 CSS 制作粘性边栏

> 原文：<https://dev.to/clairecodes/how-to-make-a-sticky-sidebar-with-two-lines-of-css-2ki7>

你是否曾经在一个网站上，当你向下滚动页面时，有一个元素“粘”在屏幕上并跟随你？这就是我所说的粘性边栏，我已经花了很多时间来调试遗留代码。过去，开发人员使用 JavaScript 通过重新计算浏览器滚动事件上侧边栏的位置来构建这一功能。维护起来很复杂，而且性能成本也很高。

最近我很高兴地发现，所有主流的桌面浏览器(Chrome、Firefox、Edge 和 Safari)现在都支持 CSS 属性`position: sticky`。(除了 Chrome 中一些表格元素的 bug。)Edge 是 2017 年 10 月在版本 16 中提供支持的最终浏览器，你可以在[caniuse.com](https://caniuse.com/#search=sticky)上查看。这意味着我们只用两行 CSS 就可以构建一个粘性边栏，不需要 JavaScript！让我告诉你怎么做。

首先设置两个 div。一个代表主页内容，应该延伸到屏幕底部之外。第二个是侧边栏，我们将它做得比屏幕的长度短，这样我们可以清楚地看到它在我们的例子中移动。我们使用 flex 将它们并排放置，因为现在是 2018 年，也因为我喜欢 flex。

使用下面的 HTML 和 CSS:

```
<div class="wrapper">
  <div class="main">
    Main content
  </div>
  <div class="sidebar">
    Sticky sidebar
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
.wrapper {
  display: flex;
  justify-content: space-between;
}

.main,
.sidebar {
  border: 3px solid black;
  padding: 15px;
  background-color: #fff;
}

.main {
  width: 60%;
  height: 150vh;
}

.sidebar {
  width: 25%;
  height: 25vh;
}

body {
  background-color: #ccc;
  font-family: sans-serif;
  padding: 10px;
} 
```

Enter fullscreen mode Exit fullscreen mode

这给了我们两个看起来像这样的元素:

[![Static sidebar and main content](img/858349fe08de7d482601e589b0ca9db4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1OABY4lm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7x8veyruw17v319rxsqp.png)

注意，主元素和侧边栏元素的高度是使用 vh 单位设置的。100vh 是当前视口的高度，因此将主 div 的高度设置为 150vh 会使其高度是屏幕高度的 1.5 倍。

但是侧边栏还不粘！当你向下滚动页面时，侧边栏不会跟着滚动。我们需要做的就是添加两行 CSS:

```
.sidebar {
  position: -webkit-sticky;
  position: sticky;
  top: 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

(好吧，我撒了个谎，三行代码表示 Safari 与前缀`-webkit-`的兼容性。)这样你就知道了，向下滚动页面，侧边栏就会粘在屏幕顶部，跟着你往下看！

[![Sticky sidebar and main content](img/ff4c83cf71da79cdbdab529faaae1330.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QQp9ICij--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/djrxlvvkzb81549o9lh9.gif)

`position: sticky`属性告诉元素贴在屏幕上，( [MDN 比我](https://developer.mozilla.org/en-US/docs/Web/CSS/position)更能解释这一点)，而`top`值告诉元素在滚动时相对于屏幕的位置。我们可以把它改成`top: 5%`来留出一个空隙，或者比如说`left: 0`，这取决于滚动的方向。

这是我之前在 CodePen 上做的一个:

[https://codepen.io/claireparker/embed/bvWKdr?height=600&default-tab=result&embed-version=2](https://codepen.io/claireparker/embed/bvWKdr?height=600&default-tab=result&embed-version=2)

尝试将侧边栏的高度改为 110vh，看看它是如何停在屏幕底部的。不错！

## 生产中可以使用 position: sticky 吗？

`position: sticky`仍然是一个实验性的 API，所以要谨慎使用。[在这里阅读草案规格](https://drafts.csswg.org/css-position-3/#position-property)。做这样的决定时，一定要考虑你支持哪些浏览器。将这些信息与 caniuse 结合起来，做出明智的决定。使用 caniuse 上所有浏览器的统计数据，而不仅仅是最新版本。问问你自己，如果侧边栏没有粘性，会如何影响用户的体验？网站会完全崩溃还是会失去一个漂亮的装饰功能？

一如既往，这取决于您的特定应用程序，没有适用于所有情况的答案。但是真的很酷，我们现在可以完全用 CSS 和你的 CSS 工具包中的一个方便的工具来构建这样的功能。