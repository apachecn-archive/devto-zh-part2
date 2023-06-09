# 提高织物速度的技巧

> 原文：<https://dev.to/ganderzz/tips-on-improving-fabricjs-speed-1p8f>

FabricJS 是一个很棒的库，可以处理[画布](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API)交互和渲染。本质上，它是一个性能很好的库，但是最近我们遇到了一个问题，我们需要渲染大约 8 兆字节的 json 数据(10k 对象)。给定任何框架，呈现这么多数据都不是一件容易的事情。渲染这么多对象时，最大的问题是渲染(或重新渲染)。当画布需要显示任何变化时，它会呈现整个画布上的*。这包括清除画布，遍历每个对象，并绘制它。在这篇文章中，我将介绍一些我们用来提高交互和渲染速度的技术。*

#### 删繁就简

FabricJS 有一个关于优化性能的[小帖子](https://github.com/fabricjs/fabric.js/wiki/Optimizing-performance)。这些都是很棒的技巧，但是要注意的是将`renderOnAddRemove`设置为 false。如果添加或删除形状，将此属性添加到画布将会阻止 Fabric 重新呈现。这极大地提高了初始加载的速度，同时为画布添加了许多形状。

```
const canvas = new fabric.Canvas("myId", { renderOnAddRemove: false }); 
```

Enter fullscreen mode Exit fullscreen mode

*注意:* `canvas.renderAll();`无论何时我们想要在画布上显示一个变化，都必须被调用

#### 搞活超负荷

当我们想将 JSON 反序列化到画布上时,`loadFromJSON`是一个非常有用的函数，但是它也有一些缺点。首先，`loadFromJSON`将在加载 JSON 对象之前清空画布。如果我们在加载 JSON 之前在画布上添加了形状，这可能会很糟糕。其次，一旦 JSON 完成，它将调用`renderAll`。当我们在 JSON 对象加载后有额外的操作要执行时(即添加更多形状或选择 then)。

或者，我们可以使用一个名为`enlivenObjects`的函数。

```
const canvas = new fabric.Canvas("myId", { renderOnAddRemove: false }););
// enlivenObjects([array of json objects], [callback])
fabric.util.enlivenObjects([{}, {}, {}], (objs) => {
    objs.forEach((item) => {
        canvas.add(item);
    });
    canvas.renderAll(); // Make sure to call once we're ready!
}); 
```

Enter fullscreen mode Exit fullscreen mode

`enlivenObjects`接受序列化 FabricJS JSON 的数组(在:[json]中找到)。对象)，并返回它的结构对象表示。我们使用回调来获取解析后的对象。这类似于`loadFromJSON`如何在幕后处理[加载](https://github.com/fabricjs/fabric.js/blob/464708aafccc0d5a4e3a7b6604db2d00ad3bc475/src/mixins/canvas_serialization.mixin.js#L55)！这里的好处是没有不必要的渲染和画布清理。我们可以控制画布何时渲染！

#### 不要什么都画

画布上的形状越多，织物在渲染时要做的工作就越多。解决这个问题的一种方法是去掉任何不需要操作或动画的形状。我们通过将这些非交互层转换为图像`canvas.toDataURL("png");`并将其呈现为 HTML `<img />`标签来实现这一点。然后，将画布覆盖在图像上；在显示所有内容时，只使用画布进行交互。