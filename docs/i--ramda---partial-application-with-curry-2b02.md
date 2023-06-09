# ❤·拉姆达-带有特殊占位符的部分应用程序

> 原文：<https://dev.to/joelnet/i--ramda---partial-application-with-curry-2b02>

[![I ❤ Ramda - Partial Application with a Special Placeholder](img/e24e23c014da829f8cf84c5fa01a7ee1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3k7R0mS_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/joelnet/articles/raw/master/articles/2018-05-09---i-heart-ramda--partial_application_with_curry/puzzle-3223941_1920-2.png)

# ❤·拉姆达简介

I ❤拉姆达系列将略有不同，其他你可能已经看到了。本系列没有使用典型的`foo`、`bar`、`baz`示例，也没有使用演示独立函数的文章，而是旨在展示 Ramda.js 的实际应用

这是第一篇文章，将重点讨论带有特殊占位符的*部分应用。*

# 局部应用与绑定

如果你用过`bind`，你可能已经熟悉了局部应用的概念。

```
const multiply = (a, b) => a * b

// partially apply 2 to multiply to create double
const double = multiply.bind(null, 2)

double(3) //=> 6 
```

Enter fullscreen mode Exit fullscreen mode

使用`bind`，我们能够将值`2`部分应用于函数`multiply`到**中的参数`a`，创建一个全新的函数** `double`。

上面的解决方案(用`bind`)工作得很好，即使我们必须调用`bind`并传入上下文(在本例中是`null`)。

# 局部涂以咖喱

处理部分应用程序的最简单的方法是**调用**的`multiply`函数。

```
import curry from 'ramda/src/curry'

const multiply = curry((a, b) => a * b)

// partially apply 2 to multiply to create double
const double = multiply(2)

double(3) //=> 6 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在代码是...不同的 T1，但是你不兴奋，我明白。但是坚持住，因为 **🧙魔术即将开始**。

## 哪里绑的不够

让我们考虑这个例子...

```
import fs from 'fs'

const output = (err, data) => {
  if (err) return console.log('ERROR:', err)
  console.log(data)
}

// How do I partially apply utf8?
fs.readFile('README.md', 'utf8', output)
fs.readFile('package.json', 'utf8', output) 
```

Enter fullscreen mode Exit fullscreen mode

所有对`readFile`的调用都将是`utf8`，现在我想到了，它们都将使用相同的`output`
函数。

在这个例子中，**不能使用**绑定，因为`bind`只能从左到右应用参数。必须创建一个全新的功能。

```
// Harrumph!
const outputUtf8File = file =>
  fs.readFile(file, 'utf8', output) 
```

Enter fullscreen mode Exit fullscreen mode

## 特殊占位符

Ramda 有一个他们称之为**的特殊占位符**，用于部分应用参数，而不管位置如何。
要使用这个特殊的占位符，首先必须简化这个函数。

```
import curry from 'ramda/src/curry'
import __ from 'ramda/src/__' // Special Placeholder
import fs from 'fs'

// curry fs.readFile to add the magic
const readFile = curry(fs.readFile) 
```

Enter fullscreen mode Exit fullscreen mode

既然`fs.readFile`已经被简化为`readFile`，我们可以使用 Ramda 的特殊占位符(`__`)作为文件名参数，并部分应用、`utf8`和`output`到`readFile`。

```
// partially apply utf8 and output.
const outputUtf8File = readFile(__, 'utf8', output)

outputUtf8File('README.md')
outputUtf8File('package.json') 
```

Enter fullscreen mode Exit fullscreen mode

😍如果此时你不兴奋，去看医生吧，因为你内心已经死了。

让我们通过一些重构来结束这个例子。

```
// let's refactor this:
outputUtf8File('README.md')
outputUtf8File('package.json')

// into this:
const files = ['README.md', 'package.json']
files.map(outputUtf8File) 
```

Enter fullscreen mode Exit fullscreen mode

也可以这样使用，因为现在我们有了更多的选择！

```
files.map(readFile(__, 'utf8', output)) 
```

Enter fullscreen mode Exit fullscreen mode

## 作业

最好的学习方法是*做*😉。所以在这个练习中，试着在函数`fetch`的基础上创建两个新函数`fetchGet`和`fetchPost`。

```
import curry from 'ramda/src/curry'
import __ from 'ramda/src/__'
import fetch from 'node-fetch' // might need this in node.js land.

const url = '???' // pick a url to test

// fetch(url, { method: 'GET' })

const fetchGet = /* ??? */
const fetchPost = /* ??? */ 
```

Enter fullscreen mode Exit fullscreen mode

如果需要帮助，在评论区发帖提问，我会尽力回答每一个问题！

# 还能做什么？

这是我正在做的一个项目的简化片段。首先我会把`tokenContract`剔除，这样**你也可以运行这个例子**。

同样，不要对这部分代码做过多解读，这只是设置。但是请注意下面如何使用`curry`函数。

```
import curry from 'ramda/src/curry'
import __ from 'ramda/src/__'

// example stub so you can run the code below
const tokenContract = {
  getTokenIndex: curry((account, tokenId) =>
    account === 1 && tokenId === 123
      ? Promise.resolve(1000)
      : Promise.reject()),
  getTokenAtIndex: curry((account, index) =>
    account === 1 && index === 1000
      ? Promise.resolve('success')
      : Promise.reject('fail')),
} 
```

Enter fullscreen mode Exit fullscreen mode

现在是肉🍖

```
const account = 1

// without partial application
tokenContract.getTokenIndex(account, 123) //=> then 1000
  .then(i => tokenContract.getTokenAtIndex(account, i)) //=> then "success"

// with partial application
tokenContract.getTokenIndex(account, 123) //=> then 1000
  .then(tokenContract.getTokenAtIndex(account)) //=> then "success" 
```

Enter fullscreen mode Exit fullscreen mode

Curry 和部分应用程序允许我们在`then`中去掉函数的两端`i =>`和`, i`。这是一个你
会经常*看到*的模式。从`then`函数提供的单个参数(在本例中是`i`)将成为在`tokenContract.getTokenAtIndex`中调用的函数的最终参数。

进一步研究:省略结尾的`i`的过程称为无点或默许编程，自变量`i`为“点”。

因为我知道我正在做的工作总是针对`account`，所以我可以将`account`值应用于两个函数。

```
const getToken = tokenContract.getTokenIndex(account)
const getTokenAtIndex = tokenContract.getTokenAtIndex(account)

getToken(123) //=> then 1000
  .then(getTokenAtIndex) //=> then "success" 
```

Enter fullscreen mode Exit fullscreen mode

# 额外学分

**函数组合将在以后的文章**中讨论，但是现在我想把这个作为额外的学分留给你
去研究和修改:

```
const account = 1

const getTokenIndex = tokenContract.getTokenIndex(account)
const getTokenAtIndex = tokenContract.getTokenAtIndex(account)

// Ramda's pipeP asynchronous function composer
const getToken = pipeP(
  getTokenIndex,
  getTokenAtIndex,
)

getToken(123) //=> then "success" 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

本文简要介绍了**局部应用**、**奉承**，甚至**功能组合**。前往 [Ramda](http://ramdajs.com/) 了解更多信息。在评论里提问，每一个问题我都会回复！

如果你觉得这很有趣，我有更多关于[媒体](https://medium.com/@joelthoms/latest)和[发展到](https://dev.to/joelnet)的文章。

在 [Twitter](https://twitter.com/joelnet) 、 [Medium](https://medium.com/@joelthoms/latest) 、 [dev.to](https://dev.to/joelnet) 和 [steemit](https://steemit.com/@joelnet) 或[joel.net](http://joel.net)订阅，以获得未来文章的通知。

[原贴于此](https://github.com/joelnet/articles/blob/master/2018-05-09---i-heart-ramda--partial_application_with_curry/Partial_Application_with_Curry.md)

干杯！

[![license CC BY 4.0](img/cfdf14d8c8a99476d3457617d720b6eb.png)T2】](https://creativecommons.org/licenses/by/4.0/)