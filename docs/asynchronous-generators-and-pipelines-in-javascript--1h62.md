# JavaScript 中的异步生成器和管道

> 原文：<https://dev.to/nestedsoftware/asynchronous-generators-and-pipelines-in-javascript--1h62>

## 介绍异步发电机

这篇文章和上一篇文章，[迭代器来了](https://dev.to/nestedsoftware/the-iterators-are-coming-symboliterator-and-symbolasynciterator-in-javascript-hj)，讨论异步迭代器，都是由我在用一些`async`函数编程时想到的一个问题所激发的:*是否有可能在`async`函数中使用`yield`？*换句话说，我们能不能把一个`async`函数和一个生成器函数结合起来？

为了探究这个问题，让我们从一个普通的*同步*发电机函数开始，`numberGenerator` :

```
const random = (min, max) => Math.floor(Math.random() * (max - min + 1)) + min

const getValue = () => {
    return random(1,10)
}

const numberGenerator = function* () {
    for (let i=0; i<5; i++) {
        const value = getValue() 
        yield value**2
    }
}

const main = () => {
    const numbers = numberGenerator()
    for (const v of numbers) {
        console.log('number = ' + v)
    }
}

main() 
```

Enter fullscreen mode Exit fullscreen mode

这段代码产生 5 个随机数的预期平方:

```
C:\dev>node gen.js
number = 1
number = 64
number = 36
number = 25
number = 49 
```

Enter fullscreen mode Exit fullscreen mode

我的想法是将`getValue`改为返回一个承诺，并将`numberGenerator`修改为`await`这个承诺，然后`yield`为一个值。我尝试了如下:

```
const random = (min, max) => Math.floor(Math.random() * (max - min + 1)) + min

const getValue = () => {
    //return promise instead of value
    return new Promise(resolve=>{
        setTimeout(()=>resolve(random(1,10)), 1000)
    })
}

const numberGenerator = function* () {
    for (let i=0; i<5; i++) {
        const value = await getValue() //await promise
        yield value**2
    }
}

const main = () => {
    const numbers = numberGenerator()
    for (const v of numbers) {
        console.log('number = ' + v)
    }
}

main() 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看会发生什么:

```
C:\dev\gen.js:12
                const value = await getValue() //await promise
                              ^^^^^

SyntaxError: await is only valid in async function
    at new Script (vm.js:51:7) 
```

Enter fullscreen mode Exit fullscreen mode

好的，这是有道理的:我们需要让我们的`numberGenerator`函数`async`。让我们试试那个！

```
const numberGenerator = async function* () { //added async 
```

Enter fullscreen mode Exit fullscreen mode

有用吗？

```
C:\dev\gen.js:10
const numberGenerator = async function* () { //added async
                                      ^

SyntaxError: Unexpected token *
    at new Script (vm.js:51:7) 
```

Enter fullscreen mode Exit fullscreen mode

哎哟，没用。这就是我在网上搜索这个话题的原因。事实证明，这种功能将在 ES2018 中发布[，我们已经可以在带有`--harmony-async-iteration`标志的 node 的最新版本中使用它。](http://2ality.com/2016/10/asynchronous-iteration.html)

让我们来看看这是怎么回事:

```
const timer = () => setInterval(()=>console.log('tick'), 1000)

const random = (min, max) => Math.floor(Math.random() * (max - min + 1)) + min

const getValue = () => {
    //return promise instead of value
    return new Promise(resolve=>{
        setTimeout(()=>resolve(random(1,10)), 1000)
    })
}

const numberGenerator = async function* () { //added async
    for (let i=0; i<5; i++) {
        const value = await getValue() //await promise
        yield value**2
    }
}

//main is 'async'
const main = async () => {
    const t = timer()
    const numbers = numberGenerator()

    //use 'for await...of' instead of 'for...of'
    for await (const v of numbers) {
        console.log('number = ' + v)
    }

    clearInterval(t)
}

main() 
```

Enter fullscreen mode Exit fullscreen mode

与以前版本的代码相比，有一些小的变化:

*   `main`函数的`for...of`循环变成了`for await...of`循环。
*   由于我们使用了`await`，`main`必须标记为`async`

> 还添加了一个计时器，因此我们可以确认生成器确实是异步的。

我们来看看结果:

```
C:\dev>node --harmony-async-iteration gen.js
tick
number = 16
tick
number = 1
tick
number = 100
tick
number = 100
tick
number = 49 
```

Enter fullscreen mode Exit fullscreen mode

成功了！

> `async`发电机功能中的`yield`类似于普通(同步)发电机功能中的`yield`。不同之处在于，在常规版本中，`yield`产生一个`{value, done}`元组，而异步版本产生一个承诺，即*将*解析为`{value, done}`元组。
> 
> 如果您`yield`了一个承诺，JavaScript 运行时会做一些偷偷摸摸的事情:它仍然产生自己的承诺，该承诺解析为一个`{value, done}`元组，但是该元组中的`value`属性将是您的承诺解析为的任何内容。

## 将异步发电机流水线连接在一起

让我们来看看这项技术的一个简单应用:我们将创建一个异步生成器函数，它驱动另一个函数来生成异步数字流的统计数据。

这种管道可用于对异步数据流执行任意转换。

首先，我们将编写一个异步生成器，它会产生无穷无尽的值。每秒钟它会生成一个 0 到 100 之间的随机值:

```
const random = (min, max) => Math.floor(Math.random() * (max - min + 1)) + min

const asyncNumberGenerator = async function* () {
    while (true) {
        const randomValue = random(0,100)

        const p = new Promise(resolve=>{
            setTimeout(()=>resolve(randomValue), 1000)
        })      

        yield p
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们要写一个函数，`createStatsReducer`。该函数返回一个回调函数`exponentialStatsReducer`，它将用于迭代计算这个数据流
的[指数移动平均值](https://dev.to/nestedsoftware/exponential-moving-average-on-streaming-data-4hhl)

```
const createStatsReducer = alpha => { 
    const beta = 1 - alpha

    const exponentialStatsReducer = (newValue, accumulator) => {
        const redistributedMean = beta * accumulator.mean

        const meanIncrement = alpha * newValue

        const newMean = redistributedMean + meanIncrement

        const varianceIncrement = alpha * (newValue - accumulator.mean)**2

        const newVariance = beta * (accumulator.variance + varianceIncrement)

        return {
            lastValue: newValue,
            mean: newMean,
            variance: newVariance
        }
    }

    return exponentialStatsReducer
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们有第二个异步生成器函数，`asyncReduce`。这个例子将一个缩减器应用于异步可迭代对象。它的工作原理就像 JavaScript 内置的 [`Array.prototype.reduce`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) 。然而，标准版本遍历整个数组来产生最终值，而我们的版本缓慢地应用缩减。这允许我们使用一个无限值序列(我们上面的异步数字生成器)作为数据源:

```
const asyncReduce = async function* (iterable, reducer, accumulator) {
    for await (const item of iterable) {
        const reductionResult = reducer(item, accumulator)

        accumulator = reductionResult

        yield reductionResult
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们把这些联系起来。下面的代码将把一个无止境的异步生成的数字序列输送到我们的异步 reduce 中。我们将遍历结果值(永远)，在新值到达时获得更新的平均值、方差和标准偏差:

```
const timer = () => setInterval(()=>console.log('tick'), 1000)

const main = async () => {
    const t = timer()

    const numbers = asyncNumberGenerator()

    const firstValue = await numbers.next()

    //initialize the mean to the first value
    const initialValue = { mean: firstValue.value, variance: 0 }

    console.log('first value = ' + firstValue.value)

    const statsReducer = createStatsReducer(0.1)

    const reducedValues = asyncReduce(numbers, statsReducer, initialValue)

    for await (const v of reducedValues) {
        const lastValue = v.lastValue
        const mean = v.mean.toFixed(2)
        const variance = v.variance.toFixed(2)
        const stdev = Math.sqrt(v.variance).toFixed(2)

        console.log(`last value = ${lastValue}, stats = { mean: ${mean}`
            + `, variance: ${variance}, stdev: ${stdev} }`)
    }

    clearInterval(t)
}

main() 
```

Enter fullscreen mode Exit fullscreen mode

让我们来看看一些示例输出:

```
C:\dev>node --harmony-async-iteration async_stats.js
tick
first value = 51
tick
last value = 97, stats = { mean: 55.60, variance: 190.44, stdev: 13.80 }
tick
last value = 73, stats = { mean: 57.34, variance: 198.64, stdev: 14.09 }
tick
last value = 11, stats = { mean: 52.71, variance: 372.05, stdev: 19.29 }
tick
last value = 42, stats = { mean: 51.64, variance: 345.16, stdev: 18.58 }
tick
last value = 42, stats = { mean: 50.67, variance: 319.00, stdev: 17.86 }
tick
last value = 60, stats = { mean: 51.60, variance: 294.93, stdev: 17.17 }
^C 
```

Enter fullscreen mode Exit fullscreen mode

我们现在不断更新异步价值流的统计数据。整洁！

我认为异步生成器函数对于处理异步数据源特别有用。

让我知道你的想法，或者你对异步生成器和迭代器的其他使用方式有什么想法！

参考资料:

*   [等待...](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for-await...of)的
*   [ES2018:异步迭代](http://2ality.com/2016/10/asynchronous-iteration.html)
*   [Array.prototype.reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)

相关:

*   [迭代器来了](https://dev.to/nestedsoftware/the-iterators-are-coming-symboliterator-and-symbolasynciterator-in-javascript-hj)
*   [等待 JavaScript 的仔细检查](https://dev.to/nestedsoftware/careful-examination-of-javascript-await--109)
*   [流数据的指数移动平均](https://dev.to/nestedsoftware/exponential-moving-average-on-streaming-data-4hhl)
*   [如何在 Javascript 中序列化并发操作:回调、承诺和异步/等待](https://dev.to/nestedsoftware/how-to-serialize-concurrent-operations-in-javascript-callbacks-promises-and-asyncawait-3ge3)
*   [JavaScript 中带有生成器、映射、过滤和归约的惰性求值](https://dev.to/nestedsoftware/lazy-evaluation-in-javascript-with-generators-map-filter-and-reduce--36h5)