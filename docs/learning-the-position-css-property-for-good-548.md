# 永远学习 position CSS 属性

> 原文：<https://dev.to/winnercrespo/learning-the-position-css-property-for-good-548>

当我从头开始创建一个布局时，我发现自己向`position` CSS 属性和 DPP(方向位置属性，我只是编造了这个缩写)顶部、右侧、底部和左侧属性添加了不同的值，希望它们会神奇地看起来像我期望的那样。也许你也是。

最好能知道它们能做什么，这样我们就知道如何使用它们。

首先，定位元素的值不同于默认的`position: static`。换句话说，将`position`设置为`relative`、`absolute`、`fixed`或`sticky`的元素。

也就是说，我们可以说`position`定义了元素在页面上的位置。另一方面，DPP 在他们指定的方向上添加偏移。

让我们看看每一个是如何工作的。

## `static`

这是默认值，意味着将根据页面流放置元素。正因为如此，民进党才不会影响它。

## `relative`

元素也将根据页面流放置，但是现在您可以使用 DPP 添加相对于其自身的偏移。正如我们在下面的动画中看到的，该元素应该使用的空间被保留，即使在添加偏移后，其他元素也不受影响。

[https://codepen.io/wistcc/embed/mjXgjq?height=600&default-tab=result&embed-version=2](https://codepen.io/wistcc/embed/mjXgjq?height=600&default-tab=result&embed-version=2)

## `absolute`

您可以使用该值将元素准确地放置在您想要的位置，同时，该元素将从页面流中删除。该位置将相对于下一个带有`relative`或`absolute`定位的父元素。如果没有这样的父级，它将作为下一个示例相对于页面本身放置。

[https://codepen.io/wistcc/embed/BPYEOV?height=600&default-tab=result&embed-version=2](https://codepen.io/wistcc/embed/BPYEOV?height=600&default-tab=result&embed-version=2)

## `fixed`

元素是相对于视口定位的，因为滚动时视口不会改变，所以元素将固定在您放置它的确切位置。同样，就像`absolute`一样，元素从页面流中移除，并且它将与下一个具有`relative`或`absolute`定位的父元素相关。

[https://codepen.io/wistcc/embed/EpQJdP?height=600&default-tab=result&embed-version=2](https://codepen.io/wistcc/embed/EpQJdP?height=600&default-tab=result&embed-version=2)

## `sticky`

它的行为类似于`fixed`值，但是相对于自身，因为元素是根据页面流放置的。在这个例子中，我们可以注意到，一旦我们能够看到元素，我们继续滚动，它将被固定。

[https://codepen.io/wistcc/embed/bjLJmm?height=600&default-tab=result&embed-version=2](https://codepen.io/wistcc/embed/bjLJmm?height=600&default-tab=result&embed-version=2)

我希望现在你能够使用这些属性，确切地知道它们是做什么的。

快乐编码。