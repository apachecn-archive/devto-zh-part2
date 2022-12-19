# 最后是承诺&尝试/抓住

> 原文：<https://dev.to/annarankin/finally-in-promises--trycatch-2c44>

最近，我对 JavaScript 中的`async` / `await`关键字进行了更多的试验。我注意到，有时我很难将我使用的承诺策略与我需要用新语法编写代码的方式协调起来。最近，我和`finally`在一些`try` / `catch`街区玩耍，发现了一些我没有预料到的行为。

这篇文章假设对异步 JavaScript 代码的工作原理有一个大致的了解——特别是承诺的工作原理。(如果你想从对 async/await 关键字的回调中寻找对 async JS 的深入解释，在 [javascript.info](https://javascript.info/async) 上有一个很好的概述——你也可以查看[穆斯塔法·加法尔的文章](https://hackernoon.com/6-reasons-why-javascripts-async-await-blows-promises-away-tutorial-c7ec10518dd9)，了解 async/await 的一些简洁特性。)

对于上下文——在我花大量时间研究的 JavaScript 代码库中，我们一直通过大量使用承诺来处理异步操作。一般来说，这种模式对我来说要熟悉得多:

```
const loadSomething = () => {
  return fetchSomeData()
    .then(data => doSomethingWith(data))
    .catch(error => logAndReport(error))
} 
```

Enter fullscreen mode Exit fullscreen mode

还有这个不太熟悉:

```
const loadSomething = async () => {
  try {
    const data = await fetchSomeData()
    return doSomethingWith(data)
  } catch (error) {
    logAndReport(error)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

# `finally`...？

您会注意到在上面的两个例子中都缺少了一个`finally`回调/块。我在代码中不经常使用这两个词，这导致了我的误解(实际上是对两者的误解)。让我们深入了解这个概念在承诺和尝试/捕捉中的区别！

## `finally`言而无信

当你使用`somePromise.then(x).catch(y).finally(z)`模式时，你的业务逻辑通常发生在`then`回调(上面的`x`——一旦`somePromise`解决后你想做的事情)或`catch`回调(上面的【T5——万一出现可怕的错误，返回你想传递的东西)中。您可能从未在代码中使用过`finally`——这没关系。

根据 [MDN docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/finally#Description) 的说法，`finally`回调允许你在你的承诺被解决——解决或拒绝——无论如何——后执行逻辑。它对你的承诺将要解决的*值*完全没有影响——它甚至没有访问它的权限。事实上，文件指出:

> `Promise.resolve(2).finally(() => {})`将通过`2`解决。

这意味着(有点违反直觉)，你可以在你的承诺链中随意地撒上`finally`次回电，而不会改变最终结果:

```
// Please don't do this 😅

Promise.resolve({ some: 'data' })
  .finally(() => { console.log('WHALE HELLO THERE 🐋') })
  .then(data => ({ ...data, anAdditional: 'key'  }))
  .finally(() => { console.log('Looks like we made it past the first step 🙏') })
  .then(data => ({ ...data, yetAnother: 'thing added' }))
  .finally(() => { console.log("We're done I think 🙌") })
  .then(data => {
    console.log('Final result:', data)
  }) 
```

Enter fullscreen mode Exit fullscreen mode

如果您运行这段代码，您应该会看到:

[![result of executing the above code: Each step is logged to the console sequentially; the final result is an object containing three keys](../Images/2748effbee32858ed786a1f25fecf20e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zXvumFEb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7o2uc2fv1nv5wll9hbrl.png)

## `finally`在尝试/捕捉块中

try/catch/finally 模式在 JavaScript 中已经存在了很长一段时间，从 1.4 版(ES3 规范，大约在 1999 年)开始。我在这种模式和如何处理承诺之间画出了一些逻辑上的相似之处:

**`try` / `then` :**
这就是我们的“快乐之路”逻辑所去的地方——如果什么都没有发生，所有的行动都在这里发生！

**`catch` :** 🙏

**`finally` :**
该逻辑将在`try` / `then`(可能还有`catch`)逻辑完成后执行。无论我们是否遇到错误，这段代码都会运行。

这里让我犯错误的差异与`return`语句有关。如果你的`finally`块*不*包含返回语句，那么*对返回值没有影响*。然而，如果你从一个`finally`块返回一个值，这个值将覆盖所有其他的返回，并成为你的函数的最终结果。(查看文档中的[示例](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/try...catch#Returning_from_a_finally_block)！)

```
// This worked as I expected.
const returnFromTryCatch = (someFunction) => {
  try {
    return someFunction()
  } catch (error) {
    return `Caught an error: ${error}`
  } finally {
    // This block has no effect on the return value.
    console.log('All done!')
  }
}

// This was a surprise to me!
const returnFromFinally = (someFunction) => {
  try {
    return someFunction()
  } catch (error) {
    return `Caught an error: ${error}`
  } finally {
    // Wait... so I'm just swallowing my return and error handling?
    return 'All done!'
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是有道理的，但我觉得不一致。我在承诺方面的经验让我抬起了头——为什么一个`finally`块*曾经*被允许覆盖一个函数返回值？

[![pixel art of a woman looking at her laptop in deep thought](../Images/9fd2b827072bd31a668f5cb3df3b551c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---s2UJNZP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h26k6giyyy38p4v6y8w9.png)

# 寻找原因

[*最后，*](https://en.wikipedia.org/wiki/Semantic_satiation) 我向我的技术主管报告了我的烦恼，他发给我[一个相关 StackOverflow 讨论的链接](https://stackoverflow.com/questions/3837994/why-does-a-return-in-finally-override-try)。看到 ECMAScript 对此行为的规范(重点是我的),帮助它在我的大脑中就位:

> 产量`TryStatement : try Block Finally`评估如下:
> 
> 设 B 是评估 Block 的结果。
> 设 F 为最终求值结果。
> **如果 F.type 正常，返回 B.**
> 返回 f

(值得注意的是，根据 [ECMAScript 规范](https://tc39.github.io/ecma262/#sec-completion-record-specification-type)，完成类型是“正常、中断、继续、返回或抛出中的一种”——我假设不包含`break`、`continue`、`return`或`throw`关键字的函数符合“正常”的条件有点奇怪的语义。)

### 多张退货单上的备注

本文中的代码示例没有使用单个回车。我不打算过多地讨论多个 return 语句——我要说的是，一般来说，对较长的函数使用单个 return 过去对我很有用，但我发现它们在较短的块中用处不大。不过，在这种情况下，这可能会让我的生活更轻松！