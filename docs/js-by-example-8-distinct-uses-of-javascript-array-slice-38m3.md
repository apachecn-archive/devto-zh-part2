# JS By Example:JavaScript 数组切片的 8 种不同用法

> 原文：<https://dev.to/kball/js-by-example-8-distinct-uses-of-javascript-array-slice-38m3>

JavaScript array `slice`方法是 JavaScript 语言中最强大和最常用的内置方法之一。

随着 React 和其他面向功能的 JavaScript 实践的兴起，它变得更加重要，原因有二:

1.  函数式编程，尤其是高级函数，大量使用数据列表
2.  函数式编程需要[纯函数](https://www.sitepoint.com/functional-programming-pure-functions/)，不会引起副作用或修改其输入数据的函数。

JavaScript 数组`slice`方法符合这两个标准。

`slice`方法提供了一种机制，用于创建列表子集的浅层副本，而不修改原始列表。因此，它为编写功能性 JavaScript 提供了一个关键的构建块。

在这篇文章中，我们将通过例子来掌握`slice`方法，探索它的 8 种不同用法。

*注意:*`slice`方法不要与`splice`方法混淆，后者在位置修改数组*。*

Slice 保持原始数组不变并返回选定项的浅层副本，splice 修改原始数组。

## Javascript 数组切片如何工作

在我们深入一些更高级的用法之前，让我们先看看`slice`方法的基础。

如 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)中所示，`slice`是一个数组上的一个方法，最多可以有两个参数:

```
arr.slice([begin[, end]]) 
```

Enter fullscreen mode Exit fullscreen mode

`begin`参数是一个从 0 开始的索引，标识从哪里开始提取，而`end`参数是一个从 0 开始的索引，标识从哪里结束。

`slice`方法创建了一个数组的副本，从`begin`开始，一直到(但不包括)`end`。

它还接受负索引，这表示从数组末尾往回计数。

## 基本用途

我们的前 4 种用法突出了`slice`的核心功能。

### 使用 1:简单复制

```
const arr2 = arr.slice(); 
```

Enter fullscreen mode Exit fullscreen mode

