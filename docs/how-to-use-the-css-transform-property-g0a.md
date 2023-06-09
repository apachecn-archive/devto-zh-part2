# 如何使用 CSS Transform 属性

> 原文：<https://dev.to/brittanmcg/how-to-use-the-css-transform-property-g0a>

# 如何使用 CSS 变换属性

我正着手学习 CSS 动画，而`transform`属性是我清单上的第二部分。
我这篇文章的目标是教授`transform`的基础知识(这样我可以更好地学习)。
这个属性非常酷，学习起来非常有趣。如果你不熟悉的话，我建议你可以尝试一下。

### 基础知识

在阅读本文之前，您应该对 CSS 和 HTML 有一个基本的了解。

## 简介

我学习 CSS 动画的旅程有一个非常重要的学习元素。我主要是寻找和过滤与这个概念相关的信息。在我能说我“知道”事情之前，列出需要理解的学习理解的清单。做那些事情，无论是读文章、做练习、看视频、看书等等。一个接一个。然后我到处玩。做这件事的时候我没有设定目标。只是为了玩最基本的感觉。打碎东西。玩得开心。这一步主要是提出问题。然后研究并回答我的问题。然后教书。那可以是一篇博客，教一个朋友，或者只是深入解释这个概念。

在这篇文章中，我可能会忽略一些其他的概念。这个帖子主要只是针对 CSS `transform`属性。然而，我也会经常使用`transition`属性。如果你想进一步了解，我有另一篇关于[的博客文章。](https://dev.to/brittanmcg/how-to-use-the-css-transition-property-20mm)

### 变换

CSS `transform`有 6 个主要属性值:

*   规模
*   翻译
*   辐状的
*   斜交
*   远景
*   矩阵

事实上，它有更多的方式，因为许多这些值有一个 X，Y 和 Z 属性。为了简洁起见，我不会显示整个列表。如果你想看一看，你可以在这里看到它。

我将在下面解释每个值的作用。

## 让我们玩玩这些

下面，我将构建 7 个示例卡。每个人都将表演它所命名的动画。

## 启动器代码

```
<div class="container">
  <div class="translate">
    <p>translate</p>
  </div>  
</div> 
```

```
@import url(https://fonts.googleapis.com/css?family=Open+Sans);

body {
  background: lightblue;
  font-family: 'Open Sans';
}

.translate {
  margin: 50px auto;
  background: papayawhip;
  width: 150px;
  height: 150px;
  border-radius: 20px;
}

p {
  margin-top: 60px;
} 
```

### 添加悬停伪类

这会将元素沿 Y 轴下移 100 像素。虽然根据你的使用情况有点酷和有用，但它不是很优雅，动画也不是很好。接下来我们将添加一个`transition`来平滑地制作动画。

```
// ...

.translate:hover {
  transform: translateY(100px);
} 
```

```
// ...

.translate {
  // ...
  transition: 1s ease;
}

.translate:hover {
  transform: translateY(100px);
} 
```

### 刻度

添加前一个 div 的副本，并将该类命名为我们将要更改的属性。我不会在下面的例子中包含这些代码，因为它们是一样的。

```
<div class="container">
  <div class="translate">
    <p>translate</p>
  </div>  
  <div class="scale">
    <p>scale</p>
  </div>  
</div> 
```

这里我们将添加一个`text-align` `center`到`.container`中，这样元素将会在这个容器的中心。
然后我们将改变`margin`和`display`的属性，让它们坐在一起。

```
// ...
.container {
  text-align: center;
}

.translate, .scale {
  margin: 50px 10px auto;
  display: inline-block;
  background: papayawhip;
  width: 150px;
  height: 150px;
  border-radius: 20px;
  transition: 1s ease;
}

p {
  margin-top: 60px;
}

// ... 
```

然后我们将在 hover 上添加一个`scale`属性。这会把它缩小到原来的 1/2。因为我只提供了一个值，所以它对 x 和 y 坐标都使用这个值。

```
// ...
.scale:hover {
  transform: scale(.5);
} 
```

如果我给它提供第二个值，那么它会把这个值分别应用到 x 和 y 坐标上。

```
// ...
.scale:hover {
  transform: scale(.5, 2);
} 
```

### 旋转

在您复制了 html 并重命名了要旋转的 p 标签的类和内部文本之后，然后将 rotate 类添加到 div 的基本样式中，如下所示:
`.translate, .scale, .rotate {`

然后我们将添加一个`:active`伪类到`.rotate`类中。这样，当我们点击该元素时，它就会旋转。
`rotate`值采用转弯值或度数值。

`rotate`取负值或正值。例如，如果你给这个`transform: rotate(-360deg)`,它会逆时针转动

```
// ...

.rotate:active {
  transform: rotate(360deg);
}

// This accomplishes the same thing as above
.rotate:active {
  transform: rotate(1turn);
} 
```

### 歪斜

根据给定的值，`skew`值会将元素“倾斜”到一边。与许多这些函数一样，您可以指定`scaleX`、`scaleY`或`scale`。如果你只给`scale()`传入一个值，它会给 Y 轴一个 0 值，然后只缩放 X 轴。

`skew`值也以度数、圈数或半径为参数。这个签名是这样的:`skew(10deg)`或者`skew(-0.06turn, 18deg)`或者`skew(.312rad)`。摘自 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/CSS/transform-function/skew)

