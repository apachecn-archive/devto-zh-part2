# 在适当的地方使用 CSS 网格(重温)

> 原文：<https://dev.to/starbist/using-css-grid-where-appropriate-revisited-473>

[![Using CSS Grid where appropriate (revisited)](img/b1719fff214a8f5452b54405c9be490b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KRY9Ww4x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g09oge5f26ip1aujk2vx.jpg)

*这篇文章最初发表于 [silvestar.codes](https://www.silvestar.codes/articles/using-css-grid-where-appropriate-revisited/) 。*

这个解决方案是我去年的文章[“在适当的地方使用 CSS 网格”](https://www.silvestar.codes/articles/using-css-grid-where-appropriate/)的后续文章。目标是找到一个带有未知数量项目的导航解决方案。

## 重述

用 CSS Grid 创建导航可能不是最好的解决方案。然而，如果你想使用 CSS 网格，有两种选择:

*   使用`grid-auto-flow: row;`并将每个项目放入网格中，如下所示:

```
.nav__item:nth-child(1) {
  grid-area: 1 / 1 / 2 / 2;
} 
```

Enter fullscreen mode Exit fullscreen mode

*   使用关键字 auto 定义明确的网格以设置行和列的宽度:

```
.nav {
  display: grid;
  grid-auto-flow: row;
}
@media screen and (min-width: 320px) {
  .nav {
    grid-template-columns: repeat(4, auto);
    grid-template-rows: repeat(2, auto);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这两个例子中，我们都定义了一个严格的网格——一行中的许多列都是严格定义的。

[https://codepen.io/CiTA/pen/dzogLV](https://codepen.io/CiTA/pen/dzogLV)

## 新方案

我使用 CSS Grid 已经一年多了，在这个过程中，我学会了如何正确地使用它的特性:

*   [`minmax()`功能](https://www.w3.org/TR/css-grid-1/#valdef-grid-template-columns-minmax)、
*   [`auto-fit`关键词](https://www.w3.org/TR/css-grid-1/#valdef-repeat-auto-fit)，
*   [`grid-auto-flow`房产](https://www.w3.org/TR/css-grid-1/#propdef-grid-auto-flow)，以及
*   如何避免媒体的质疑🎊。

### 代码

我已经分叉以前的解决方案，并更新了上面提到的功能。这是最终的解决方案。

[https://codepen.io/CiTA/pen/pOgGqv](https://codepen.io/CiTA/pen/pOgGqv)T2】

```
.nav--grid2 {
  display: grid;
  grid-auto-flow: dense;
  grid-template-columns: repeat(auto-fit, minmax(60px, auto));
  justify-content: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们分解这段代码。

### minmax()

`minmax()`功能将尺寸定义为最小值和最大值之间的范围。它允许定义列和行的动态大小。

我们可以使用这个属性来定义导航项目的最小和最大宽度。在我们的例子中，我们使用下面的最小最大值定义:

`minmax(60px, auto)`

我们说该列至少应该有 60px 宽，并且应该和最大内容宽度一样宽。详见 [`auto`关键词](https://www.w3.org/TR/css-grid-1/#valdef-grid-template-columns-auto)。

### 自动适应

`auto-fit`应该作为一个重复数——一个在[功能](https://www.w3.org/TR/css-grid-1/#funcdef-repeat)中使用的数。它说网格应该放置尽可能多的项目，就像项目为空时一样(我认为🤔).

### 网格-自动-流动

`grid-auto-flow`是控制放置项目的网格算法如何工作的属性。在我们的例子中，我们使用的是 [`dense`关键字](https://www.w3.org/TR/css-grid-1/#valdef-grid-auto-flow-dense)。它说，网格应该填补更大的网格项目出现时可能留下的漏洞。

### 自圆其说-内容

[`justify-content`属性](https://www.w3.org/TR/css-align-3/#propdef-justify-content)对齐框内内容。我们使用`justify-content: center`将条目的内容居中对齐。

### 奖励:无媒体查询

如您所见，我们没有使用媒体查询。媒体查询是有用的，没有它们，就不会有一个响应式的网站设计，但是当我们能够不用媒体查询就能构建出响应式的行为时，这种感觉是如此令人满意。

## 最后的想法

CSS 网格可能仍然不是导航元素的最佳方法，但它是有效的。总是在适当的地方尝试使用 CSS Grid，即使它解决了你的问题。如果你是一个叛逆者，忽略这种想法并使用它——只要你的用户满意，构建 web 解决方案就没有规则。😎