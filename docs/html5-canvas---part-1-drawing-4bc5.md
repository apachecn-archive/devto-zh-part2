# HTML5 画布-第 1 部分:绘图

> 原文：<https://dev.to/gmartigny/html5-canvas---part-1-drawing-4bc5>

# 在画布上画画

从 HTML5 开始，可以在你的页面上使用`<canvas>`标签。但是，你应该如何使用它呢？
在这第一部分中，我们将看看绘制形状的可用方法。

## 语境

首先，你需要从 HTML 元素中获取绘图上下文。在我们的例子中，我们通知浏览器我们只想用它来绘制二维图形。

```
<canvas id="scene"></canvas> 
```

```
const canvasElement = document.getElementById("scene");
const drawingContext = canvasElement.getContext("2d"); 
```

你可以通过`"webgl"`来获得一个三维渲染上下文，但是现在最好保持简单。

## 画

一旦我们有了上下文，我们就可以用它来绘制浏览器页面。
例如，让我们用`rect`指令画一个矩形:

[//codepen.io/GMartigny/embed/JvbNwq?height=500&default-tab=js,result&embed-version=2#?height=500&default-tab=result&embed-version=2](//codepen.io/GMartigny/embed/JvbNwq?height=500&default-tab=js,result&embed-version=2#?height=500&default-tab=result&embed-version=2)

> 哼，那一页空白。

良好的...这太尴尬了...

### 幻灭

使用 canvas 是小菜一碟，但是语法太繁琐了！与此同时，这种语法允许惊人的性能提升，但对于新手来说可能非常迟钝。实际上，有 3 种方法可以画出一个矩形，每种方法都有不同的清晰度。

### 1.直接的

最简单的方法就是使用`fillRect`方法。但是，这种类型的方法只适用于矩形和文本(带`fillText`)。这太有限了，但这是渲染文本的唯一方法。

[//codepen.io/GMartigny/embed/QrMgQy/?height=500&default-tab=js,result&embed-version=2#?height=500&default-tab=result&embed-version=2](//codepen.io/GMartigny/embed/QrMgQy/?height=500&default-tab=js,result&embed-version=2#?height=500&default-tab=result&embed-version=2)

### 2.电流通路

其次，可以按顺序定义一个路径，然后填充或描边它。没那么糟，但你必须自己掌握状态。

[//codepen.io/GMartigny/embed/aGyyNQ/?height=500&default-tab=js,result&embed-version=2#?height=500&default-tab=result&embed-version=2](//codepen.io/GMartigny/embed/aGyyNQ/?height=500&default-tab=js,result&embed-version=2#?height=500&default-tab=result&embed-version=2)

### 3.路径对象

最后，第三种方法是使用`Path2D`类来定义路径。最大的好处是你可以在变量中存储一个路径并在以后使用它。

[//codepen.io/GMartigny/embed/BxYpeO/?height=500&default-tab=js,result&embed-version=2#?height=500&default-tab=result&embed-version=2](//codepen.io/GMartigny/embed/BxYpeO/?height=500&default-tab=js,result&embed-version=2#?height=500&default-tab=result&embed-version=2)

最后一个是最通用最有用的。路径可以在 canvas API 中以多种方式使用:

*   isPointInPath -判断一个位置是否在路径中
*   剪辑-删除路径之外的所有内容
*   addPath -相互添加路径
*   ...

## 渲染循环

渲染 1 帧很棒，但不是很动态。了解动画最关键的技巧是渲染循环。
要创建流畅的动画，需要每秒更新视图 60 次(达到 60FPS)。一秒除以 60 大约是 16 毫秒，所以你每次抽 16 毫秒。希望有更好的方法。

你的浏览器已经以 60FPS 刷新了，你可以让它同步一个函数到这个循环中。多亏了`requestAnimationFrame`，您可以将回调绑定到下一次窗口刷新。

请记住，更新意味着清除整个画布，重新绘制。我还添加了一个检查，以防我们需要停止动画。瞧，我们的第一部动画。

[//codepen.io/GMartigny/embed/LmmGPN/?height=500&default-tab=js,result&embed-version=2#?height=500&default-tab=result&embed-version=2](//codepen.io/GMartigny/embed/LmmGPN/?height=500&default-tab=js,result&embed-version=2#?height=500&default-tab=result&embed-version=2)

> 你在开玩笑吗？这又不行了？

不，不，它非常有效！事实上，这里什么也没有发生，因为我们一遍又一遍地画同一个框架。我们需要更新画布的状态，但这是第二部分的故事。

同时，尝试使用路径上的[圆弧](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/arc)和[椭圆](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/ellipse)指令。甚至可以使用 [`lineTo`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/lineTo) 或 [`moveTo`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/moveTo) 构建自己的形状。

再见！