# 用生成器实现异步和等待

> 原文：<https://dev.to/maciejcieslar/implementing-async-and-await-with-generators-12f8>

如今，我们可以用同步的方式编写异步代码，这要感谢 *async* 和 *await* 关键字，这使得代码更容易阅读和理解。然而，最近我想知道，如果不使用这些关键字，如何能达到同样的效果？

事实证明这非常简单，因为使用生成器可以很容易地模拟*异步*和*等待*的行为。让我们来看看！

继续，克隆[库](https://github.com/maciejcieslar/asynq)，让我们开始吧。

## 发电机

我假设你对生成器没有什么经验，因为，老实说，大多数时候它们不是特别有用，没有它们你也能轻松应付。不过，请不要担心，我们将从一个快速提醒开始:

生成器是由[生成器函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*)创建的对象，这些函数的名称旁边带有 *** (星号)。

这些生成器具有惊人的能力，让我们可以通过使用关键字 *yield* 来停止代码的执行——只要我们愿意。

考虑这个例子:

```
const generator = (function*() {
  // waiting for .next()
  const a = yield 5;
  // waiting for .next()
  console.log(a); // => 15
})();

console.log(generator.next()); // => { done: false, value: 5 }
console.log(generator.next(15)); // => { done: true, value: undefined } 
```

鉴于这些都是绝对的基础知识，我建议在你进一步滚动之前，你先阅读一下这篇文章，了解一下这里到底发生了什么。

如果你觉得你对潜在的想法有很强的理解，我们可以继续。

## 坚持住，*等待*一分钟

你有没有想过*等待*到底是怎么运作的？

不知何故，它只是等待我们的承诺，返回一个值，并继续执行。对我来说，这似乎是 generator 稍加调整后就能做的事情。

我们所能做的，就是把每一个产生的值，放入一个承诺，然后等待承诺被解决，然后通过调用*generator . next(resolved value)**把它返回给生成器。*

听起来像是一个计划，但是首先，让我们写一些测试来确保一切按预期运行。

我们的 *asynq* 函数应该做什么:

*   在继续执行之前等待异步代码
*   用函数的返回值返回一个*承诺*
*   使*尝试/捕捉*在异步代码上工作

注意:因为我们使用发电机，我们的*等待*变成*产出*。