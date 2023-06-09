# 更好的带有方框阴影的 CSS 轮廓

> 原文：<https://dev.to/hybrid_alex/better-css-outlines-with-box-shadows-1k7j>

我想我们都同意默认的浏览器轮廓样式不是最好的设计实现。我还认为我们可以同意，它可能不漂亮，但它的功能确实很好。

经常可以看到人们完全去掉轮廓，而不用任何其他视觉指示来代替它。这是不好的做法，会损害键盘用户的用户体验。

为了安抚我们的设计师，同时保持我们的网站可访问性，让我们删除默认的轮廓样式，并替换为如下所示的框阴影实现。

```
input:focus {
  outline: 0;
  box-shadow: 0 0 0 3px rgba(0, 123, 255, .5);
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们可以看到，当输入被聚焦时，我们将 outline 设置为 0，并添加了一个 box-shadow 声明来替换它的功能。作为一个额外的奖励，使用框阴影也遵循边界半径声明。因此，如果我们的输入有一个 4px 的边界半径，我们的仿轮廓将不会有一个角落的差距。

查看 [CodePen](https://codepen.io/alexcarpenter/pen/vaPwaa) 示例，看看它是如何工作的。

[https://codepen.io/alexcarpenter/embed/vaPwaa?height=600&default-tab=result&embed-version=2](https://codepen.io/alexcarpenter/embed/vaPwaa?height=600&default-tab=result&embed-version=2)