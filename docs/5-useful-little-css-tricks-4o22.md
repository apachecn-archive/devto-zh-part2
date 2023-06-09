# 5 个有用的 CSS 小技巧

> 原文：<https://dev.to/flippedcoding/5-useful-little-css-tricks-4o22>

每个开发人员都会遇到 CSS 中那些烦人的小怪癖。它们会让你挠头好几天，想知道为什么你的元素有一个紫色的背景，并且在左下角。像这样的 CSS 问题会让你感到沮丧，特别是如果这是唯一阻碍你的事情。

当然，这些小怪癖还有很多，我无法一一列举，但这里有 5 个我遇到过的，比我希望的还要多。

*   **一行文本的垂直对齐。**
    *   几乎每次我尝试制作导航菜单时，都会出现这个问题。这个把戏比我想承认的要简单得多。如果你让你的菜单的高度和文本的行高一样，你就不会有额外的空间或者字母移位的问题。它应该是这样的:

```
.nav-element {
    height: 24px;
    line-height: 24px
} 
```

Enter fullscreen mode Exit fullscreen mode

*   确保图像调整后没有失真。
    *   有时你会得到一个奇怪比例的图像，无论屏幕大小如何，你都需要它来保持这些比例。你可以通过设置最大宽度为 100%和高度为自动。这样做是为了防止图像在较小的设备上溢出，并根据宽度计算图像的高度。现在，您不必担心您的图像会被拉伸或挤压。你可以用像这样简单的东西来做:

```
img {
    height: auto;
    max-width: 100%;
} 
```

Enter fullscreen mode Exit fullscreen mode

*   **重置所有浏览器默认样式。**

    *   当你有一个元素，无论你用 CSS 对它做什么，它总是错误的，浏览器可能会覆盖你的样式。浏览器有预设的边距、填充、边框和其他东西。这些真的会打乱你的布局。好的方面是有这么多的 CSS 重置表，你可以选择一个你喜欢的。CSS 重置表删除了所有的浏览器样式，因此您可以在每个浏览器上重新开始。这是我在我的网站上使用的一个，也是更广为人知的重置表之一:[https://meyerweb.com/eric/tools/css/reset/reset.css](https://meyerweb.com/eric/tools/css/reset/reset.css)
*   **压倒一切的风格。**

    *   在这里我要警告你。这是一种技术，应该只在你真正需要的时候使用。否则，当你以后试图更新你的风格时，会有混乱和悲伤。尽管如果你想让某种风格优先于其他风格，你可以使用`!important`规则。您可以将此规则添加到任何 CSS 属性中，它将始终优先于所有其他样式。这就是为什么你要在真正必要的时候才使用它。如果每个样式都是“重要的”，那么 CSS 的层叠流就会被打断，你就不知道样式是如何应用的了。下面是它在使用中的样子:

```
p {
    font-size: 24px !important;
} 
```

Enter fullscreen mode Exit fullscreen mode

*   **调整垂直屏幕高度。**
    *   你有没有想过用一个特定的元素或者一类元素来填满一个屏幕？你可以通过使用视图高度测量单位来实现。它的工作方式是，你将元素的高度设置为你希望它填充屏幕的百分比。你需要记住的一点是填充在不同尺寸的屏幕上是如何工作的。以下是使用视图高度的方法:

```
.screen-fill {
    height: 75vh;
} 
```

Enter fullscreen mode Exit fullscreen mode

希望这些 CSS 小技巧能帮助你更好或更快地开发。[除了定心元件的整个问题](https://flippedcoding.com/2018/01/17/how-to-center-an-html-element-in-a-div/)，这些技巧只是触及了表面。

感谢阅读！:)

* * *

嘿！你应该在推特上关注我，因为原因:[https://twitter.com/FlippedCoding](https://twitter.com/FlippedCoding)