# 如何在 JavaScript 中序列化并发操作:回调、承诺和异步/等待

> 原文：<https://dev.to/nestedsoftware/how-to-serialize-concurrent-operations-in-javascript-callbacks-promises-and-asyncawait-3ge3>

## 概述

这篇文章是关于如何在 JavaScript 中指定并发操作的顺序。

通常我们并不关心并发操作的完成顺序。例如，假设我们有一个 Web 服务器正在处理来自客户端的请求。每个请求花费的时间可能会有所不同，响应被发回的顺序并不重要。

然而，我们确实关心排序的情况并不少见。有时，当我们执行一个异步操作时，我们需要在执行下一个操作之前让它运行完成。这就是这篇文章的内容。

在现代 JavaScript 中，基本上有 3 种方法可以做到这一点。

*   最古老的方法是只使用回调。这种方法可能在概念上是最纯粹的，但它也可能导致所谓的[回调地狱](http://callbackhell.com/):一种难以理解和调试的意大利面条代码。
*   另一种方法是使用[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)，它允许以更程序化的方式指定操作的顺序。
*   最近，JavaScript 引入了 [`async`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) 和 [`await`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await) 。

> 这些方法不是相互排斥的，而是互补的:`async/await`建立在承诺之上，而承诺使用回调。

我将展示一个以这三种方式实现的简单例子，首先是回调，然后是承诺，最后是 async/await。

对于这个例子，我们有一个假设的应用程序，它可以同时自动地将一些定制软件部署到多台计算机上。假设每个部署有 3 个步骤:

*   安装操作系统
*   部署我们的软件
*   运行测试

对于任何给定的目标，这 3 个操作需要按顺序运行，但是它们可以跨目标并发执行(感谢 [edA-qa](https://dev.to/mortoray) 提出这个实际例子！).

## 并发执行

首先让我们看一些代码，这些代码同时运行这些任务，根本没有序列化它们(unserialized.js):

```
const random = (min, max) => Math.floor(Math.random() * (max - min + 1)) + min

const installOS = () => asyncTask("Install OS")

const deploySoftware = () => asyncTask("Deploy Software")

const runTests = () => asyncTask("Run Tests")

const taskDone = (name) => console.log(`Completed async "${name}"`)

const asyncTask = (name) =>  {
    console.log(`Started async "${name}"...`)
    setTimeout(() => taskDone(name), random(1,3) * 1000)
    console.log(`Returning from async "${name}"`)
}

const main = ()=> {
    installOS()
    deploySoftware()
    runTests()
}

main() 
```

Enter fullscreen mode Exit fullscreen mode

我们通过调用`asyncTask`来模拟我们的操作，它使用`setTimeout`来等待 1 到 3 秒，然后完成它的任务并调用`taskDone`。

下面是一个典型的输出(实际的顺序会在每次代码运行时改变):

```
C:\dev\asyncio>node unserialized.js
Started async "Install OS"...
Returning from async "Install OS"
Started async "Deploy Software"...
Returning from async "Deploy Software"
Started async "Run Tests"...
Returning from async "Run Tests"
Completed async "Deploy Software"
Completed async "Install OS"
Completed async "Run Tests" 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，这并不太好:我们在操作系统安装完成之前就部署了我们的软件。

## 使用回调

好了，让我们用回调来解决这个问题(callbacks.js):

```
const random = (min, max) => Math.floor(Math.random() * (max - min + 1)) + min

const installOS = (nextTask) => asyncTask("Install OS", nextTask)

const deploySoftware = (nextTask) => asyncTask("Deploy Software", nextTask)

const runTests = () => asyncTask("Run Tests")

const taskDone = (name, nextTask) => {
    console.log(`Completed async "${name}"`)
    if (nextTask) {
        nextTask()
    }
}

const asyncTask = (name, nextTask) =>  {
    console.log(`Started async "${name}"...`)
    setTimeout(() => taskDone(name, nextTask), 
        random(1,3) * 1000)
    console.log(`Returning from async "${name}"`)
}

const main = ()=> {
    installOS(()=>deploySoftware(()=>runTests()))
}

main() 
```

Enter fullscreen mode Exit fullscreen mode

我们用回调函数调用`installOS`，一旦`installOS`完成，回调函数将运行`deploySoftware`。一旦`deploySoftware`完成，它将调用自己的回调函数`runTests`。

每次操作完成后，`taskDone`功能会将该操作记录为完成，并开始下一个操作。

来看看有没有效果:

```
C:\dev\asyncio>node callbacks.js
Started async "Install OS"...
Returning from async "Install OS"
Completed async "Install OS"
Started async "Deploy Software"...
Returning from async "Deploy Software"
Completed async "Deploy Software"
Started async "Run Tests"...
Returning from async "Run Tests"
Completed async "Run Tests" 
```

Enter fullscreen mode Exit fullscreen mode

很好，我们可以看到每一步都是按顺序进行的。

然而，该代码仍然存在许多问题。即使有这样一个简单的例子，我认为代码还是有点难读。

错误处理也可能不像它应该的那样简单。例如，让我们修改`deploySoftware`函数来抛出一个错误:

```
const deploySoftware = (nextTask) => {
    throw new Error('deploying software failed')
    asyncTask("Deploy Software", 
    nextTask)
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们尝试用一个异常处理程序:
天真地包装我们的主调用

```
const main = ()=> {
    try {
        installOS(()=>deploySoftware(()=>runTests()))
    } catch (error) {
        console.log(`*** Error caught: '${error}' ***`)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，`catch`块从未被执行，异常最终弹出堆栈:

```
C:\dev\asyncio\callbacks.js:7
        throw new Error('deploying software failed')
        ^

Error: deploying software failed
    at deploySoftware (C:\dev\asyncio\callbacks.js:7:8)
    at installOS (C:\dev\asyncio\callbacks.js:30:17)
    at taskDone (C:\dev\asyncio\callbacks.js:17:3)
    at Timeout.setTimeout [as _onTimeout] (C:\dev\asyncio\callbacks.js:23:19)
    at ontimeout (timers.js:458:11)
    at tryOnTimeout (timers.js:296:5)
    at Timer.listOnTimeout (timers.js:259:5) 
```

Enter fullscreen mode Exit fullscreen mode

问题是在错误发生时,`installOS`已经返回。很明显，在处理错误方面还需要一些额外的努力。我将把它留给读者作为练习。正如我们将看到的，承诺将使错误处理更容易。

## 利用承诺

让我们稍微修改一下代码，使用 promises (promises.js):

```
const random = (min, max) => Math.floor(Math.random() * (max - min + 1)) + min

const installOS = () => asyncTask("Install OS")

const deploySoftware = () => asyncTask("Deploy Software")

const runTests = () => asyncTask("Run Tests")

const taskDone = (name) => console.log(`Completed async "${name}"`)

const asyncTask = (name) =>  {
    console.log(`Started async "${name}"...`)

    const promise = new Promise((resolve, reject) => {
        setTimeout(()=>resolve(name), random(1,3) * 1000)
    })

    console.log(`Returning from async "${name}"`)

    return promise
}

const main = ()=> {
    installOS().then(name=>{
        taskDone(name)
        return deploySoftware()
    }).then(name=>{
        taskDone(name)
        return runTests()
    }).then(taskDone)
}

main() 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到，我们已经能够从任务中移除`nextTask`回调。现在每个任务都可以独立运行了。将它们连接在一起的工作已经转移到了`main`中。

为了实现这一点，我们修改了`asyncTask`来返回一个承诺。

这是如何工作的？当异步操作的结果准备好时，我们调用 promise 的`resolve`回调。Promises 有一个方法`then`,可以通过回调作为参数提供。当我们触发`resolve`函数时，它将运行我们提供给 promise 的`then`方法的回调。

这允许我们序列化我们的异步操作。当`installOS`完成时，我们向调用`deploySoftware`的`then`提供一个回调。`deploySoftware`函数返回另一个承诺，通过调用`runTests`来解决。当`runTests`完成时，我们只提供一个简单的回调函数，将任务记录为完成。

通过从我们的任务返回 promise 对象，我们可以将我们想要一个接一个完成的任务链接在一起。

> 如何实现承诺的细节超出了本文的范围。如果你对这个话题感兴趣，我发现了一篇关于 [Q promise 库](https://github.com/kriskowal/q)设计的有趣的[文章](https://github.com/kriskowal/q/blob/master/design/README.md)。

我认为这段代码比回调示例更容易阅读。

这也使得处理错误变得更加容易。让我们再次修改`deploySoftware`来抛出一个错误:

```
const deploySoftware = () => {
    throw new Error('"Deploy Software" failed')
    return asyncTask("Deploy Software")
} 
```

Enter fullscreen mode Exit fullscreen mode

承诺有一个方便的方法来处理这个问题。我们只需在承诺链的末尾添加一个`catch`方法:

```
const main = ()=> {
    installOS().then(name=>{
        taskDone(name)
        return deploySoftware()
    }).then(name=>{
        taskDone(name)
        return runTests()
    }).then(taskDone)
    .catch((error)=>console.log(`*** Error caught: '${error}' ***`))
} 
```

Enter fullscreen mode Exit fullscreen mode

如果在试图解决一个承诺时出现错误，这个`catch`方法被调用。

让我们看看运行这段代码会发生什么:

```
C:\dev\asyncio>node serialize_with_promises.js
Started async "Install OS"...
Returning from async "Install OS"
Completed async "Install OS"
*** Error caught: 'Error: "Deploy Software" failed' *** 
```

Enter fullscreen mode Exit fullscreen mode

太好了，我们发现了我们的错误！我认为这看起来比纯粹的回调例子简单多了。

## 使用异步/等待

Aync/Await 是我们要看的最后一个例子。这种语法与使序列化异步操作看起来像常规同步代码的承诺一起工作。

好了，不用再等了——让我们修改前面的例子，使用 async/await (async_await.js)！

```
const random = (min, max) => Math.floor(Math.random() * (max - min + 1)) + min

const installOS = () => asyncTask("Install OS") 

const deploySoftware = () => asyncTask("Deploy Software") 

const runTests = () => asyncTask("Run Tests") 

const taskDone = (name) => console.log(`Completed async "${name}"`)

const asyncTask = (name) =>  {
    console.log(`Started async "${name}"...`)

    const promise = new Promise((resolve, reject) => {
        setTimeout(()=>resolve(name), random(1,3) * 1000)
    })

    console.log(`Returning from async "${name}"`)

    return promise
}

const main = async ()=> {
    const installOSResult = await installOS()
    taskDone(installOSResult)

    const deploySoftwareResult = await deploySoftware()
    taskDone(deploySoftwareResult)

    const runTestsResult = await runTests()
    taskDone(runTestsResult)
}

main() 
```

Enter fullscreen mode Exit fullscreen mode

我们做了哪些改变？首先，我们将`main`标记为一个`async`函数。接下来，我们`await`异步操作的结果，而不是承诺链。

`await`会自动等待函数返回的承诺自行解析。像我们今天看到的所有代码一样，它是非阻塞的，所以在等待一个表达式时，其他事情可以并发运行。然而，跟随在`await`之后的下一行代码不会运行，直到承诺被解决。任何包含`await`的函数都必须标记为`async`。

让我们运行这段代码，看看结果:

```
C:\dev\asyncio>async_await.js
Started async "Install OS"...
Returning from async "Install OS"
Completed async "Install OS"
Started async "Deploy Software"...
Returning from async "Deploy Software"
Completed async "Deploy Software"
Started async "Run Tests"...
Returning from async "Run Tests"
Completed async "Run Tests" 
```

Enter fullscreen mode Exit fullscreen mode

太好了，成功了！

我们可以再做一点小小的改变，让`deploySoftware`抛出一个错误:

```
const deploySoftware = () => {
    throw new Error('"Deploy Software" failed')
    return asyncTask("Deploy Software")
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看如何处理这个问题:

```
const main = async ()=> {
    try {
        const installOSResult = await installOS()
        taskDone(installOSResult)

        const deploySoftwareResult = await deploySoftware()
        taskDone(deploySoftwareResult)

        const runTestsResult = await runTests()
        taskDone(runTestsResult)
    } catch(error) {
        console.log(`*** Error caught: '${error}' ***`)     
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

此作品:

```
C:\dev\asyncio>node async_await.js
Started async "Install OS"...
Returning from async "Install OS"
Completed async "Install OS"
*** Error caught: 'Error: "Deploy Software" failed' *** 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，async/await 使得使用标准的同步语法来处理由我们的异步代码产生的任何错误成为可能！

在结束本文之前，我想我应该添加一些代码来说明`await`确实是非阻塞的。让我们添加一个计时器，它将与我们的其他代码并发运行:

```
const timer = () => setInterval(()=>console.log('tick'), 500)

const main = async ()=> {
    const t = timer()

    const installOSResult = await installOS()
    taskDone(installOSResult)

    const deploySoftwareResult = await deploySoftware()
    taskDone(deploySoftwareResult)

    const runTestsResult = await runTests()
    taskDone(runTestsResult)

    clearInterval(t)
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是结果:

```
C:\dev\asyncio>node async_await.js
Started async "Install OS"...
Returning from async "Install OS"
tick
Completed async "Install OS"
Started async "Deploy Software"...
Returning from async "Deploy Software"
tick
tick
tick
tick
tick
tick
Completed async "Deploy Software"
Started async "Run Tests"...
Returning from async "Run Tests"
tick
tick
Completed async "Run Tests" 
```

Enter fullscreen mode Exit fullscreen mode

我们可以确认计时器在我们执行任务时继续运行。太好了！

在使用`await`时，我认为记住这一点是有帮助的，它大致相当于从异步调用中获得一个承诺并调用它的`then`方法。

> 一些问题:你必须在标有`async`的函数中使用`await`。这意味着你不能在 JavaScript 代码的顶层`await`某些东西。当编写顶级代码时，您可以使用 promises 的`then`语法，或者将代码封装在一个标记为 async 的自执行函数中。
> 
> 同样，如果你需要同时运行一堆任务，并且你只想在所有的*任务都完成后执行一些代码，参见 [Promise.all](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)*

相关:

*   [JavaScript 中带有生成器、映射、过滤和归约的惰性求值](https://dev.to/nestedsoftware/lazy-evaluation-in-javascript-with-generators-map-filter-and-reduce--36h5)
*   [等待 JavaScript 的仔细检查](https://dev.to/nestedsoftware/careful-examination-of-javascript-await--109)
*   迭代器来了！JavaScript 中的【Symbol.iterator】和【Symbol.asyncIterator】
*   [JavaScript 中的异步生成器和管道](https://dev.to/nestedsoftware/asynchronous-generators-and-pipelines-in-javascript--1h62)