# 我如何记得 CSS 网格属性

> 原文：<https://dev.to/zellwk/how-i-remember-css-grid-properties-9i5>

CSS 网格的语法是外来的，很难记住。但是如果你记不住 CSS Grid 的语法，你在使用 CSS Grid 的时候就不会自信。

为了有效地使用 CSS Grid，您需要记住它的属性和值。

我想分享一下我是如何记住今天最常见的 CSS 网格属性的。这将帮助你使用 CSS 网格，而不是像一个疯子一样谷歌。

## 组属性

我记得 CSS 网格显示了四组属性:

1.  显式网格
2.  缺口
3.  对齐事物
4.  隐式网格

## 显式网格

假设您想要创建一个 4 列 3 行的网格。你大声说出这 4 列 3 行。很露骨。

如果您声明了网格中的行数和列数，则网格是显式的。

您可以使用两个属性来创建显式网格:

1.  `grid-template-columns`
2.  `grid-template-rows`

`grid-template-columns`允许您定义列数。`grid-template-rows`允许您定义行数。

```
.grid {
  display: grid; 
  grid-template-columns: 1fr 1fr 1fr 1fr; 
  grid-template-rows: 3em 3em 3em;
} 
```

这将创建一个四列三行的网格。

见[码笔](https://codepen.io)上 Zell Liew ( [@zellwk](https://codepen.io/zellwk) )的笔 [XPyGZp](https://codepen.io/zellwk/pen/XPyGZp/) 。

你怎么知道有四列三行？

`grid-template-columns`为您添加的每个长度值创建一个新列。在上面的`grid-template-columns`声明中，我们有四个`1fr`值。这意味着四列。

`grid-template-rows`以同样的方式工作。上面的网格有三个`3em`值，这意味着它有 3 行。

`grid-template-columns`和`grid-template-rows`也可以接受`repeat`、`autofill`、`autofit`、`minmax`这样的值。在本文中，我们不会深入探讨这些价值观。

您现在需要知道的是，您可以创建一个具有两个属性的显式网格:

1.  `grid-template-columns`:创建列
2.  `grid-template-rows`:创建行

### 在网格中定位项目

您可以使用两个属性来控制网格中项目的位置:

1.  `grid-column`
2.  `grid-row`

这两个属性只能在网格项目上使用。

`grid-column`让您选择想要放置网格项目的列。它是`grid-column-start`和`grid-column-end`的简写。

它是这样工作的:`grid-column-start / grid-columns-end`。

```
/* Using the longhand */
.grid-item {
  grid-column-start: 1; 
  grid-column-end: 3;
}

/* Using the shorthand */
.grid-item {
  grid-column: 1 / 3;
} 
```

注意:您也可以使用`span`关键字来告诉 CSS Grid 您希望您的项目占据多少列。

```
/* Using the longhand */
.grid-item {
  grid-column-start: 1; /* Start at column one */
  grid-column-end: span 2; /* Width is two columns */
} 
```

参见 [CodePen](https://codepen.io) 上 Zell Liew ( [@zellwk](https://codepen.io/zellwk) )的 Pen [显式网格属性](https://codepen.io/zellwk/pen/dqQrmm/)。

`grid-row`让您选择要放置网格项目的行。它是`grid-row-start`和`grid-row-end`的简写。

它是这样工作的:`grid-row-start / grid-row-end`。

```
/* Using the longhand */
.grid-item {
  grid-row-start: 1; 
  grid-row-end: span 2;
}

/* Using the shorthand */
.grid-item {
  grid-row: 1 / span 2;
} 
```

参见 [CodePen](https://codepen.io) 上 Zell Liew ( [@zellwk](https://codepen.io/zellwk) )的笔[定位项目(行)](https://codepen.io/zellwk/pen/OoaqoG/)。

### 定位命名区域中的项目

如果你不喜欢计算行和列，你可以命名你的网格的一部分。这些命名的部分称为网格区域。要创建一个网格区域，在网格上使用`grid-template-area`。

关于创建网格区域的一些注意事项:

1.  您必须命名每个网格区域
2.  如果你不想命名一个区域，使用`.`
3.  用引号(`"row1" "row2"`)分隔的每个组表示一行
4.  引号(`"area1 area2"`)内的每个值表示一个区域

以下示例有三个网格区域:

1.  `header`放在前两列，占用 4 列
2.  `main`在第二行，占据中间 2 列
3.  `footer`在第三行，占据 4 列

```
.grid {
  grid-template-areas: "header header header header"
                      ".      main   main   .     "
                      "footer footer footer footer";
} 
```

要将项目放置在网格区域中，可以使用网格项目上的`grid-area`属性。

要在网格区域放置项目，您可以使用`grid-area`。

```
.grid {
  display: grid; 
  /* ... */
}

main {
  grid-area: main
} 
```

参见 [CodePen](https://codepen.io) 上 Zell Liew ( [@zellwk](https://codepen.io/zellwk) )的钢笔 [Grid-template-area](https://codepen.io/zellwk/pen/PdxLyg/) 。

### 如何记住这些属性

到目前为止，您已经学习了 6 个属性:

1.  `grid-template-columns`
2.  `grid-template-rows`
3.  `grid-template-areas`
4.  `grid-column`
5.  `grid-row`
6.  `grid-area`

记住这 6 个属性的一些提示:

1.  `template`关键字只能在网格上使用
    1.  它们用于声明网格和命名区域
    2.  带有`template`关键字的属性是复数
2.  网格项目的属性没有关键字`template`
    1.  这些特性是独特的
    2.  这些属性会影响定位

## 缺口

创建网格时，可以在列和行之间创建空间。这些空间被称为间隙。

需要记住三个属性:

1.  `grid-column-gap`
2.  `grid-row-gap`
3.  `grid-gap`

`grid-column-gap`决定列间距。

`grid-row-gap`决定行与行之间的间距。

`grid-gap`是`grid-column-gap`和`grid-row-gap`的简称。

对于这个简写:

1.  首先是`column`值:`column-gap / row-gap`
2.  如果使用单个数字，两个值都将是该数字。

```
/* Different values */
.grid {
  grid-column-gap: 1em;
  grid-row-gap: 2em;
}

.grid {
  grid-gap: 1em / 2em; 
} 
```

```
/* Same values */
.grid {
  grid-column-gap: 1em;
  grid-row-gap: 1em;
}

.grid {
  grid-gap: 1em;
} 
```

参见 [CodePen](https://codepen.io) 上 Zell Liew ( [@zellwk](https://codepen.io/zellwk) )的笔[带间隙的显式网格](https://codepen.io/zellwk/pen/bxQZZG/)。

## 整理事物

这是很多人困惑的地方。

有六个属性来对齐事物:

1.  `justify-content`
2.  `align-content`
3.  `justify-items`
4.  `align-items`
5.  `justify-self`
6.  `align-self`

您可以在这里看到两组图案:

*   第一组是`justify`对`align`
*   第二组是`content`、`items`和`self`

这两组属性告诉你你在处理什么。如果你理解了 property 关键字，你就会知道如何使用它们。

### 对齐 vs 对齐

每个 CSS 网格有两个轴:

1.  主轴
2.  横轴

当你`justify`某个东西时，你正在根据*主轴*改变对准。当你`align`某个东西时，你正在根据*横轴*改变对准。

以下是识别主轴和横轴的简单方法:

1.  确定语言的方向
2.  主轴是你阅读语言的方式
3.  横轴是你读完第一行末尾后的读法。

让我们以英语为例。你怎么读英语？

1.  从左到右
2.  从上到下

所以主轴和横轴是:

1.  主要:从左到右
2.  交叉:从上到下

[![Main-axis reads left to right. Cross-axis reads top to bottom](../Images/bd67fce5af517bcf110131a4ff00bdda.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KHDyRPM5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.com/images/2018/remember-css-grid-properties/axes.png) 

<figure>

<figcaption>主轴从左向右读。横轴从上到下读</figcaption>

</figure>

注意:如果用`writing-mode`改变语言方向，主轴和横轴也会改变。

### 内容、项目和自我

`justify-content`和`align-content`允许您将网格本身与网格外部的可用空间对齐。只有当网格小于其定义的区域时，才需要这些属性。(这种情况很少见)。

```
.grid {
  justify-content: /* some value */; 
  align-content: /* some value */; 
} 
```

您可以从七个值中选择:

1.  **开始**:将网格刷新到轴的起点
2.  **end** :刷新网格到轴的末端
3.  **中心**:将网格对准轴的中心
4.  **拉伸**:网格填充轴(这是默认值)
5.  **space-between** :在网格项目之间展开空白。结尾没有空格
6.  **空格环绕**:在每个网格项目周围散布空格
7.  **空间均匀**:在所有网格项目周围均匀分布空白，包括两端

[![7 possible alignment values and what they do](../Images/af0124f38618c6763af3d5d39b42227f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v2mOckOk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.com/images/2018/remember-css-grid-properties/alignment-values.png) 

<figure>

<figcaption>7 种可能的对齐值及其作用</figcaption>

</figure>

以上图片摘自 CSS Tricks 的[网格完全指南](https://css-tricks.com/snippets/css/complete-guide-grid/)。它详细解释了每个值的作用。你可以阅读它了解更多信息。

我们这里的重点是记住属性以及如何使用它们。让我们回到下一组属性的轨道上来。

`justify-items`和`align-items`允许您将网格项目与它们各自单元格中的任何可用空白对齐。大多数时候，当你试图调整事物时，你要么寻找`justify-items`要么寻找`align-items`。

```
.grid {
  justify-items: /* some value */; 
  align-items: /* some value */; 
} 
```

您可以从相同的四个值中选择:

1.  **开始**:将项目刷新到轴的起点
2.  **end** :将项目刷新到轴的末端
3.  **居中**:将项目对齐轴的中心
4.  **拉伸**:填充轴(这是默认值)

[![4 possible alignment values and what they do](../Images/751cbc061049ef3b86a35980088a2efe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pxpnhLNf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.com/images/2018/remember-css-grid-properties/alignment-values-2.png) 

<figure>

<figcaption>4 种可能的对齐值及其作用</figcaption>

</figure>

`justify-self`和`align-self`与`justify-items`和`align-items`做同样的事情。不同之处在于，它只允许您更改一个网格项目的对齐方式。

```
.grid-item {
  justify-self: /* some value */;
  align-self: /* some value */;
} 
```

## 隐含网格

假设您创建了一个 CSS 网格，但是没有足够的行数。在下面的例子中，我只为三个项目创建了一个明确的网格。(3 列 1 行)

```
.grid {
  display: grid; 
  grid-template-columns: 1fr 1fr 1fr;
  grid-template-row: 3em;
} 
```

但是我有六件物品！

```
<div class="grid">
  <div class="grid-item"></div>
  <div class="grid-item"></div>
  <div class="grid-item"></div>
  <div class="grid-item"></div>
  <div class="grid-item"></div>
  <div class="grid-item"></div>
</div> 
```

当你的显式网格中没有足够的空间时，CSS Grid 会自动帮你创建额外的网格。默认情况下，它会创建更多的行。

如果你想切换网格方向，你可以将`grid-auto-flow`设置为`row`。

这种自动创建的部分称为隐式网格。

您可以使用以下两个属性来调整自动创建的列或行:

1.  `grid-auto-columns`
2.  `grid-auto-rows`

```
.grid {
  display: grid; 
  grid-template-columns: 1fr 1fr 1fr;
  grid-template-rows: 3em; 
  grid-auto-rows: 6em;
} 
```

参见 [CodePen](https://codepen.io) 上 Zell Liew ( [@zellwk](https://codepen.io/zellwk) )的笔[隐格](https://codepen.io/zellwk/pen/yxQrJb/)。

### 如何记忆隐含网格

`auto`是你要提防的关键词。

1.  `template`创建明确的网格
2.  `auto`创建隐式网格

我经常使用隐式网格。我将在另一篇文章中分享我如何使用 CSS Grid。

## 包装完毕

对于 80%的网格来说，这几乎是你需要知道的所有 CSS 网格属性！以下是您所学属性的总结:

1.  创建网格
    1.  明确:
        1.  `grid-template-columns`
        2.  `grid-template-rows`
        3.  `grid-template-areas`
    2.  隐式:
        1.  `grid-auto-columns`
        2.  `grid-auto-rows`
2.  缺口
    1.  `grid-column-gap`
    2.  `grid-row-gap`
    3.  `grid-gap`
3.  在网格中定位项目
    1.  `grid-column`
    2.  `grid-row`
4.  对齐事物
    1.  `justify-content`
    2.  `align-content`
    3.  `justify-items`
    4.  `align-items`
    5.  `justify-self`
    6.  `align-self`

希望这有助于你记住 CSS 网格！万事如意！

* * *

感谢阅读。这篇文章最初发表在我的博客上。如果你想要更多的文章来帮助你成为一个更好的前端开发者，请注册[我的时事通讯](https://zellwk.com)。