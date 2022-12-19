# 背景图像渐变叠加

> 原文：<https://dev.to/hybrid_alex/background-image-gradient-overlay-534f>

五年前，我制作了一个 [CodePen](https://codepen.io/alexcarpenter/pen/LveDx/) 来演示如何在背景图像上应用渐变叠加。从那以后，这支笔被浏览了 294804 次，被点赞 705 次。

由于它仍然每天被引用，我想我应该给它一个适当的记录。

首先让我们创建我们的横幅元素，我们将应用我们的背景图像和梯度覆盖。我们还将为横幅中的元素添加一个额外的内容包装器，稍后将会引用。

```
<div class="banner">
  <div class="banner__content">
    <h1>Banner Heading</h1>
    <p>Banner description</p>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们将背景图像应用到横幅元素中，并将最小高度设置为 400 像素。

```
.banner {
  position: relative;
  width: 100%;
  min-height: 400px;
  background: url('http://unsplash.it/1200x800');
  background-position: center center;
  background-repeat: no-repeat;
  background-size: cover;
} 
```

Enter fullscreen mode Exit fullscreen mode

为了应用我们的渐变叠加，将使用一个[伪元素](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-elements)。我们绝对定位覆盖，以涵盖我们的横幅 100%的宽度和高度。

```
.banner:after {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-image: linear-gradient(to bottom right, red, blue);
  opacity: 0.6;
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们重新看看横幅内容包装。因为我们用一个伪元素来应用渐变叠加，你有时会遇到在渐变叠加下面显示的横幅内容的问题。为了解决这个问题，我们用一个包装器把所有的内容包装在我们的横幅里面，并设置它接收一个比覆盖图高的`z-index`,如下图所示。

```
.banner__content {
  position: relative;
  z-index: 10;
} 
```

Enter fullscreen mode Exit fullscreen mode

正如在第一条评论中提到的，您可以省略伪元素，使用多种背景。这也很好，但是去除了悬停时渐变动画的能力，这是一个常见的用例。

[https://codepen.io/alexcarpenter/embed/LveDx/?height=600&default-tab=result&embed-version=2](https://codepen.io/alexcarpenter/embed/LveDx/?height=600&default-tab=result&embed-version=2)

查看原[码笔](https://codepen.io/alexcarpenter/pen/LveDx/)。