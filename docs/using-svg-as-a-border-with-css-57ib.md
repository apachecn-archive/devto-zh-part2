# 使用 Svg 作为 CSS 的边框

> 原文：<https://dev.to/jackharner/using-svg-as-a-border-with-css-57ib>

你曾经想要一个比堆叠`border`元素更酷的边框吗？不用担心，今天我将带您浏览一下`border-image` CSS 属性。

这是我们将要切割成边框的图像:

[![Heres Our Fancy Border](img/211b50d2b9efd3c21f521d4d3bcc7cb2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--isLBn9ec--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://harnerdesigns.com/wp-content/uploads/2018/08/border.svg)

如果您正在跟进，请在此下载它。

这也适用于光栅图像，但是由于缩放问题，最好使用 SVG。

## 边框-图像

你需要设置`border`和`border-image`属性，其中`border`设置边框图像的宽度(并作为后备，尽管浏览器支持[非常好](https://caniuse.com/#search=border-image))。

```
.border{
    border:20px solid;
    border-image:url(border.svg);
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/jackharner/embed/QBZdGZ?height=600&default-tab=result&embed-version=2](https://codepen.io/jackharner/embed/QBZdGZ?height=600&default-tab=result&embed-version=2)

显然，虽然有趣，但这不是预期的结果，所以我们还有几个步骤。

## 边框-图像-切片

[border-image-slice](https://www.w3schools.com/cssref/css3_pr_border-image-slice.asp) 属性告诉浏览器在哪里剪切图像以创建不同的边缘。它最多接受 4 个无单位数字或百分比。

这些值从图像的顶部、右侧、底部和左侧边缘开始测量(按此顺序)。如果其中一个单元丢失，它将镜像另一边。

```
// These are all the same.
border-image-slice: 0 40;
border-image-slice: 0 40 0 40;
border-image-slice: 0 40 0; 
```

Enter fullscreen mode Exit fullscreen mode

[![Slicing the image](img/208c5c940b4ce14ffc5e189247c5bc6c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lVXlaeJ---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://harnerdesigns.com/wp-content/uploads/2018/08/Border-Image-Slice.png)

所以，回到我们的例子，因为我们使用的图像是对称的，所以很容易切片:

```
.border{
    ...
    border-image-slice:170;
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/jackharner/embed/oMaBpZ?height=600&default-tab=result&embed-version=2](https://codepen.io/jackharner/embed/oMaBpZ?height=600&default-tab=result&embed-version=2)

哒哒！！您有一个自定义图像边框。还有几个其他的属性可以影响边框对缩放和其他事情的反应。查看来自 CSS-Tricks 的这篇文章[,这篇文章对所有这些做了更详细的介绍。](https://css-tricks.com/almanac/properties/b/border-image/)

## 用例

没有人希望他们的边界上有一条弯弯曲曲的线，但这就是我在学习边界图像时所做的工作。

[![Back To School Chalkboard](img/d2373e3cceca4bc5f7b37d199ed045b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iJRpA6Vn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://harnerdesigns.com/wp-content/uploads/2018/08/back-to-school-chalkboard.png)

框架和粉笔是构成边界的 SVG 图像的一部分。这使得容器保持黑板的外观，不管里面的内容是什么样的布局或大小。

### 你见过自己使用这个吗？为什么或为什么不？