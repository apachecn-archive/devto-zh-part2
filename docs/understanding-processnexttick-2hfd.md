# 了解 process.nextTick()

> 原文：<https://dev.to/flaviocopes/understanding-processnexttick-2hfd>

当你试图理解 [Node.js 事件循环](https://flaviocopes.com/node-event-loop/)时，它的一个重要部分是`process.nextTick()`。

每次事件循环走完整个行程，我们称之为滴答。

当我们将一个函数传递给`process.nextTick()`时，我们指示引擎在当前操作结束时，在下一个事件循环周期开始之前调用这个函数:

```
process.nextTick(() => {
  //do something
}) 
```

Enter fullscreen mode Exit fullscreen mode

事件循环正忙于处理当前的函数代码。

当该操作结束时，JS 引擎运行该操作期间传递给`nextTick`调用的所有函数。

这是我们可以告诉 JS 引擎异步处理一个函数(在当前函数之后)的方式，但是要尽快，不要排队。

调用`setTimeout(() => {}, 0)`将在下一个节拍执行函数，比使用`nextTick()`要晚得多。

当您想要确保在下一次事件循环迭代中代码已经被执行时，使用`nextTick()`。