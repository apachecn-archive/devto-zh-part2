# 从头开始制作一个响应性网站的 6 个技巧

> 原文：<https://dev.to/canderson93/6-tips-for-making-a-responsive-website-from-scratch-4427>

[![6 Tips for making a responsive website from scratch](img/d7ae2af1e12f1192e05bff54287c0305.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QoRFHR-7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1534412027333-5e25ba9074f5%3Fixlib%3Drb-0.3.5%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ%26s%3Db01235b6f450c1f8a2ed8413f67b2b97)

当你开始使用 CSS 和 web 时，很容易进入 Bootstrap 或布尔玛这样的框架。它们是快速原型制作和掌握如何建立网站的极好工具。然而，这些框架是固执己见的，随着我们的成长，我们遇到了一个点，这是更多的痛苦而不是好处——如果我每次看到有人说“我只对列使用 bootstrap”时，我都有五分钱的话

从 CSS 库的世界中迈出第一步并从零开始构建自己的响应网站是一种痛苦的经历，但有一些简单的技巧可以让你获得让用户喜极而泣的体验。

## 始于头部

任何响应式设计要做的第一件事就是确保你的`<head>`中有正确的 meta 标签。如果你不这样做，你的小尺寸将只是你的桌面尺寸的缩小版本。

```
<meta name="viewport" content="width=device-width, initial-scale=1.0"> 
```

Enter fullscreen mode Exit fullscreen mode

## 有两种尺寸的图案

请注意，不仅仅是两种尺寸——您希望一种设计用于桌面，另一种用于您的*最小支持设备*。一个很好的目标是 iPhone 5s/SE。

我们想要两种设计的原因是，随着屏幕变得越来越小，我们想知道如何重新定位我们的内容，使之适合。在两个目标设计之间转换，可以更容易地权衡不同的方法，并且知道我们正在进行正确的权衡。

## 应用一些公理

好吧，这一步实际上并不是**所必需的，但在更简单的设计中，这一步可能是救命稻草。海登·皮克林有一篇[非常棒的博文](http://www.heydonworks.com/article/on-writing-less-damn-code)，讲述了一些漂亮的 CSS 技巧。我将提请大家注意我认为很棒的两点。** 

```
body * + * {
  margin-top: 1.5rem;
} 
```

Enter fullscreen mode Exit fullscreen mode

该选择器在所有元素之间放置一个边距。这将节省大量令人讨厌的间距，特别是当涉及到文本时——如果它不起作用，忽略它是微不足道的。

```
font-size: calc(1em + 1vw); 
```

Enter fullscreen mode Exit fullscreen mode

这个有点碰运气，但是可以超级有用。它让你的字体随着视窗大小缩放，而不会在小设备上低于`1em`。它很棒，因为它保证了你的字体大小对于每种设备大小都是合理的——如果你的设计要求你有特定的字体大小，那么这就不值得花力气了。

## 利用反应灵敏的单位

响应单元是相对于另一个单元计算的单元。这对于响应式设计非常有帮助，因为它让我们可以调整整个网站的大小，比如说，缩小屏幕的大小。我会列出一个响应单元的列表供你参考，否则一定要查看 CSS 上的 [Mozilla 开发者指南。](https://developer.mozilla.org/en-US/docs/Learn/CSS/Introduction_to_CSS/Values_and_units)

*   `em` -该单位代表当前字体大小。这可能是最常见的响应单元，对于像`padding`、`margin`、`line-height`这样的东西，以及基本上任何接近文本的东西都很有用。
*   `rem` -该单位与`em`相同，除了它是相对于默认字体大小，所以它不会受到继承字体大小的影响。这是一个设置其他`font-size`属性的非常好的单元，只是为了确保你不会意外地得到错误大小的文本。
*   `ex` -这是小写字母 x 的高度。这不是我曾经需要使用的东西，但它是一个很好的调整字体的单位。
*   `ch` -这是 0 字符的宽度。这不是你会在你的网站上到处散布的东西，但这是一种*美丽的*设置线条宽度的方式。`max-width: 75ch`，好的。
*   `%` -计算与父节点`width`或`height`相关的事物。描述它的行为可能很复杂，因为它使用的值取决于它所使用的属性——有几个很明显的例子，比如`width`和`height`,但除此之外，你需要用规范来检查[。](https://www.w3.org/)
*   `vw`和`vh` -这些单位计算视口宽度或高度的百分比值。这些通常比`%`属性更有用，因为我们能够更好地描述特定设备上的视图，并且在需要时混合使用`vh`和`vw`。

在屏幕尺寸之间移动时要记住的是*任何使用字体大小的单位实际上都是绝对的*。当然，如果你有一个相对于视图宽度的字体大小，这是个例外。

## 不要烘焙你的断点...

使用 CSS 时，断点是一把双刃剑。它们是你遇到的大多数问题的优秀解决方案，但是它们也充满了陷阱。Bootstrap 和其他框架在创建 css 断点时以设备宽度为目标，因此人们可以明确地以设备类型为目标。

你不应该这样做，否则每次苹果发布 1px 宽版 iPhone 时，你都必须重新审视你的设计。(如果你必须针对特定的设备，查看[这篇文章](https://medium.freecodecamp.org/the-100-correct-way-to-do-css-breakpoints-88d6a5ba1862))

断点的最佳实践是在设计停止工作时使用它们——这可能是在 400px 或 1000px。如果你的设计在整个范围内都是像素完美的，那么用户使用的设备突然变得无关紧要了。

Bootstrap 和其他框架*不*这么做的原因是因为他们不知道你的设计什么时候会停止工作——*但是你会*。从头开始构建时，你的设计和你的 CSS 之间的内聚性是你最大的优势——你永远不会想回去。

## ...实际上，根本不要写它们

如果可能的话，最好避免陷入需要编写断点的境地。通过使用`max-*`和`min-*`属性，你可以改变元素的行为而不需要断点:

```
section {
    width: 100vw;
    max-width: 100ch;

    margin: 0 auto;
    padding: 1.5em 1em;
} 
```

Enter fullscreen mode Exit fullscreen mode

这导致部分填满整个屏幕，直到屏幕能够容纳 100 个字符，然后它将内容浮动在中间。你不太可能完全没有断点*，但是你会惊讶有多少这样的机会:*提示:[查看显示:flex](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)**