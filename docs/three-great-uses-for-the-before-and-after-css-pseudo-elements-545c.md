# CSS 伪元素::before 和::after 的三大用途

> 原文：<https://dev.to/brob/three-great-uses-for-the-before-and-after-css-pseudo-elements-545c>

众所周知，我是::before 和::after 伪元素的粉丝。在之前的帖子中，我用它们为[创建了暗覆盖图。](https://bryanlrobinson.com/blog/2018/04/30/how-to-css-after-elements-for-background-overlays/)

然而，除此之外，它们还有很多用途。这篇文章概述了我最喜欢的叠加之外的三个用途。

但是首先快速解释一下伪元素是如何工作的。

## 注意实际使用::after 和::before

当且仅当这些元素的`content`属性有值时，浏览器才会将这些元素呈现为“生成的内容”。该值可以设置为空字符串:`content: "";`。

当浏览器将这个元素插入到 DOM 中时，它会将它插入到用于选择器的元素中。这是规范中的定义:

> **:**之前表示紧跟在*原始元素*的实际内容之前的可样式化的子伪元素。
> 
> **:**之后表示紧跟在*原始元素*的实际内容之后的可样式化的子伪元素。

默认情况下，这个新元素将是一个内联元素。将元素插入 DOM 后，可以像修改任何其他元素一样修改它。这给了我们很大的控制力去享受各种乐趣。

*重要提示:并不是所有的浏览器/屏幕阅读器组合都能阅读你放在伪元素`content`中的内容。这应该只用于样式元素。真正的内容应该总是添加到页面的标记中。*

## 在某些类型的链接旁边添加图标

如果你想给你的用户更多一些关于链接将要做什么的视觉信息，你可以使用::after 来添加一个图标而不添加标记。

向非绝对链接添加“外部链接”图标。

```
a[href^="http"]::after {
    background-image: url(https://s3-us-west-2.amazonaws.com/s.cdpn.io/161359/open-in-new.svg);
    background-size: contain;
    content:"";
    display: inline-block;
    vertical-align: middle;
    width: 1em;
    height: 1em;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这段代码中，我们可以找到任何包含以 http 开头的 href 元素的锚标记。这假设您将内部链接写成相对路径。

这种方法的另一个很好的用例是 pdf。

```
a[href$=".pdf"]::after {
    content: " (pdf)";
    font-size:  .8em;
    color: tomato;
} 
```

Enter fullscreen mode Exit fullscreen mode

对于任何以。pdf，我们可以追加字符串“(pdf)”This ::after 元素比 image 更容易控制，因为我们有完整的 CSS 控制，可以调整字体大小、颜色和任何其他属性。

想了解更多关于这些选择器的神奇之处，请阅读这篇关于 CSS 技巧的极好的入门文章。

[在 CodePen 上玩这些](https://codepen.io/brob/pen/zLJRjQ?editors=1100)

## 给容器添加有趣的“边框”

[![Full Gradient border](../Images/7565020de573e368c4d06a9d28ecccb5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0qAAmMgN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bryanlrobinson.com/images/pseudo-element-full-border.png)

[https://www.youtube.com/embed/_Vtvc0JIgQ8](https://www.youtube.com/embed/_Vtvc0JIgQ8)

在胡迪尼画图 API 登陆所有浏览器之前，你可能会觉得你的元素被非常无聊的边框卡住了。使用简单的 CSS 和::before 和::after，可以给所有浏览器带来一些有趣的想法。

```
.related-article {  
    padding: 20px;
    position: relative;
    background-image: linear-gradient(120deg,#eaee44,#33d0ff);
}

.related-article * {
    position: relative; // Set stacking context to keep content on top of the background
}

.related-article::before {
    content: "";
    background-color: #fff;
    display: block;
    position: absolute;
    top: 10px;
    left: 10px;
    width: calc(100% - 20px);
    height: calc(100% - 20px);
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们将背景渐变应用于父元素，并使用::before 元素用简单的背景色“裁剪”内部。尽管是两个矩形，但这给出了边界的外观。要获得适合您的边框的大小，只需要一些简单的数学。

通过定位伪元素 absolute，我们可以控制它的位置。Sass 可以通过变量和数学函数使这种数学变得更简单，如果你喜欢的话。

[在 CodePen 上玩这个](https://codepen.io/brob/pen/JBmKqN?editors=1100)

如果我们希望标题下面有漂亮的小边框，而不是全边框，该怎么办？

我们可以使用::after 元素来实现这一点。

```
.cool-border::after {
    content: "";
    display: block;
    height: 7px;
    background-image: linear-gradient(120deg, #e5ea15, #00c4ff);
    position: absolute;
    top: calc(100% + 5px);
    left: 50%;
    width: 45%;
    transform: translateX(-50%) skew(-50deg);
}

.cool-border {
    position: relative;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们还放置了一个伪元素 absolute。我们的“边界”的大小由新元素的高度决定。如果这是一个右边或左边的“边框”,你将使用元素的宽度来决定大小。

由于这只是页面上的一个元素，我们也可以让我们的边界倾斜。长方形太过时了！

[在 CodePen 上玩这个](https://codepen.io/brob/pen/jpawRd?editors=1100)

## 添加不想添加标记的样式元素

A `<blockquote>`是一个很好的语义元素。让我们不要用额外的标记破坏它。

在 many ::after 示例(包括本文中的其他示例)中，您会看到伪元素`position: absolute`。没有规定说一定要这样。

让我们使用::before 和::after 作为网格项来给`<blockquote>`加上引号。

通过显式地将所有内容放在网格上，我们不必担心额外的包装器。我们还可以使用引号作为背景图片，并允许它们在我们的`grid-template-columns`中通过一个简单的`minmax`函数进行缩放。

[https://codepen.io/brob/embed/QBZKVE?height=600&default-tab=result&embed-version=2](https://codepen.io/brob/embed/QBZKVE?height=600&default-tab=result&embed-version=2)

## 我错过了什么？

使用::after 和::before 有很多好主意。我错过了什么？你在日常开发中使用过伪元素吗？

在这三个用例之外的另一个帖子上，有一个很好的讨论！去看看！

[![brob](../Images/5b5e9de500cf76ddabb094a375b16a68.png)](/brob) [## 是否使用 CSS ::before 和::after 伪元素？

### 布莱恩·罗宾逊 6 月 17 日 191 分钟阅读

#css #webdev #discuss](/brob/do-you-use-css-before-and-after-pseudo-elements-3e92)

## 报名参加我的伪元素课程:伪动力

[![Pseudo Power Cover Image](../Images/8095fc79943448ba448ef9ede4aa48ce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lw2zS3kS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d31ezp3r8jwmks.cloudfront.net/variants/aftsVnPtM1cNc1m5M3bBrEm9/d2e337a4f6900f8d0798c596eb0607a8e0c2fbddb6a7ab7afcd60009c119d4c7)

我正在完成一门关于伪元素的课程。如果您在下面的链接注册，当它启动时您会收到通知！

[注册以获得发布通知](https://store.codecontemporary.com/pseudo-power-using-the-powerful-css-before-and-after-pseudo-elements)