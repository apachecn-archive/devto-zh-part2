# 用 CSS 滤镜给白色图像着色

> 原文：<https://dev.to/domysee/coloring-white-images-with-css-filter-4bd1>

最近，我不得不改变一个白色图像的颜色。因为这个图像是由一个库渲染的，不允许用另一个替换它，所以必须用 CSS 来完成。虽然有一个相对简单的解决方案，但事实证明，提出这个方案是一个相当大的挑战。

让我们开始吧。

## 应该不难！

只要加一个`hue-rotate`滤镜，应该就可以了。

[https://codepen.io/domysee/embed/XYzaor/?height=600&default-tab=result&embed-version=2](https://codepen.io/domysee/embed/XYzaor/?height=600&default-tab=result&embed-version=2)

但事实并非如此。当然不是，那太容易了。图像仍然是白色的。

## 为什么没用？

在检查了几次过滤器是否真的被应用后，我恍然大悟。

白色的透光度值为 100%，所以改变色相不会有任何作用。其实白色的色相值为 0，就是红色。所以我应该事先知道改变色调是行不通的，我真傻！

## 接着改变光度

因为 100%的亮度是白色，0%是黑色，所以 50%应该产生丰富的颜色。让我们试试那个。

通过`brightness`滤镜可以改变发光度，根据给定的因子改变亮度。

[https://codepen.io/domysee/embed/xzPLNZ/?height=600&default-tab=result&embed-version=2](https://codepen.io/domysee/embed/xzPLNZ/?height=600&default-tab=result&embed-version=2)

好吧，有些事情改变了，这很好。但结果并不如预期。没有颜色。图像只是灰色的。

## 发生了什么？

正如预期的那样，透光率现在是 50%。但是我忘了饱和度。因为白色有 100%的亮度，我认为饱和度也应该是 100%。可惜是 0%，造成了灰颜色。

## 因此改变饱和度

如果有一个`brightness`过滤器，那么肯定有一个`saturate`过滤器。是的，有！

让我们设置饱和度为 100%然后:

[https://codepen.io/domysee/embed/LrOzVx/?height=600&default-tab=result&embed-version=2](https://codepen.io/domysee/embed/LrOzVx/?height=600&default-tab=result&embed-version=2)

同样，什么都没有改变。

事实证明，`saturate`滤波器的工作方式与`brightness`滤波器相同。它只是根据给定的*因子*改变数值。这是个问题。就像小学数学教我们的，*一切*乘以`0`就是`0`。

这使得`saturate`过滤器在此用途上毫无用处。

## 放弃的时候到了？

还没有！还有一些其他的过滤器可以尝试。让我们浏览一下:

*   `blur`:明显不变色
*   在灰色图像中不做任何事情
*   `drop-shadow`:也不改变颜色
*   `grayscale`:嗯，不能让一个灰色的图像变灰 _ 呃 _
*   `hue-rotate`:重点是让色调可见，一个*不同的*色调不会改变任何事情
*   可悲的是，反转灰色只会导致不同的灰色变化
*   让另一种颜色发光是可能的，但这不是我想要的
*   那是最后一个了。最后，它看起来可以工作！

MDN 文档的例子看起来很有希望。

[![Sepia Filter Example](img/979fae69e379488926f062df88eab196.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--13rC1-WN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://domysee.com/blogposts/21/sepia-filter.png)

让我们试一试:

[https://codepen.io/domysee/embed/rKYGLO/?height=600&default-tab=result&embed-version=2](https://codepen.io/domysee/embed/rKYGLO/?height=600&default-tab=result&embed-version=2)

这改变了什么吗？它看起来像白色的。*差不多*。通过用颜色选择器检查，我发现实际的颜色是`hsl(60, 100%, 97%)`。

这意味着，我们已经饱和了！终于有结果了🎉

从那里，现在可以应用其他过滤器来实现所需的结果。

## 让我们看看颜色！

关于试错法，我已经写得够多了。让我为你节省一些时间，告诉你如何得到一个纯粹的红色:

[https://codepen.io/domysee/embed/rKYzJg/?height=600&default-tab=result&embed-version=2](https://codepen.io/domysee/embed/rKYzJg/?height=600&default-tab=result&embed-version=2)

在我看来，这是最容易得到你想要的颜色的颜色。

例如，要得到绿色，只需在末尾加一个`hue-rotate(120deg)`。

## 包装完毕

用滤镜改变白色是一种讨厌的颜色。使用典型的滤镜，不可能创建除灰度以外的颜色。

`sepia`滤镜可能不是为此目的而设计的，但它拯救了世界！

## 还有一件事:黑

要把黑色变成一种颜色，先把它变成白色，然后从那里开始:

```
img {
  filter: invert(1) brightness(50%) sepia(100%) saturate(10000%);
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

在 Twitter 上关注我，了解更多我的想法、文章、项目和工作。