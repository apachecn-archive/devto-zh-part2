# 退货、回调和整个动物园

> 原文：<https://dev.to/hoffmann/returns-callbacks-and-the-whole-zoo-1hbp>

我目前正在共同开发两个实体`A`和`B`之间的标准化通信。为了把我的思想从所有关于为什么和平衡不同方法的好处和缺点的想法中解放出来，我想和你们分享一下。也许你的 2 会帮助我优化我们的策略。我想补充的是，我的上下文是基于浏览器的 JavaScript，但有些想法可能是通用的。

## ➡来电

如果`A`想给`B`打电话，我有以下几种方法:

1.  在`A` : `[B.]act(param)`范围内调用预定义的函数/方法
2.  使用公共通信传输:
    1.  通过消息:`transport.postMessage({action: act, parameter: param})`在帧间和主线程/工作线程通信中使用，但也可在一个文档上下文中使用(未命中)(见附录 A)
    2.  按事件:`transport.dispatchEvent(new actEvent(param))`。

第二点可能看起来过于复杂，但是对于解耦非常有用，如果两个实体不在相同的上下文中，甚至是必要的。第二种方法的一个优点是，即使`B`暂时不可用，`A`也会继续工作。但是另一方面，`B`需要主动监听指定的事件(就像接收任何其他事件一样接收消息)。

## ⬅接听

对于`B`来说，反应和提交成功状态或从动作返回数据有更多的方法。

1.  在➡1:的情况下直接返回结果`function act(param) { …; return success }`
2.  类似于➡1:在`B` : `[A.]doneActing(success)`范围内调用预定义的函数/方法
3.  相似的➡2:使用公共交通工具例如`transport.dispatchEvent(new doneActingEvent(success)`
4.  使用包含在`param` : `param.callWhenDone(success)`中的回调
5.  兑现承诺，履行或拒绝取决于成功与否`return new Promise(function (f, r) { (success ? f : r)(successData) })`

第一种是所有非异步上下文的标准方式，第二种在某些情况下也是必要的。异步解耦是通过回调来实现的。承诺，而承诺似乎是新的“正确”的方式去做。

## 结论？

你的想法是什么，什么时候应该使用其中一个？附录 B 中显示的互换性是否导致实体`A`的一种方式和`B`的另一种方式？两个实体之间的层级关系如何，你的建议会因为`A`和`B`哪个更重要而改变吗？

# 附录

## 答:使用`postMessage`的窗口间通信

```
class B {
  constructor (targetWindow) {
    targetWindow.addEventListener('message', message => console.log(`B is reading: '${message.data}'`))
  }
}

class A {
  constructor (targetWindowOfB) {
    this.targetOfB = targetWindowOfB
  }
  letBAct (message) {
    this.targetOfB.postMessage(message, '*')
  }
}

let entityA = new A(window)
let entityB = new B(window)
entityA.letBAct('Hy, here is A, are you listening?') 
```

Enter fullscreen mode Exit fullscreen mode

> b 在读:‘Hy，这是 A，你在听吗？’

## B:转换

最后是琐碎的，*大多数*方法都是可互换的(1 作为目标被忽略)。这里可互换的`n``m`被定义为使用方法`n`的应答实体和使用方法`m`的接收实体。

1 2:

```
doneActing(act(param)) 
```

Enter fullscreen mode Exit fullscreen mode

1 3:

```
transport.dispatchEvent(new doneActingEvent(act(param))) 
```

Enter fullscreen mode Exit fullscreen mode

1 4:

```
param.callWhenDone(act(param)) 
```

Enter fullscreen mode Exit fullscreen mode

1 5:

```
var returnPromise = new Promise(function (f, r) {
  let success = act(param)
  (success ? f : r)(success)
  /* or */
  f(act(param))
}) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

2 3:

```
function doneActing (success) {
  transport.dispatchEvent(new doneActingEvent(success))
} 
```

Enter fullscreen mode Exit fullscreen mode

2 4:

```
function doneActing(success) {
    param.callWhenDone(success)
} 
```

Enter fullscreen mode Exit fullscreen mode

2 5:

```
let returnPromise = new Promise(function (f, r) {
  function doneActing(success) {
    (success ? f : r)(success)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

3 2:

```
transport.addEventListener('doneActingEvent', event => doneActing(event.data)) 
```

Enter fullscreen mode Exit fullscreen mode

3 4:

```
transport.addEventListener('doneActingEvent', event => param.callWhenDone(event.data)) 
```

Enter fullscreen mode Exit fullscreen mode

3 5:

```
let returnPromise = new Promise(function (f, r) {
  transport.addEventListener('doneActingEvent', event => (event.data ? f : r)(event.data))
}) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

4 2:

```
param.callWhenDone = doneActing 
```

Enter fullscreen mode Exit fullscreen mode

4 3:

```
param.callWhenDone = success => transport.dispatchEvent(new doneActingEvent(success)) 
```

Enter fullscreen mode Exit fullscreen mode

4 5:

```
let returnPromise = new Promise(function (f, r) {
  param.callWhenDone = success => (success ? f : r)(success)
}) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

5 2:

```
promiseResponse.finally(doneActing) 
```

Enter fullscreen mode Exit fullscreen mode

5 3:

```
promiseResponse.finally(param.callWhenDone) 
```

Enter fullscreen mode Exit fullscreen mode

5 4:

```
promiseResponse.finally(success => transport.dispatchEvent(new doneActingEvent(success)) 
```

Enter fullscreen mode Exit fullscreen mode