不带任何参数的 Slice 执行简单的浅层复制。在现代 JavaScript 中，使用 [spread 操作符](https://dev.to/kball/understanding-the-spread-operator-in-javascript-48op)来实现这一功能更加习惯，但是如果在旧的代码库中工作或者没有使用 babel 的构建步骤，您可能仍然希望使用`slice`。

### 使用 2:从 N 开始的子阵列

使用`slice`方法最简单的方法就是从 n 开始抓取数组的所有元素。

一个用例的例子可能是你想弹出一个数组的第一个元素并使用它，返回剩余的数组，但是你想在不修改原始数组的情况下这样做。

```
function useOne(arr) {
  const usedItem = arr[0];
  // do something with usedItem
  return arr.slice(1);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 使用 3:数组的最后 N 个

使用`slice`的另一种方法是抓取数组的末尾，利用负索引从末尾开始计数的事实。

这种负索引使得去除你想要的任何元素变得非常简单。例如，如果你想抓取 3:

```
const last3 = arr.slice(-3) 
```

Enter fullscreen mode Exit fullscreen mode

### 使用 4:数组的前 n 个

在拉掉数组的前面，我们需要开始使用方法的第二个参数:`end`。

当两个参数都被传递时，`slice`方法返回一个集合，从`begin`开始，直到但不包括`end`。

由于 JavaScript 数组是从零开始的(索引从 0 开始)，这使得提取前 N 个元素变得非常简单:

```
const first4 = arr.slice(0, 4) 
```

Enter fullscreen mode Exit fullscreen mode

### 使用 5:n 的内段，从 m 开始

推广 use 5，如果我们想使用`slice`从任意索引开始提取数组的一部分，该怎么办？

为此，我们需要从`(begin, length)`转换到`(begin, end)`。幸运的是，数学很简单，我们可以定义一个简单的函数来做这件事:

```
function pullSegment(arr, begin, length) {
  return arr.slice(begin, begin + length);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 处理类似数组的对象

`slice`方法也可以用在类似数组的对象上。这些对象的行为*很像*数组，但实际上并不是数组。这些基本上是任何包含一个`length`值的对象，可以通过数字索引访问，但不包括数组方法。

一些例子包括`arguments`(用于访问传递给函数的所有参数的关键字)、`NodeLists`(从任何返回节点列表的 DOM API 方法返回)，甚至是使用数字索引并添加了`length`属性的原始对象。

要在一个类似数组的对象上使用`slice`方法，你需要直接从`Array.prototype`中引用它，就像这样:

```
Array.prototype.slice.call(arguments); 
```

Enter fullscreen mode Exit fullscreen mode

这导致了一些有价值的用途:

### 使用 6:将类似数组的对象转换成数组

Slice 操作符在类似数组的对象上的一个常见用途是将它们转换成实际的数组。例如:

```
const args = Array.prototype.slice.call(arguments); 
```

Enter fullscreen mode Exit fullscreen mode

你为什么想这么做？来访问数组方法。例如，想象一个函数是这样的:

```
function addOne() {
  return arguments.map(i => i+1);
} 
```

Enter fullscreen mode Exit fullscreen mode

看起来这是可行的，但是如果你尝试这样做，你会得到:

```
> addOne(1, 2, 3)
TypeError: arguments.map is not a function
    at test (repl:2:18)
    at repl:1:1
    at ContextifyScript.Script.runInThisContext (vm.js:44:33)
    at REPLServer.defaultEval (repl.js:239:29)
    at bound (domain.js:301:14)
    at REPLServer.runBound [as eval] (domain.js:314:12)
    at REPLServer.onLine (repl.js:440:10)
    at emitOne (events.js:120:20)
    at REPLServer.emit (events.js:210:7)
    at REPLServer.Interface._onLine (readline.js:279:10) 
```

Enter fullscreen mode Exit fullscreen mode

这是因为`arguments`实际上并不是一个数组，而是一个类似数组的对象。您可以使用`slice`实现该功能，如下所示:

```
function addOne() {
  return Array.prototype.slice.call(arguments).map(i => i+1);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在你得到了你所期望的:

```
> addOne(1, 2, 3)
[2, 3, 4] 
```

Enter fullscreen mode Exit fullscreen mode

### 使用 7:将任意长度的多余参数强制放入一个数组中

有时你想允许一个函数有任意数量的参数。

较新版本的 JavaScript 已经引入了所谓的 [rest 语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)来处理这个问题，但是如果你无法支持没有翻译系统的旧浏览器，你可以用`slice` :
来处理

```
function myFunc(a, b) {
  const extraArgs = Array.prototype.slice.call(arguments, 2);
} 
```

Enter fullscreen mode Exit fullscreen mode

这允许用任意数量的参数调用 myFunc。例如:

```
myFunc(1, 2, 3, 4, 5, 6, 7, 8) 
```

Enter fullscreen mode Exit fullscreen mode

在里面你会看到`a === 1`、`b === 2`和`extraArgs === [3, 4, 5, 6, 7, 8]`

## 功能数组操作

`slice`的另一个巨大用途是以函数/纯粹的方式操作数组，而不修改原始数组。

### 使用 8:修改数组中的某个索引

在函数上下文中，`slice`的一个强大而常见的用途是替换数组中特定项的值。

这很简单，你只需要分配新的值，但是在函数世界中你不能修改原始数组。

相反，您可以将`slice`与新的 [JavaScript spread 操作符](https://dev.to/kball/understanding-the-spread-operator-in-javascript-48op)结合起来，返回一个新的数组，该数组与您想要更新的索引相同:

```
function replaceIdx(arr, index, newVal) {
  return [
    ...arr.slice( 0, index ),
    newVal,
    ...arr.slice( index + 1)
  ],
} 
```

Enter fullscreen mode Exit fullscreen mode

### 部分功能应用

函数式编程中的另一个常见模式是所谓的[部分函数应用](https://jrsinclair.com/articles/2016/gentle-introduction-to-functional-javascript-functions/#partialapplication):预先将参数应用于函数，然后返回一个新函数。

这种模式允许您组合函数，通过让您使用带有不同预应用参数的相同核心函数来创建更大的可重用性。

虽然像 Haskell [这样更纯粹的函数式语言本身支持部分函数应用](https://wiki.haskell.org/Partial_application)，但是在 JavaScript 中，我们可以使用`slice` :
实现一个函数来实现它

```
var partial = function() {
  const fn = arguments[0];
  const args = Array.prototype.slice.call(arguments, 1);

  // Return a function that calls fn
  return function() {
    var remainingArgs = Array.prototype.slice.call(arguments);
    return fn.apply(this, args.concat(remainingArgs));
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 包装完毕

正如您在这一点上所看到的，Array `slice`方法是您的 JavaScript 工具箱中非常有用的工具，尤其是当您开始转向更函数式的编程方式时。

我相信还有更多使用`slice`的好例子——如果你有一个好例子，在下面的评论中分享吧！我很乐意更新帖子。

* * *

附:如果你对这类话题感兴趣，你或许应该[在 Twitter 上关注我](https://twitter.com/kbal11)或者加入我的邮件列表。我发出一份名为“[周五前端](https://zendev.com/friday-frontend.html)”的每周简讯。每周五我会发出 15 个链接，链接到 CSS/SCSS、JavaScript 和其他各种精彩的前端新闻中的最佳文章、教程和公告。在这里报名:【https://zendev.com/friday-frontend.html T4】