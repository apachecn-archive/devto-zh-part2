# JavaScript 中的基本函数式编程模式

> 原文：<https://dev.to/nestedsoftware/basic-functional-programming-patterns-in-javascript-49p2>

几年前，我找到了一本由[阿尔瑙·桑切斯](https://github.com/tokland)撰写的很有帮助的[教程](https://github.com/tokland/tokland/wiki/RubyFunctionalProgramming)，它展示了普通的过程编程模式如何被函数式方法所取代。教程是用 Ruby 写的。最近我想起了它，我想我应该将该教程中的一些例子转换成 JavaScript(然而，本文的文本是原始内容)。

## 纯函数

函数式编程的核心是纯函数的概念。纯函数有几个特征:

*   我们可以反复调用一个纯函数，只要参数相同，它总会返回相同的值。这意味着获取用户输入、获取当前系统时间或检索特定股票价值的函数不是纯函数:这些函数不能保证每次都返回相同的信息，即使我们用相同的参数调用它们。
*   一个纯粹的函数没有副作用:如果一个函数把一些东西打印到屏幕上，或者保存到数据库，或者发送一条文本消息，那么它就不是纯粹的。另一个例子是有状态性:如果调用一个函数改变了该函数范围之外的变量，这也是一个副作用:在该函数被调用之后，世界变得不一样了，所以它不是纯粹的。

因为它们非常简单，纯函数有很多潜在的好处:它们更容易理解和测试。它们也很容易缓存(memoize)。拥有纯函数有助于多线程/多处理，因为它们不需要在共享状态下同步。还有其他好处，包括可能的编译器优化。我们将在本文中探讨的主要好处是，我们如何利用函数式技术来减少重复，并使我们的代码更干净、更易维护。然而，实现这一好处可能会自动产生其他一些好处。

因此，纯粹的功能是好的，但是它们显然是有限的:它们不能成为软件系统的全部。函数式编程的主要思想是处理编程中更复杂和混乱的方面，如处理状态和副作用，并在这些混乱的部分和代码的其余部分之间定义一个清晰的接口:我们编写纯函数，并在它们周围包装一些更高级别的代码，以处理编程中不纯的方面。

## 陈述句与祈使句

将函数式编程与过程式编程区分开来的另一个特征是，它强调声明式编程风格。在过程式编程中，我们经常会看到命令式代码向我们展示如何做某事。声明式方法告诉我们结果应该是什么样子。我们将在本文的示例中看到这种差异。

> 人们可以用函数式语言编写命令式代码，也可以用过程式语言编写声明式代码！所以这更多的是侧重点的不同。函数式编程更倾向于强调声明性方法。

## 三位一体的函数式编程

迭代在许多方面是编程的面包和黄油。在下面的例子中，我们将探索如何使用循环将一些熟悉的过程迭代模式转换成函数方法。这些例子的简单性使得它们非常适合作为教程，但是核心思想——我们可以将纯函数插入到更高阶的抽象中——是函数式编程的核心。

> 高阶函数是将另一个函数作为参数和/或返回另一个函数的函数。在 JavaScript 中，函数是“一等公民”这意味着我们可以将它们赋给变量，在其他函数中创建它们，并像其他对象一样将它们作为参数传递。如果你熟悉回调，那么你已经使用过高阶函数了！

函数式编程中的迭代依赖于高阶函数的神圣三位一体: [map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 、 [filter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 和 [reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) 。让我们依次探索每一个。然后，我们还将看看几个简单的变化:

## 【Init+each+push-】>地图

让我们将一个列表转换成另一个列表。对于源列表中的每一项，我们会在将它放入目标列表之前对其应用一些函数。例如，让我们获取一个字符串列表，并产生一个大写的相同字符串的列表。

过程化:我们创建一个空列表来保存我们的结果。我们遍历源代码列表。对于每一项，我们对其应用一个函数，并将其添加到结果列表中。

```
let uppercaseNames = []
for (let name of ['milu', 'rantanplan']) {
  uppercaseNames.push(name.toUpperCase())
}
console.log(uppercaseNames) // ['MILU', 'RANTANPLAN'] 
```

**功能**:我们在我们的源列表上执行一个`map`操作。我们为`map`提供了一个回调函数。在后台，`map`将遍历源列表，并对每一项调用我们的回调函数，将其添加到结果列表中。这里的目标是提取`for`循环样板文件，并将其隐藏在一个高阶函数后面。剩下的工作就是编写一个包含我们关心的实际逻辑的纯函数。

```
const uppercaseNames = ['milu', 'rantanplan'].map(name => name.toUpperCase())
console.log(uppercaseNames) // ['MILU', 'RANTANPLAN'] 
```

## Init+each+条件推送- >过滤器

这里，我们从一个源列表开始，并对它应用一个过滤器:对于每一项，如果它符合标准，我们就保留它，否则就从结果列表中排除它。

**过程化**:我们建立一个空的结果列表，然后遍历一个源列表，将匹配的条目添加到结果列表中。

```
let filteredNames = []
for (let name of ['milu', 'rantanplan']) {
  if (name.length === 4) {
    filteredNames.push(name)
  }
}
console.log(filteredNames) // ['milu'] 
```

**Functional** :我们在对`filter`的回调中提供我们的匹配逻辑，我们让`filter`做遍历数组和根据需要应用过滤回调的工作。

```
const filteredNames = ['milu', 'rantanplan'].filter(name => name.length === 4)
console.log(filteredNames) // ['milu'] 
```

## Init+each+accumulate->reduce

让我们获取一个字符串列表，并返回所有字符串的总长度。

**过程化**:我们在一个循环中迭代，将每个字符串的长度添加到我们的`sumOfLengths`变量中。

```
let sumOfLengths = 0
for (let name of ['milu', 'rantanplan']) {
  sumOfLengths += name.length
}
console.log(sumOfLengths) // 14 
```

**功能**:首先我们`map`我们的列表为一个长度列表，然后我们将这个列表传递给`reduce`。对于每一项，`reduce`运行我们提供的 reducer 回调，传递一个累加器对象和当前项作为参数。无论我们从缩减器返回什么，都将替换为下一次迭代传递的累加器。同样，我们只是提供了一个简单的纯函数作为回调函数，让 reduce 来完成剩下的工作。

```
const total = (acc, len) => len + acc

const sumOfLengths = ['milu', 'rantanplan'].map(v=>v.length).reduce(total, 0)
console.log(sumOfLengths) // 14 
```

> `reduce`很强大。事实上，我们可以用它来编写`map`和`filter`的实现。

## Init+each+accumulate+push->扫描

比方说，我们不仅要获得最终的总长度，还要跟踪中间值。在 Haskell 中，我们可以使用`scan`，但是 JavaScript 没有内置的`scan`函数。我们自己造吧！

**过程化**:我们在`for`循环的每次迭代中用累计值更新一个列表。

```
let lengths = [0]
let totalLength = 0
for (let name of ['milu', 'rantanplan']) {
  totalLength += name.length
  lengths.push(totalLength)
}
console.log(lengths) // [0, 4, 14] 
```

**功能**:代码看起来非常类似于使用`reduce`的版本。

```
const total = (acc, item) => acc + item.length

const lengths = ['milu', 'rantanplan'].scan(total, 0)
console.log(lengths) //[0, 4, 14] 
```

下面是`scan`的一个可能的实现:这次不是直接传递回调函数给 reduce，而是在回调函数周围包装一个新的 reducer，`appendAggregate`。`appendAggregate`从累加器中取出包含累计值的数组，并创建一个包含最新值的累计值的副本。这样，我们得到的不是最后从`reduce`返回的单个值，而是所有中间总数的数组。

```
Array.prototype.scan = function (callback, initialValue) {
  const appendAggregate = (acc, item) => {
    const aggregate = acc[acc.length-1] //get last item
    const newAggregate = callback(aggregate, item)
    return [...acc, newAggregate]
  }

  const accumulator = [initialValue]

  return this.reduce(appendAggregate, accumulator)
} 
```

## Init+each+hash - > mash

让我们看最后一个例子。假设我们想将一个列表转换成一个键值对的映射。对于每一项，键将是该项，值将是以某种方式处理该项的结果。在下面的例子中，我们将把一个字符串列表转换成一个对象，这个对象把每个字符串作为一个键，它的长度作为值。

过程性的:我们创建一个空的对象。对于列表中的每一项，我们将该项和它相应的值一起作为键添加到我们的对象中。

```
const items = ['functional', 'programming', 'rules']

const process = item => item.length

let hash = {}
for (let item of items) {
  hash[item] = process(item)
}
console.log(hash) //{functional: 10, programming: 11, rules: 5} 
```

**Functional** :我们将每一项转换成一个包含键和值的数组。`mash`将这些元组折叠到一个对象中，在那里它们成为实际的键/值对。

```
const items = ['functional', 'programming', 'rules']

const mashed = items.mash(item => [item, item.length])
console.log(mashed) // {functional: 10, programming: 11, rules: 5}

//also works: 
const alsoMashed = items.map(item => [item, item.length]).mash()
console.log(alsoMashed) // {functional: 10, programming: 11, rules: 5} 
```

让我们来看看`mash`的一个可能的实现:我们使用与`scan`相同的技巧。这次我们给`reduce`供应`addKeyValuePair`。每次`reduce`执行这个回调，它都会创建一个新的对象，这个对象包含累加器中的现有值以及一个对应于当前键值对的新值。

```
Array.prototype.mash = function(callback) {
    const addKeyValuePair = (acc, item) => {
        const [key, value] = callback ? callback(item) : item
        return {...acc, [key]: value}
    }

    return this.reduce(addKeyValuePair, {})
} 
```

> 上面两个例子修改了`Array.prototype`来支持`scan`和`mash`。我不建议在实践中做这种猴子打补丁。这里，为了简单起见，我这样做是为了让所有的例子看起来都一样。在实际应用中，我们可以用接受数组作为参数的版本来替换数组函数。这些可以用一个`compose`函数链接在一起。为了避免重新发明轮子，我们也可以使用第三方功能实用程序库，如 [Ramda](https://ramdajs.com/) 。

## 讨论

上面的例子希望能够展示我们如何使用函数式编程来减少日常代码中的样板文件，让它保持[干爽](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)。注意，这些例子中的所有回调都是纯函数。这意味着它们不会改变外部世界的状态。特别是，`appendAggregate`和`addKeyValuePair`不修改它们作为参数接收的累加器对象。取而代之的是，他们创建一个对象的副本，该副本包含任何需要的更改。

> 我不会在这篇文章中详细阐述这一点，但这可能是一个好地方，至少可以提一下[深和浅复制](https://we-are.bookmyshow.com/understanding-deep-and-shallow-copy-in-javascript-13438bad941c)的区别。使用扩展语法`...`的示例代码正在执行浅层复制。一般来说，我们应该考虑一个函数是否会产生副作用，以及在函数运行过程中，传递给函数的状态是否会被外界改变。因为我们的 reducers 没有改变传入的参数，而且 JavaScript 是单线程/非抢占式的，所以这里可以使用浅拷贝。然而，这是一个我们应该经常注意的问题。

使用纯函数通常会让我们作为程序员的生活更轻松。然而，它的一个缺点是在某些情况下会影响性能:在我们的例子中，当处理大型列表时，我们会创建许多短命的对象，使垃圾收集器一直很忙。通常，在这个拥有大量 RAM 的强大计算机的时代，这在实践中不是问题。然而，如果它确实成为一个问题，那么我们可能不得不做出一些设计妥协。

> Haskell 是一种非常纯粹的函数式语言，它利用函数的有保证的纯粹性和惰性来优化垃圾收集。然而，由于像 JavaScript 这样的语言并没有强制实现纯粹性，所以这在 V8 引擎中似乎不太可行。

## 参考文献

*   [干](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)
*   [纯函数](https://www.sitepoint.com/functional-programming-pure-functions/)
*   [纯函数的好处](https://alvinalexander.com/scala/fp-book/benefits-of-pure-functions)
*   [贴图](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)，[滤镜](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)，[减少](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)
*   [Ruby 函数式编程](https://github.com/tokland/tokland/wiki/RubyFunctionalProgramming)
*   [Ramda 功能 JS 库](https://ramdajs.com/)
*   [JavaScript 中的浅层和深层复制](https://we-are.bookmyshow.com/understanding-deep-and-shallow-copy-in-javascript-13438bad941c)
*   [Haskell 中的垃圾收集](https://wiki.haskell.org/GHC/Memory_Management)

## 相关

*   [React 中带有表单的函数式编程](https://dev.to/nestedsoftware/forms-in-react-a-functional-programming-primer-183)

## 功能概念的更高级应用

*   [还原](https://redux.js.org/introduction/motivation)
*   [MapReduce](https://en.wikipedia.org/wiki/MapReduce)
*   [功能反应编程](https://en.wikipedia.org/wiki/Functional_reactive_programming)