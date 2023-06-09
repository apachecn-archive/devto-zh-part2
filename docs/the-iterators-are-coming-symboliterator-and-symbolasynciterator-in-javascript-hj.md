# 迭代器来了！JavaScript 中的迭代器和异步迭代器

> 原文：<https://dev.to/nestedsoftware/the-iterators-are-coming-symboliterator-and-symbolasynciterator-in-javascript-hj>

## 简介

本文介绍了 JavaScript 中的两种迭代器:同步迭代器和异步迭代器。前者成为 JavaScript 的一部分已经有一段时间了。后者即将在 ES2018 推出。

JavaScript 中的[迭代协议](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)非常简单。对于同步版本，我们只需要定义一个`next`函数，该函数返回一个带有`value`和`done`标志的元组。例如:

```
class SimpleIterable {
    next() {
        return { value: 3, done: true }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，JavaScript 中的许多构造期望“可迭代的”，仅仅有一个`next`函数并不总是足够好的。 [`for...of`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of) 语法就是一个例子。让我们尝试使用`for...of`来循环我们的`SimpleIterable`对象之一:

```
const iter = new SimpleIterable()
for (const value of iter) {
    console.log('value = ' + value)
} 
```

Enter fullscreen mode Exit fullscreen mode

结果是:

```
C:\dev>node iter.js
C:\dev\iter.js:8
for (const value of iter) {
                    ^
TypeError: iter is not iterable
    at Object.<anonymous> (C:\dev\iter.js:8:21) 
```

Enter fullscreen mode Exit fullscreen mode

## 同步迭代器

我们可以通过提供一个特殊的函数来解决这个问题。该功能由符号`Symbol.iterator`标识。通过将它添加到我们的类中，我们可以用`for...of` :
来实现我们的可迭代工作

```
class SimpleIterable {
    next() {
        return { value: 3, done: true }
    }

    [Symbol.iterator]() {
        return {
            next: () => this.next()
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 这是什么`[Symbol.iterator]`语法？Symbol.iterator 是一个唯一的标识符(见[符号](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol))。将它放在方括号中使它成为一个计算属性名。ES2015 中增加了计算属性(见[对象初始化器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer))。文档提供了这个简单的示例:
> 
> `// Computed property names (ES2015)`
> `var prop = 'foo';`
> `var o = {`
> `[prop]: 'hey',`
> `['b' + 'ar']: 'there'`
> 
> `console.log(o) // { foo: 'hey', bar: 'there' }`

我们再试一次:

```
C:\dev>node iter.js 
```

Enter fullscreen mode Exit fullscreen mode

这修复了我们的错误，但是我们仍然没有输出我们的值。看起来好像一旦`for...of`遇到真正的`done`标志，它就会忽略`value`。

让我们通过实际迭代一个小的值数组来使我们的例子稍微复杂一点。当我们超出数组的界限时，我们的`value`将变成`undefined`，我们的`done`标志将被设置为`true` :

```
class SimpleIterable {
    constructor() {
        this.index = 0
        this.values = [3,1,4]
    }

    next() {
        const value = this.values[this.index]
        const done = !(this.index in this.values)
        this.index += 1
        return { value, done }
    }

    [Symbol.iterator]() {
        return {
            next: () => this.next()
        }
    }   
}

const iter = new SimpleIterable()
for (const value of iter) {
    console.log('value = ' + value)
} 
```

Enter fullscreen mode Exit fullscreen mode

我们来试试:

```
C:\dev>node iter.js
value = 3
value = 1
value = 4 
```

Enter fullscreen mode Exit fullscreen mode

太好了，成功了！

## 异步迭代器

目前 JavaScript 的迭代器是同步的，但是[异步迭代器](https://github.com/tc39/proposal-async-iteration)是[即将在 ES2018](http://2ality.com/2016/10/asynchronous-iteration.html) 推出。它们已经在 node 的最新版本中实现了，我们可以使用`--harmony-async-iteration`标志来使用它们。让我们修改现有的例子，使用异步迭代器:

```
const timer = () => setInterval(()=>console.log('tick'), 500)

class SimpleAsyncIterable {
    constructor() {
        this.index = 0
        this.values = [3,1,4]
    }

    next() {
        const value = this.values[this.index]
        const done = !(this.index in this.values)
        this.index += 1
        return new Promise(
            resolve=>setTimeout(()=>resolve({ value, done }), 1000))
    }

    [Symbol.asyncIterator]() {
        return {
            next: () => this.next()
        }
    }   
}

const main = async () => {
    const t = timer()

    const iter = new SimpleAsyncIterable()
    for await (const value of iter) {
        console.log('value = ' + value)
    }

    clearInterval(t)    
}

main() 
```

Enter fullscreen mode Exit fullscreen mode

有什么不同？

*   我们可以看到，我们的`next`方法现在返回了一个承诺，即*将*解析为`{value, done}`元组，而不仅仅是返回一个`{value, done}`元组。
*   另外，我们现在实现了一个`Symbol.asyncIterator`函数来代替`Symbol.iterator`。
*   `for...of`的语法已经改成了异步形式:`for await...of`。

> 由于我们只能在标有`async`的函数中使用`await`，我将驱动代码移到了一个`async`函数中。我还添加了一个计时器，这样我们可以清楚地看到`for await...of`结构是非阻塞的。

让我们看看我们的异步可迭代函数:

```
C:\dev>node --harmony-async-iteration asyncIter.js
tick
value = 3
tick
tick
value = 1
tick
tick
value = 4
tick
tick 
```

Enter fullscreen mode Exit fullscreen mode

太好了，成功了！我们可以看到,`for await...of`用`Symbol.asyncIterator`对`await`进行各种许诺。如果`done`标志为假，`for await...of`将在循环的每次迭代中检索`value`。一旦命中一个标志为真的对象，循环就结束。

> 通过为`value`属性返回一个承诺，使用同步迭代器可以达到类似的效果。然而，在这种情况下，`done`不会是异步的。

在下一篇文章中，我将写一篇关于异步生成器函数的详细文章，这些函数可以和这个新的`for await...of`语法一起使用。

参考资料:

*   [等待...](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for-await...of)的
*   [asynciater](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/asyncIterator)
*   [迭代协议](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)
*   [T2`for...of`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of)
*   [符号](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)
*   [对象初始化器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer)
*   [异步迭代器](https://github.com/tc39/proposal-async-iteration)
*   [ES2018:异步迭代](http://2ality.com/2016/10/asynchronous-iteration.html)

相关:

*   [JavaScript 中带有生成器、映射、过滤和归约的惰性求值](https://dev.to/nestedsoftware/lazy-evaluation-in-javascript-with-generators-map-filter-and-reduce--36h5)
*   [如何在 JavaScript 中序列化并发操作:回调、承诺和异步/等待](https://dev.to/nestedsoftware/how-to-serialize-concurrent-operations-in-javascript-callbacks-promises-and-asyncawait-3ge3)
*   [等待 JavaScript 的仔细检查](https://dev.to/nestedsoftware/careful-examination-of-javascript-await--109)
*   [JavaScript 中的异步生成器和管道](https://dev.to/nestedsoftware/asynchronous-generators-and-pipelines-in-javascript--1h62)