倾斜可能有点棘手，因为您可能只想倾斜元素的容器，而不是内部内容。在这种情况下，你必须反向倾斜。

下面你可以看到一个倾斜 div 的例子，但是保持里面的文本不倾斜。

1.  我们将复制我们的 html，像这样改变我们的类名和内部文本
2.  我们将把`.skew`添加到基础 CSS 中不断增长的目标元素列表中

现在，我们将向该元素添加一个倾斜值。

```
// ...

.skew:hover {
   transform: skew(10deg)
} 
```

问题是它也扭曲了内部文本。我们可以通过给它一个相同数量的相反值，并以该元素为目标来补救。

```
.skew:hover p {
  transform: skew(-10deg);
} 
```

这看起来仍然有点奇怪，因为文本在悬停时会自动更正。我不确定是否有更自然的方法来解决这个问题。在这个人为的例子中，我想不出一个。如果有读者有更好的方法，请在评论中告诉我。

### 透视

`perspective`值用于给 3d 元素一些透视效果。您的目标是 z-index，这样它看起来就像元素从页面中弹出一样。透视的原点是中心。您可以使用`perspective-origin`值对此进行更改。但是我将使用`transform-origin`属性，因为我将在转换属性和透视属性上使用它。

对于这个例子，我将同时使用`perspective`和`transform-origin`值。我会让它看起来像是从底部向上翻出来的。

复制上一个示例中的代码，并添加相应的类和支持 css。

然后将其添加到悬停状态。

```
// ...
.perspective {
  transform-origin: top;
}

.perspective:hover {
  transform: perspective(300px) rotateX(50deg);
} 
```

### 矩阵

`matrix`函数稍微复杂一点，不可否认是我花时间最少的一个。它还要求你对线性代数和笛卡尔坐标系统有很好的理解(我没有)，以便真正理解正在发生的事情。

有一个`matrix`和一个`matrix3d`功能。每个转换函数背后都有一个`matrix`函数。例如，当你呼叫`rotateX(30deg)`时，你实际上是在说`matrix(0.86602540, 0.50000000, -0.50000000, 0.86602540, 0, 0);`。这里有一个很酷的`rotate` [的矩阵计算器，可以看到你旋转的数值。](http://www.boogdesign.com/examples/transforms/matrix-calculator.html)

2d 转换是一个 3x3 的整数矩阵，而 3d 转换是一个 4x4 的矩阵。因此，参数被分别传递给每个函数。

非常感谢这篇由[蒂芙尼·布朗](https://twitter.com/webinista)撰写的深度文章[理解 CSS 转换矩阵](https://dev.opera.com/articles/understanding-the-css-transforms-matrix/)。

在这里，我将使用`matrix`函数使这张卡片向上翻转到左边。

我们将像前面的卡片一样添加 html 和 css，然后您可以将这个`:hover`状态添加到`.matrix`类中

```
// ...
.matrix:hover {
  transform: matrix(.5, -1, 0, .5, 0, 0);
} 
```

`matrix3d`可以使用 CSS 创建二维的三维效果。签名采用 16 个参数作为坐标。

我将复制我刚才为 Matrix 做的所有内容，并在类名和文本中添加 3d。然后，我们将使用`matrix3d`功能在底部向上、向右和向外旋转这张卡。我们将在`:active`pseudo 类中这样做。

```
// ...
.matrix3d:active {
  transform: matrix3d(0.8535533905932737, 0.4999999999999999, 0.14644660940672619, 0, -0.4999999999999999, 0.7071067811865476, 0.4999999999999999, 0, 0.14644660940672619, -0.4999999999999999, 0.8535533905932737, 0, 22.62994231491119, -20.3223304703363, 101.3700576850888, 1);
} 
```

下面是所有这些例子的一笔。
[https://codepen.io/konamax123/embed/aGZGVG/?height=600&default-tab=result&embed-version=2](https://codepen.io/konamax123/embed/aGZGVG/?height=600&default-tab=result&embed-version=2)T2】

## 结论

我希望听到任何人对我对`transform`房产的理解的反馈。我写这篇文章是为了加强我对这个概念的了解。如果我有什么明显的错误，请让我知道。我很乐意用正确的信息更新这个帖子。

这是一篇来自[媒体](https://medium.com/@brittanmcginnis/how-to-use-the-css-transform-property-b3b31f31316f)的横帖