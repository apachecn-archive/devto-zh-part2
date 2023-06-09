# 5 分钟学会 CSS 网格

> 原文：<https://dev.to/scrimba/learn-css-grid-in-5-minutes-8kp>

[![](img/89fd1ec5230e1a880645fddb3d08809a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--L-EjSvmb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AOc88rInEcNuY-xCN3e1iPQ.png)

网格布局是网站设计的基础，CSS 网格模块是创建它的最强大和最简单的工具。我个人认为它比 Bootstrap 好得多(在这里阅读为什么)。

该模块还得到了[主流浏览器](https://caniuse.com/#feat=css-grid) (Safari、Chrome、Firefox、Edge)的原生支持，所以我相信在*不久的将来*所有前端开发者都将不得不学习这项技术。

在本文中，我将尽快带您了解 CSS 网格的基础知识。在你理解基础知识之前，我会把你不应该关心的东西都删掉。

> 我还创建了一个免费的 CSS 网格课程。[点击此处全面了解
> it。](https://scrimba.com/g/gR8PTE?utm_source=dev.to&utm_medium=referral&utm_campaign=gR8PTE_5_minute_article)

 [![](img/9e88471cd68e819b3125b915e1cb0523.png)
T4】](https://scrimba.com/g/gR8PTE?utm_source=dev.to&utm_medium=referral&utm_campaign=gR8PTE_5_minute_article)

或者，看看这篇文章，它解释了你将在整个课程中学到的东西:

现在让我们开始吧！

### 你的第一个网格布局

CSS 网格的两个核心成分是**包装器**(父)和
**项目**(子)。包装器是实际的网格，项目是网格中的内容。

下面是一个包含六项内容的包装器的标记:

```
 <div class="wrapper">
      <div>1</div>
      <div>2</div>
      <div>3</div>
      <div>4</div>
      <div>5</div>
      <div>6</div>
    </div> 
```

Enter fullscreen mode Exit fullscreen mode

为了将我们的包装器`div`变成一个**网格**，我们简单地给它一个
T1 的显示:

但是，这还没有做任何事情，因为我们还没有定义我们想要我们的网格看起来像什么。它会简单地将 6 个`div's`堆叠在一起。

[![](img/f0618290741a9d8b98160335df9b0e33.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9mffDmM9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AvTY7C5FMIp8OLkjrgp-vBg.png)

我添加了一些样式，但这和 CSS 网格没有任何关系。

### 列和行

为了使它二维，我们需要定义列和行。让我们创建三列两行。我们将使用`grid-template-row`和`grid-template-column`属性。

因为我们已经为`grid-template-columns`写了三个值，所以我们将得到三列。我们将得到两行，因为我们已经为`grid-template-rows`指定了两个值。

这些值决定了我们希望的列宽(100 像素)和行高(50 像素)。结果如下:

[![](img/74142b152908c46073a502b940bfe9bd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hlxQStxF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AfJNIdDiScjhI9CZjdxv3Eg.png)

为了确保您正确理解了值之间的关系以及网格的外观，也看一下这个例子。

```
.wrapper {
    display: grid;
    grid-template-columns: 200px 50px 100px;
    grid-template-rows: 50px 50px;
} 
```

Enter fullscreen mode Exit fullscreen mode

尽量抓住代码和布局之间的联系。

事情是这样的:

[![](img/9825c28ef9cdda5472d3960a63816adb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jz21LIcZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AM9WbiVEFcseUCW6qeG4lSQ.png)

### 放置物品

接下来您需要学习的是如何在网格上放置项目。这是你获得超能力的地方，因为它使创建布局变得非常简单。

让我们创建一个 3x3 的网格，使用和以前一样的标记。

```
.wrapper {
    display: grid;
    grid-template-columns: 100px 100px 100px;
    grid-template-rows: 100px 100px 100px;
} 
```

Enter fullscreen mode Exit fullscreen mode

这将导致以下布局:

[![](img/9825c28ef9cdda5472d3960a63816adb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jz21LIcZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AM9WbiVEFcseUCW6qeG4lSQ.png)

> 注意，我们在页面上只看到一个 3x2 的网格，而我们将其定义为 3x3 的网格。这是因为我们只有六个项目来填充网格。如果我们还有三个，那么最低的一行也会被填满。

为了定位和调整项目的大小，我们将它们作为目标，并使用`grid-column`和`grid-row`属性:

```
.item1 {
    grid-column-start: 1;
    grid-column-end: 4;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里说的是，我们希望 item1 从第一个网格线开始，在第四个列线结束。换句话说，它会占据整行。

这就是它在屏幕上的表现:

[![](img/0d17ed2b9e0451d206444ee26d3256e8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aA8SYnKO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Ahe7CoAzdQB3sei_WpHOtNg.png)

你是否对我们只有 3 列却有 4 列线感到困惑？看看这张图片，我用黑色画了列线:

[![](img/dbc4c4e61f2f241c2978f1a16edbedc5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qQPkcz8z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Al-adYpQCGve7W6DWY949pw.png)

请注意，我们现在使用了网格中的所有行。当我们让第一个项目占据整个第一行时，它会将其余的项目向下推。

最后，我想展示一种更简单的方法来编写上面的语法:

为了确保您已经正确理解了这个概念，让我们稍微重新排列一下项目。

```
.item1 {
    grid-column-start: 1;
    grid-column-end: 3;
}

.item3 {
    grid-row-start: 2;
    grid-row-end: 4;
}

.item4 {
    grid-column-start: 2;
    grid-column-end: 4;
} 
```

Enter fullscreen mode Exit fullscreen mode

这是它在页面上的样子。试着想想为什么会这样。应该不会太难。

[![](img/c79da7d489604e86d1261f37300559a1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LafhrL15--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AQDSybpxjXSat6UtoHgUapQ.png)

就这样了！

当然，还有很多概念我们还没有经历过。如果你想学习这些，可以看看我在 Scrimba 上的免费 CSS 网格课程。

如果你有任何问题，请留下评论，我会尽我所能回答。或者，通过 Twitter 联系我。。