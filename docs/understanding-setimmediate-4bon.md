# 了解 setImmediate()

> 原文：<https://dev.to/flaviocopes/understanding-setimmediate-4bon>

当您想异步执行某段代码，但是要尽可能快时，一个选择是使用 Node.js 提供的`setImmediate()`函数:

```
setImmediate(() => {
  //run something
}) 
```

Enter fullscreen mode Exit fullscreen mode

任何作为 setImmediate()参数传递的函数都是在事件循环的下一次迭代中执行的回调函数。

`setImmediate()`与`setTimeout(() => {}, 0)`(经过 0 毫秒的超时)和`process.nextTick()`有什么不同？

在当前操作结束后，传递给`process.nextTick()`的函数将在事件循环的当前迭代中执行。这意味着它总是在`setTimeout`和`setImmediate`之前执行。

延迟为 0 毫秒的`setTimeout()`回调与`setImmediate()`非常相似。执行顺序将取决于各种因素，但它们都将在事件循环的下一次迭代中运行。