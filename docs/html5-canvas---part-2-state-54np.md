# HTML5 画布-第 2 部分:状态

> 原文：<https://dev.to/gmartigny/html5-canvas---part-2-state-54np>

# 更改画布的状态

在[第一部分](//dev.to/gmartigny/html5-canvas---part-1-drawing-4bc5)中，我们已经看到了在每个循环中绘制相同的帧是多么的无聊。
这一次，我们要看看有什么可以把这个静态的黑色矩形变成一个可爱的移动的黑色小矩形。

## 转换

还记得我们之前看到的路径可以存储在变量中以便重用吗？嗯，它有一个小小的缺陷:你不能在事后改变它的位置。

如果我们不能改变路径的位置，让我们改变画布的位置！

### 放弃

画布状态的所有修改都是永久的和累积的。假设您移动了 10px，然后移动了 20 px，您总共移动了 30px，而不是 20px(例如，您可以从 CSS 中看到)。所以，如果你这样做，你需要在每次改变它的时候恢复状态。
当然，canvas API 允许使用`save`和`restore`。

```
ctx.save(); // Save the canvas' state

// Here goes as many changes as we want

ctx.restore(); // Restore to the last saved canvas' state 
```

### [翻译](//developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/translate)

很简单，`translate`移动画布(画布“上下文而非画布”节点)。

[//codepen.io/GMartigny/embed/YLOzwO/?height=500&default-tab=js,result&embed-version=2#?height=500&default-tab=result&embed-version=2](//codepen.io/GMartigny/embed/YLOzwO/?height=500&default-tab=js,result&embed-version=2#?height=500&default-tab=result&embed-version=2)

在这个例子中，我将路径位置设置为`[0, 0]`，然后使用自定义函数`getPositions`和`translate`将画布移动到定义的位置。最后，我用`save`和`restore`将它们包围起来。

### [旋转](//developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/rotate)

你猜对了，把画布转过来。在这里，事情会变得一团糟。
首先你要记住期望角度是以弧度为单位的顺时针方向(0:无，PI:半转，2 * PI:全转)。
接下来，你不能定义旋转中心，它总是原点(0，0)。如果你想使用另一个旋转中心，你必须先平移画布。
假设我们想从矩形中心旋转 45 度。

[//codepen.io/GMartigny/embed/vjzYPo/?height=500&default-tab=js,result&embed-version=2#?height=500&default-tab=result&embed-version=2](//codepen.io/GMartigny/embed/vjzYPo/?height=500&default-tab=js,result&embed-version=2#?height=500&default-tab=result&embed-version=2)

> 切角是怎么回事？

我开始溢出画布了，但是现在我们先不要担心这个。

### [刻度](//developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/scale)

几乎像`rotate`一样工作。还是那句话，你不能定义一个刻度中心，你得自己处理。

### [变换](//developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/transform)

这个函数允许你在一次调用中进行缩放、倾斜和平移。用起来真的很别扭。然而，这是唯一的偏斜方式。Sooo，真了不起...

## 画布大小

默认情况下，画布元素为(300x150)px。如果你尝试用 CSS 改变它的大小，它会被拉伸。然而，使用节点的属性`width`和`height`会增加它的大小。大多数时候，用 Javascript 更容易做到。
另外，画布元素的默认显示是`"inline"`，这很少是您想要的。

最后，让我们动态改变`getPositions`和`getRotation`返回的值来一点动画。

[//codepen.io/GMartigny/embed/MGqaZE/?height=500&default-tab=js,result&embed-version=2#?height=500&default-tab=result&embed-version=2](//codepen.io/GMartigny/embed/MGqaZE/?height=500&default-tab=js,result&embed-version=2#?height=500&default-tab=result&embed-version=2)

以下是发生的变化:

*   添加 CSS，使画布全页和灰色
*   每帧增加一个变量`i`，并将其传递给`getPositions`和`getRotation`
*   给`getPositions`和`getRotation`增加一些数学
*   使所有变量成为画布大小的比例

> 好吧，这很感人，但还是有点悲伤。

我完全同意你，我的读者的想象寓言，希望，我们将在第三部分看到如何处理图像。

在此期间，您可以尝试在带有`scale`和`Math.cos`的矩形上添加“摇摆”效果。

再见！