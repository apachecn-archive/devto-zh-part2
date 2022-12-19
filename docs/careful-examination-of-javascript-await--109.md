# 等待着对 JavaScript 仔细检查

> 原文：<https://dev.to/nestedsoftware/careful-examination-of-javascript-await--109>

最近，我发现自己在用 [async](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) / [await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await) 编写一些 JavaScript 代码时有点困惑。我研究了一些细节，当我们`await`的时候发生了什么，我想发表一篇关于它的文章可能会有帮助(对我未来的自己和其他读者都有帮助！).

> TL；dr: `await`与`yield`相似，但也有一些不同。

下面的代码有望阐明 JavaScript 中的`async/await`发生了什么。你能想出它会做什么吗？

```
const asyncTask = () => {
    console.log('asyncTask started')

    const promise = new Promise(resolve => {
        setTimeout(() => { 
            console.log('asyncTask resolving promise')
            resolve('1000')
        }, 2000)
    })

    console.log('asyncTask returning promise')

    return promise
}

const asyncFunction = async () => {
    console.log('asyncFunction started')

    const promise = asyncTask()

    const awaitResult = await promise

    console.log('returning from asyncFunction, awaitResult = "' 
        + awaitResult + '"')

    return 'I am returning with "' + awaitResult + '"'
}

const timer = () => setInterval(()=>console.log('tick'), 500)

//start of main

const t = timer()

const mainPromise = asyncFunction()

console.log('mainPromise = ' + mainPromise)

mainPromise.then((result) => {
    console.log('mainPromise has resolved, result = ' + result)

    //stop timer
    clearInterval(t)
})

console.log('end of main code') 
```

Enter fullscreen mode Exit fullscreen mode

下面是输出:

```
C:\dev>node promises.js
asyncFunction started
asyncTask started
asyncTask returning promise
mainPromise =  [object Promise]
end of main code
tick
tick
tick
asyncTask resolving promise
returning from asyncFunction, awaitResult = "1000"
mainPromise has resolved, result = I am returning with "1000" 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript 在幕后用`await`做了一些棘手的事情，所以我认为仔细阅读这段代码可能会有所帮助，以便了解每一步发生了什么:

*   在主代码中，我们启动了一个定时器。
*   接下来，我们调用`asyncFunction`。
*   在`asyncFunction`中，我们称之为`asyncTask`。
*   `asyncTask`创造承诺。
*   承诺引发一场`setTimeout`。
*   `asyncTask`将承诺返回给`asyncFunction`。
*   在`asyncFunction`中，我们现在`await`从`asyncTask`返回的承诺。
*   *这个部分很重要* : `await`非常类似于[发生器功能](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function%2A)中的`yield`。这里发生的是`asyncFunction`被暂时挂起，并提前“返回”到“主”代码。如果`asyncFunction`是一个生成器函数，那么我们可以通过调用它的`next`方法在自己的代码中恢复它。然而，我们将会看到，在这种情况下并不完全是这样。
*   `asyncFunction`暂停时产生了什么？结果是 JavaScript 运行时在这一点上创建了一个新的承诺，这就是分配给变量`mainPromise`的内容。重要的是要意识到这个承诺不同于`asyncTask`的回报。
*   现在，其余的“主”代码运行，我们看到“主代码结束”打印到控制台。然而，JavaScript 运行时并没有退出，因为它还有工作要做！毕竟，我们还有一个`setTimeout`未决(以及我们的计时器的`setInterval`)。
*   一旦两秒钟过去(我们可以通过计时器的“滴答”看到这种情况)，就会调用`setTimeout`的回调函数。
*   这个回调函数反过来解析`asyncFunction`当前正在等待的承诺。
*   当承诺被解决时，JavaScript 运行时从被`await`暂停的地方恢复`asyncFunction`。这非常类似于在一个生成器函数上调用`next`，但是这里是运行时为我们做的。
*   由于没有更多的`await`语句，`asyncFunction`现在运行完成，并实际上正确返回。
*   asyncFunction 返回时会发生什么？毕竟，它之前已经被挂起了，在那时，它产生了一个赋值给`mainPromise`变量的承诺。
*   发生的情况是，JavaScript 引擎截取返回，并使用 return 语句中的任何值来实现它之前创建的承诺。
    *   我们可以看到这种情况发生，因为现在提供给`mainPromise.then`的回调实际上被执行了。
    *   我们从`asyncFunction`返回了一个字符串，其中包含了来自 asyncTask 的已解析承诺的值:因此这就是作为`result`传递给`mainPromise.then((result) => { console.log('mainPromise has resolved, result = ' + result) })`中的回调的字符串

因为这些东西很容易让人混淆，所以让我们总结一下:

*   `async`函数中的`await`与生成器函数中的`yield`非常相似:在两种情况下，函数都被挂起，执行返回到调用它的地方。
*   然而，`await`在以下方面有所不同:
    *   JavaScript 运行时将创建一个新的承诺，这就是函数暂停时产生的结果。
    *   当被`await` ed 的承诺实现时，JavaScript 运行时将自动恢复`async`功能
    *   当`async`函数正常返回时，JavaScript 运行时将使用函数的返回值来履行运行时之前创建的承诺。

参考资料:

[Aync 函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)
[等待](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await)
[生成器函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function%2A)
[迭代器和生成器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators)

相关:

*   [JavaScript 中带有生成器、映射、过滤和归约的惰性求值](https://dev.to/nestedsoftware/lazy-evaluation-in-javascript-with-generators-map-filter-and-reduce--36h5)
*   [如何在 JavaScript 中序列化并发操作:回调、承诺和异步/等待](https://dev.to/nestedsoftware/how-to-serialize-concurrent-operations-in-javascript-callbacks-promises-and-asyncawait-3ge3)
*   迭代器来了！JavaScript 中的【Symbol.iterator】和【Symbol.asyncIterator】
*   [JavaScript 中的异步生成器和管道](https://dev.to/nestedsoftware/asynchronous-generators-and-pipelines-in-javascript--1h62)