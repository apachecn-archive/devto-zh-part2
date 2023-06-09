# JavaScript 中带有生成器、映射、过滤和归约的惰性求值

> 原文：<https://dev.to/nestedsoftware/lazy-evaluation-in-javascript-with-generators-map-filter-and-reduce--36h5>

我的朋友 [edA-qa](https://dev.to/mortoray) 最近在 [twitch](https://www.twitch.tv/mortoray) 上用 Rust 语言做了一些现场编程。一段有趣的代码出现了:

```
(1..).filter(|num| num%2 == 0).take(n).sum() 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到，有些运算是在一个无界的数字范围上进行的:`(1..)`，换句话说，从 1 开始，一直进行下去。这种代码是函数式编程范例的一部分，并利用了“惰性计算”，即表达式实际上只在需要的基础上计算。

我最近一直在用 JavaScript 编程，我很好奇这是否也适用于 JavaScript。我知道 JavaScript 有类似于 [filter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 、 [map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 和 [reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) 的函数可以处理数组，但是我想知道它们是否也可以处理[生成器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators)。

事实证明，他们现在没有，至少没有开箱即用。假设我们有一个生成器，它只生成从 1:
开始的整数

```
const numbers = function* () {
    let i = 1
    while (true) {
        yield i++ 
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

可以直接用这个来做过滤、贴图之类的操作吗？

```
let result = numbers.map(num=>num**2).slice(0,3) //doesn't work :(
console.log('result = ' + result) 
```

Enter fullscreen mode Exit fullscreen mode

这产生:

```
let result = numbers.map(num=>num**2).slice(0,3) //doesn't work :(
                 ^

TypeError: numbers.map is not a function
    at Object.<anonymous> (C:\dev\lazy.js:66:18) 
```

Enter fullscreen mode Exit fullscreen mode

尝试先启动发电机也不起作用:

```
let result = numbers().map(num=>num**2).slice(0,3) //doesn't work :(
console.log('result = ' + result) 
```

Enter fullscreen mode Exit fullscreen mode

这产生:

```
TypeError: numbers(...).map is not a function
    at Object.<anonymous> (C:\dev\lazy.js:66:20) 
```

Enter fullscreen mode Exit fullscreen mode

我决定用 JavaScript 编写一个简单的类包装器，使类似于 Rust 示例的功能成为可能。

下面的`Lazy`类充当所需行为的基类。

```
class Lazy {
    constructor(iterable, callback) {
        this.iterable = iterable
        this.callback = callback
    }

    filter(callback) {
        return new LazyFilter(this, callback)
    }

    map(callback) {
        return new LazyMap(this, callback)
    }

    next() {
        return this.iterable.next()
    }

    take(n) {
        const values = []
        for (let i=0; i<n; i++) {
            values.push(this.next().value)
        }

        return values
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`Lazy`类只是包装了一个简单的 JavaScript iterable(参见[迭代协议](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols))。默认情况下，如果您调用它的`next`方法，它会将调用委托给它所包装的 iterable。

注意，对`filter`和`map`的调用本身不会做太多事情:它们只是实例化一个对象。下面是`LazyFilter`和`LazyMap` :
的实现

```
class LazyFilter extends Lazy {
    next() {
        while (true) {
            const item = this.iterable.next()

            if (this.callback(item.value)) {
                return item
            }
        }
    }
}

class LazyMap extends Lazy {
    next() {
        const item = this.iterable.next()

        const mappedValue = this.callback(item.value)
        return { value: mappedValue, done: item.done }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这两个子类也只是实现了 JavaScript 的`next`方法。

现在让我们看看这段代码的运行情况！下面是一些运行这段代码的简单例子:

```
 let result = new Lazy(numbers()).map(num=>num*3).take(4).reduce((a,v) => a + v)
console.log('result = ' + result)

result = new Lazy(numbers()).filter(n=>n%2==0).take(4).reduce((a,v) => a + v)
console.log('result = ' + result)

result = new Lazy(numbers()).filter(n=>n%2==0).map(num=>num**2).take(4).reduce((a,v) => a + v)
console.log('result = ' + result)

result = new Lazy(numbers()).map(num=>num**2).filter(n=>n%2==0).take(4).reduce((a,v) => a + v)
console.log('result = ' + result) 
```

Enter fullscreen mode Exit fullscreen mode

下面是在节点
中运行这个例子的结果

```
C:\dev>node lazy.js
result = 30
result = 20
result = 120
result = 120 
```

Enter fullscreen mode Exit fullscreen mode

如果你不熟悉这种类型的代码，我会试着解释它是如何工作的。我们来看第一个例子:

```
let result = new Lazy(numbers()).map(num=>num*3).take(4).reduce((a,v) => a + v)
console.log('result = ' + result) 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们来看一下`take`函数。这个函数启动一切。在调用`take`之前，除了创建一些对象之外，什么也不会发生。

`take`函数将对`map(num=>num*3)`返回的`LazyMap`对象调用`next` 4 次。这又会在由`numbers()`返回的发电机上调用`next` 4 次。`map`会将这些数字从生成器传递到`num=>num*3`回调函数，回调函数会将每个数字乘以 3，然后再将结果传递回`take`。Take 返回一个普通的 JavaScript 数组。在这种情况下，它将包含`[3,6,9,12]`。现在我们可以调用`Array.reduce`方法，该方法使用提供的回调将数组折叠为单个值。在这种情况下，所有的数字加在一起产生最终结果“30”。

> 有几个潜在的“陷阱”需要注意:首先，试图在不停的生成器上调用`reduce`是行不通的，因为`reduce`函数永远不会用完要处理的值。然而，我们可以编写一个版本的 reduce 来迭代地折叠值。此外，在使用`filter`时一定要小心。`filter`直到达到与其回调匹配的值才会停止。如果我们试图在一个永远运行的生成器上调用`filter`，而它没有找到任何匹配，那么`filter`也会永远运行，导致我们的程序挂起。

我认为 JavaScript 支持任何 iterable 作为像`map`和`filter`这样的函数的目标，甚至可能是`reduce`，而不仅仅是数组，会更优雅。也许 Mozilla 会在下一个版本中这么做，并提供漂亮的语法糖，比如 Rust `(1..)`语法，用于无限制的惰性范围。

相关:

*   [如何在 JavaScript 中序列化并发操作:回调、承诺和异步/等待](https://dev.to/nestedsoftware/how-to-serialize-concurrent-operations-in-javascript-callbacks-promises-and-asyncawait-3ge3)
*   [等待 JavaScript 的仔细检查](https://dev.to/nestedsoftware/careful-examination-of-javascript-await--109)
*   迭代器来了！JavaScript 中的【Symbol.iterator】和【Symbol.asyncIterator】
*   [JavaScript 中的异步生成器和管道](https://dev.to/nestedsoftware/asynchronous-generators-and-pipelines-in-javascript--1h62)