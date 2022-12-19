# 泡沫 VS 捕获

> 原文：<https://dev.to/clickys/bubble-vs-capture--3b19>

欢迎各位开发者，

当一个事件发生在另一个元素内的一个元素中，并且两个元素都为该事件注册了句柄时，事件冒泡和捕获是 HTML DOM API 中事件传播的两种方式。使用冒泡，事件首先被最里面的元素捕获和处理，然后传播到外面的元素。

通过捕获，事件首先被最外面的元素捕获，然后传播到内部元素。在这篇文章中，我将试着解释你可以在 eventListeners 中使用的可选参数以及它们之间的区别。我们开始吧！

target.addEventListener(type，listener[，use capture])；

target:区分大小写的字符串，表示要监听的事件类型。
type:事件的类型
listener:事件发生时会触发的函数。

useCapture(可选):一个布尔值，指示此类型的事件在被调度到 DOM 树中其下的任何 EventTarget 之前，是否将被调度到已注册的侦听器。在树中向上冒泡的事件不会触发指定使用捕获的侦听器。当两个元素都注册了事件句柄时，事件冒泡和捕获是传播嵌套在另一个元素中的元素中发生的事件的两种方式。事件传播模式决定了元素接收事件的顺序。有关详细解释，请参见 DOM Level 3 事件和 JavaScript 事件顺序。如果未指定，useCapture 默认为 false。

在这篇文章中，我将尝试解释第三个可选选项是什么。因此，我们设置了一个基本布局，3 个盒子相互嵌套。
[https://codepen.io/Clickys/embed/LBmwzw%20?height=500&default-tab=result&embed-version=2](https://codepen.io/Clickys/embed/LBmwzw%20?height=500&default-tab=result&embed-version=2)

我们将为 box-1、box-2 和 box-3 添加一个 eventListener，其中包含“click”事件和一个匿名函数中的控制台日志，当事件被触发时将调用该函数。

```
document.querySelector('.box-1').addEventListener('click', e => {
    console.log('Box-1 is clicked');
});

document.querySelector('.box-2').addEventListener('click', e => {
    console.log('Box-1 is clicked');
});

document.querySelector('.box-3').addEventListener('click', e => {
    console.log('Box-1 is clicked');
}); 
```

### 你认为当我点击方框-3 时会发生什么？我们去看看。

[![bubbling-gif](../Images/9143c48cb3aced6ce717a00e9522e7db.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jGpL5QLZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/kHzoby8.gif)

### 示例结论

当我们点击。box-3 事件被触发，并在控制台 Box-3、Box-2、Bob-1 中打印出来。因此，正如我们在图片中看到的那样，该事件是由被点击到顶部的元素触发的。这就是冒泡，首先由最内部的元素捕获和处理，然后传播到外部元素。

[![bubbling-img](../Images/20bb6a2b921dc28d879de47dc28ca3d2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0jyAWv8T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pywh75b7ewbjrr1l6l3n.png)

现在让我们来看看捕捉。要将其设置为使用捕获事件传播，您必须将 addEventListener 中的第三个(可选)参数设置为 true。

```
document.querySelector('.box-1').addEventListener('click', e => {
    console.log('Box-1 is clicked');
}, true);

document.querySelector('.box-2').addEventListener('click', e => {
    console.log('Box-1 is clicked');
}, true);

document.querySelector('.box-3').addEventListener('click', e => {
    console.log('Box-1 is clicked');
}, true); 
```

让我们再次点击框-3，看看会发生什么。

[![capturing-gif](../Images/85e8d5df04482898325eb0d35516e09f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2QaP9By0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/WWcUruq.gif)

### 示例结论捕获

从上面的例子可以看出，当我们单击 Box-3 时，它首先触发 Box-1，然后是 Box-2，最后是我们单击的元素。这就是我们所说的从上到下捕获它(到我们触发事件的元素)。

[![capture-img-example](../Images/5fce3e3e0c622151864978059f258a4d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4dmgbJ31--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/id61mkalwiedrm2l2az3.png)

# 最终结论

默认情况下，javascript 将事件传播设置为冒泡。如果我们想使用 capture，我们必须将 addEventListener 函数中的第三个参数设置为 true。

Codepen 示例:[https://codepen.io/Clickys/pen/LBmwzw?editors=1111](https://codepen.io/Clickys/pen/LBmwzw?editors=1111)