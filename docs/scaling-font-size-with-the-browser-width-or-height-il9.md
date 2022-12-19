# 用浏览器的宽度或高度缩放大小(例如，字体大小)

> 原文：<https://dev.to/benjaminblack/scaling-font-size-with-the-browser-width-or-height-il9>

我的帖子通常是我自己的笔记和参考资料，我在这里发布，希望其他人会觉得有用。

* * *

**弃用**:现代浏览器中的 CSS 提供了一个内置机制，用`clamp()`函数来做这个[。](https://developer.mozilla.org/en-US/docs/Web/CSS/clamp())

* * *

TL；博士:作为 SCSS 函数:

```
// y1: smallest size (px)
// x1: smallest browser width (px)
// y2: largest size (px)
// x2: largest browser width (px)
// e.g. 12px @ 375w to 24px @ 1280w = interpolate(12, 375, 24, 1280)
@function interpolate($y1, $x1, $y2, $x2) {
    $m: ($y2 - $y1) / ($x2 - $x1);
    $b: $y1 - $m * $x1; // or $y2 - $m * $x2

    @return calc((#{$m}) * 100vw + (#{$b}) * 1px);
}

h1 {
    font-size: interpolate(12, 375, 24, 1280);
    width: interpolate(120, 300, 800, 1280);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果对象的一个维度应随视口宽度(或高度)线性缩放，例如将字体大小从 375 瓦的 12px 缩放到 1280 瓦的 24px，则可以使用斜率截距线方程(`y = mx + b`)来计算插值大小，其中`y`是结果大小(以像素为单位)，`x`设置为`100vw`(或`vh`)，而`b`在`px`中测量。

这是因为`100vw`正好是视窗宽度的 100%,即使视窗宽度变化。因此，当视口为 375 瓦时，100 瓦就等于 375 像素。同样，当视口为 1280 瓦时，100 瓦等于 1280 像素。

所以，继续字体大小的例子，在斜率截距公式中，当视口宽度为 375w 时，浏览器将`100vw`视为`375px`，因此该公式实际上是`y = m * 375px + b * 1px`，同样在 1280w、`y = m * 1280px + b * 1px`，以及之间的每个值。

使用 [Wolfram Alpha](http://wolframalpha.com/) 计算通过两点`(x1, y1)`和`(x2, y2)`的直线方程，使您的生活变得更加简单，其中`x1`和`x2`是视窗尺寸(宽度或高度)的大小，`y1`和`y2`是这些视窗尺寸的目标尺寸。

继续上面的例子，随着浏览器宽度从 375w 变为 1280w，字体从 12px 缩放到 24px，点是`(375, 12)`和`(1280, 24)`。所以我们想要通过`(375, 12)`和`(1280, 24)`的直线的方程。

对于通过这两点的直线， [Wolfram Alpha 返回斜率截距方程](http://www.wolframalpha.com/input/?i=line+through+(375,+12)+and+(1280,+24)) :

```
y = (12/905)x + (1272/181) 
```

Enter fullscreen mode Exit fullscreen mode

写成 CSS 规则，这是:

```
font-size: calc((12/905) * 100vw + (1272/181) * 1px); 
```

Enter fullscreen mode Exit fullscreen mode

检查两种视窗尺寸(375 和 1280)下的结果:

```
(12/905) * 375 + 1272/181 = 12
(12/905) * 1280 + 1272/181 = 24 
```

Enter fullscreen mode Exit fullscreen mode

在这两个浏览器尺寸之上或之下，如果尺寸突然改变，使用媒体查询。