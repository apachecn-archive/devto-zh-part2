# 我创造了一个怪物

> 原文：<https://dev.to/stereobooster/i-created-a-monster-237o>

这是系列文章的第四篇。这个帖子的代码是[这里是](https://github.com/stereobooster/react-fsm-example/tree/post-4)

1.  [还原为有限状态机](https://dev.to/stereobooster/pragmatic-types-how-to-turn-redux-to-finite-state-machine-with-the-help-of-types-5f08)
2.  [Redux 中的副作用](https://dev.to/stereobooster/turn-redux-into-finite-state-machine-vol-2-side-effects-6j0)
3.  [乐观界面](https://dev.to/stereobooster/optimistic-ui-5f1f)
4.  [我创造了一个怪物](https://dev.to/stereobooster/i-created-a-monster-237o)

## 新要求

在每篇文章中，我都会添加新的技术方法或新的 UX 需求，然后解决它。这一次，我想，在请求挂起时取消阻止搜索按钮会很好，这样用户就可以提交另一个搜索请求，而无需等待当前搜索请求完成。

提醒:在第一篇文章中，我们介绍了 FSM，但是为了简化，我认为我们不想考虑竞争条件，而是一次只允许一个搜索请求。否则，可能会出现这样的情况:用户提交了两个请求，但是第一个请求的响应比第二个请求晚到达:

```
 +---------------------------------------+
 |first request                          |
 +---------------------------------------+
        +----------------------+
        |second request        |
        +----------------------+ 
```

从用户的角度来看，流程将是:提交第一个搜索，用户看到加载状态，提交第二个请求，用户看到加载状态，接下来(两个可能的选项):

*   下一个用户看到第二个请求的结果(无负载状态)，然后突然看到第一个请求的结果
*   下一个用户会看到第一个请求的结果

这两个选项都不好，为了避免这种情况，我们从 FSM 的角度出发，通过将请求数量限制为一个来简化任务:

```
switch (action.type) {
  case "SUBMIT_FRUIT":
    switch (reduxState.state) {
      case "fruit_loading":
        return loop(reduxState, Cmd.none); 
```

而从 UI 的角度来看:

```
<button
  type="submit"
  disabled={this.props.stateState === "fruit_loading"}
>Search</button> 
```

现在我想去掉这个限制。听起来很简单，对吧？

## 第一次尝试:取消之前的请求

因此，我们不允许一次提交多个请求，如果我们想提交另一个请求，我们可以简单地取消上一个请求。看似简单明了，但由于之前的决定[代码变得不清楚和混乱](https://github.com/stereobooster/react-fsm-example/pull/1/files)。

首先，我认为我创造了一个怪物，这样写代码是不好的。此外，我怀疑这个需求是否会首先出现在现实生活中，也许我把需求想得太复杂了。我想，这种任务，用反应式库，比如 RxJS 或 cycle.js 来解决可能是微不足道的。但后来我想:等等，也许有另一种方法？

## 第二次尝试:检查结果是否与当前请求相同

完成这项任务的另一种方法是允许多个请求同时进行，但是当应答返回(成功或失败)时，检查应答是否与最近提交的请求具有相同的参数。

```
case "SUBMIT_FRUIT_OK":
  if (!deepEqual(reduxState.form, action.form))
    return loop(reduxState, Cmd.none); 
```

如你所见[第二次尝试](https://github.com/stereobooster/react-fsm-example/pull/2/files)简单多了。

## 又一期

我们在第三篇文章中介绍了缓存。后来我意识到，有一个错误。出错的获取请求应该立即从缓存中清除，否则在临时错误的情况下，用户可能会在一段时间内陷入错误状态。

```
result = baseFruitRequest(form).catch(e => {
  cache.delete(query);
  return Promise.reject(e);
}); 
```

## 值得吗？

所有这些意外的复杂性和一些 bug 潜入的事实让我对这种方法产生了疑问。

这种方法只解决了一些问题，一旦我们脱离了 FSM 和同步操作的“区域”,我们仍然必须处理一些不明显的复杂性(如缓存问题)。

JS/Flow/TypeScript 的语法和使用的 libs 不够有表现力，我们仍然需要大量的样板文件，我想知道这些代码在 Elm 中是否会更短。

但另一方面，我认为我已经厌倦了用一种容易出错的老方法编写软件，我想找到一种更好的方法来做这件事。而且如果这种方法不是最终的解决方案，它至少让我更接近我的目标。

也许我会尝试探索[代数效应](https://dev.to/yelouafi/algebraic-effects-in-javascript-part-1---continuations-and-control-transfer-3g88)接下来，也许我会找到一种方法为它做类型检查。

> freestocks.org 在 Unsplash 上拍摄的照片

* * *

在 [twitter](https://twitter.com/stereobooster) 和 [github](https://github.com/stereobooster) 关注我。