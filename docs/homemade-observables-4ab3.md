# 自制的可观察物

> 原文：<https://dev.to/vonheikemen/homemade-observables-4ab3>

> 你可以在这里阅读西班牙文本。

在这一集里，我们将构建我们自己的可观察的实现。我希望在这篇文章结束时，我们能对 RxJS 等库中使用的这种模式有更好的理解。

## 关于可观测量

### 什么事？

让我们从**我的**可观察的定义开始。

> 可观察对象是一个遵循约定的函数，用于连接数据源和消费者。

在我们的例子中，数据源是产生值的东西。消费者是从数据源接收值的东西。

### 有趣的事实

#### 可观者皆懒

这意味着除非绝对必要，否则他们不会做任何工作。除非您订阅它们，否则什么都不会发生。

#### 它们可以发出多个值

根据数据源的不同，您可以接收有限数量的值或无限数量的值流。

#### 它们可以是同步的也可以是异步的

这完全取决于它们的内部实现。您可以设置一个以同步方式处理一些数据流的观察对象，或者从一个随时间推移而发生的事件中创建一个观察对象。

### 有些规则

还记得我说过可观测量遵循惯例吗？嗯，我们将制定我们自己的任意规则，我们的实现将遵循这些规则。这些将是重要的，因为我们将围绕我们的可观测量建立一个小的生态系统。

我们开始吧:

1.  一个可观察的实例将有一个`subscribe`方法。
2.  可观察的“工厂”将把一个`subscriber`函数作为参数。
3.  `subscriber`函数将把一个`observer`对象作为参数。
4.  `observer`对象可以实现这些方法`next`、`error`和`complete`。

现在，让我们做些事情。

### 代码

### 工厂功能

```
function Observable(subscriber) {
  return {
    subscribe: observer => subscriber(observer)
  };
}

// I swear to you, this works. 
```

Enter fullscreen mode Exit fullscreen mode

这没有我想象的那么神奇。我们在这里看到的是**可观察的**工厂只是一种推迟必须完成的工作的方式，直到你调用 subscribe。`subscriber`函数正在做繁重的工作，这很好，因为我们可以在那里做我们想做的任何事情，这将使我们的可观测量变得有用。

到目前为止，我还没有很好地解释`observer`和`subscriber`角色。我希望当你看到他们的行动时会明白。

## 一个用例

假设我们想把一个数组转换成一个可观察的。我们如何做到这一点？

让我们想想我们所知道的:

*   我们可以在`subscriber`函数中完成所有的逻辑。
*   我们可以用三种方法期待一个观察者对象，`next`、`error`和`complete`

我们可以使用 observer 对象的方法作为通信通道。`next`函数将接收数据源给我们的值。`error`将处理我们抛出的任何错误，就像`Promise`类中的`catch`函数一样。并且，当数据源完成生成值时，我们将使用`complete`方法。

我们的可观测函数数组可以是这样的。

```
function fromArray(arr) {
  return Observable(function(observer) {
    try {
      arr.forEach(value => observer.next(value));
      observer.complete();
    } catch (e) {
      observer.error(e);
    }
  });
}

// This is how we use it

var arrayStream = fromArray([1, 2, 3, 4]);

arrayStream.subscribe({
  next: value => console.log(value),
  error: err => console.error(err),
  complete: () => console.info('Nothing more to give')
});

// And now watch all the action on the console 
```

Enter fullscreen mode Exit fullscreen mode

### 注意安全

现在 observer 对象基本上是一个没有法律的城镇，我们可以做各种奇怪的事情，比如在调用了`complete`方法之后向`next`发送另一个值。理想情况下，我们的可观测量应该给我们一些保证，比如:

*   观察者对象上的方法应该是可选的。
*   `complete`和`error`方法需要调用退订函数(如果有的话)。
*   退订的话，就不能打`next`、`complete`或者`error`了。
*   如果调用了`complete`或`error`方法，就不会发出更多的值。

### 互动示例

实际上，我们可以用目前所学的知识开始做一些有趣的事情。在这个例子中，我创建了一个 helper 函数，让我从 DOM 事件中创建一个 observable。

[https://codepen.io/VonHeikemen/embed/wxNYPV?height=600&default-tab=result&embed-version=2](https://codepen.io/VonHeikemen/embed/wxNYPV?height=600&default-tab=result&embed-version=2)

## 结论

可观察性是一种强大的东西，只要有一点点创造力，你就可以把任何你想要的东西变成可观察的。真的。一个承诺、一个 AJAX 请求、一个 DOM 事件、一个数组、一个时间间隔...另一个可观察到的现象(想一想)...你能想象到的任何东西都可以是一个数据源，可以被包装在一个可观察的对象中。

# 其他来源

*   [通过建立观察值来学习观察值](https://medium.com/@benlesh/learning-observable-by-building-observable-d5da57405d87)
*   可观察到的，只是强大的功能？

你可以在[这里](https://dev.to/vonheikemen/homemade-observables-part-2-composition-576f)看到这篇文章的第二部分。

* * *

感谢您的阅读。如果你觉得这篇文章有用，并想支持我的努力，请给我买一杯☕咖啡。

[![buy me a coffee](img/9e6f0f1293ebfd51e2e5cab78e6e6a08.png)T2】](https://www.buymeacoffee.com/vonheikemen)