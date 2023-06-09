# 向 HTML - Transition 属性添加移动

> 原文：<https://dev.to/neshaz/add-movement-to-your-html---transition-property-46ck>

所以，我已经从网页设计中的文字和颜色转向了独特的、爆炸性的、爆炸式的设计...好吧，没那么多，但我已经搬走了。CSS 跟随并支持这些变化，正因为如此，我们有了**转换**选项。

## 关于过渡

transition 属性允许平滑地更改 CSS 属性值。
**需要定义一个将要改变的属性和效果的持续时间。**

```
 button{
    transition: background 1s;
    -webkit-transition: background 1s; /* Safari */
 } 
```

这两个值可以用通用属性`transition`或单个属性`transition-property`和`transition-duration`来定义。

我现在能记得的一些`transition-property`值是:

*   宽度，
*   身高，
*   颜色，
*   背景(颜色、图像、位置)，
*   转换(在下一篇文章中)，
*   边框(颜色、宽度)，
*   [位置](https://kolosek.com/css-position-relative-vs-position-absolute/)(上、下、左、右)，
*   文本([大小](https://kolosek.com/css-relative-font-size/)，粗细，阴影，字间距)，
*   保证金，
*   填充，
*   不透明，
*   能见度，
*   z 指数，
*   全部。

如果该值是用属性定义的，则定义一个以逗号分隔的 CSS 属性名称列表，该列表用于过渡效果。**值 all** 是缺省值，定义所有可以转换的属性都将转换。**在这种情况下，所有改变的属性都将有一个相同的`duration`(以及`delay`和`timing`，如果定义的话)。**

`transition-duration`属性非常简单，它定义了完成一个过渡效果需要多长时间，其值可以用秒(s)或毫秒(ms)来定义。

## 更多过渡选项

与过渡相关的其他属性有:

*   `transition-delay`
*   `transition-timing-function`

与 duration 属性非常相似，`transition-delay`是用秒(s)或毫秒(ms)来定义的，它指定了过渡效果开始的时间。与 duration 属性不同，这个属性可以是负数，如果是这样，它将在播放周期的中途开始。

`transition-timing-function`属性由以下函数定义:

*   **缓** -慢开始，快中间，慢结束，
*   **直线** -恒速、
*   **缓和** -缓慢开始，快速结束，
*   **放松** -快速开始，慢速结束，
*   **缓入缓出** -更明显的加速/减速曲线，
*   **三次-贝塞尔(n，n，n，n)** -你可以编写自己的用 4 个坐标定义的函数([三次贝塞尔函数](http://cubic-bezier.com/))。

    button{
    转场:背景 1s 渐出 2s；
    -WebKit-过渡:后台 1s 渐出 2s；/*狩猎*/
    }

**使用通用属性`transition`你可以定义所有四个属性，只要记住第一次定义总是`transition-duration`。**

可以设置多个转场。使用`,`分割不同的过渡。

```
 button{

     transition: background 1s ease-in-out 2s, width 2s linear;

     -webkit-transition: background 1s ease-in-out 2s, width 2s linear; /* Safari */

   } 
```

## 
  
触发

关于转换，要记住的主要事情是，这个属性是之前定义的，只有当它被触发时才会发生(如果用 transition-property 定义的属性被更改)。

您可以使用伪类直接触发 CSS 转换，如:hover、:focus 和:active，或者通过 JavaScript 添加或删除类。

## 结论

这个属性得到了广泛的支持，所以请使用它！如果你需要一些灵感，看看这个例子。

这篇文章最初发表在 [Kolosek 博客](https://kolosek.com/css-transition/)上。