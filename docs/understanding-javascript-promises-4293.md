# 理解 JavaScript 承诺

> 原文：<https://dev.to/flaviocopes/understanding-javascript-promises-4293>

## 承诺简介

一个承诺通常被定义为**一个最终将变得可用的价值的代理**。

承诺是处理异步代码的一种方式，无需在代码中编写太多回调。

虽然已经存在多年，但它们已经在 [ES2015](https://flaviocopes.com/ecmascript/#es2015-aka-es6) 中被标准化和引入，现在它们已经在 [ES2017](https://flaviocopes.com/ecmascript/#es2017-aka-es8) 中被[异步函数](https://flaviocopes.com/async-await)取代。

异步函数使用 promises API 作为它们的构建模块，所以理解它们是基本的，即使在新的代码中你可能会使用异步函数而不是 promises。

### 简而言之，承诺是如何起作用的

一旦承诺被调用，它将从**未决状态**开始。这意味着调用者函数继续执行，同时等待承诺进行自己的处理，并给调用者函数一些反馈。

此时，调用者函数等待它返回处于**已解决状态**或**已拒绝状态**的承诺，但是正如您所知 [JavaScript](https://flaviocopes.com/javascript/) 是异步的，因此*函数继续执行，同时承诺发挥作用*。

### 哪些 JS API 使用承诺？

除了您自己的代码和库代码，promises 还被标准的现代 Web APIs 使用，例如:

*   电池 API
*   [获取 API](https://flaviocopes.com/fetch-api/)
*   [服务人员](https://flaviocopes.com/service-workers/)

在现代 JavaScript 中，你不太可能发现自己*而不是*在使用承诺，所以让我们开始深入了解它们。

* * *

## 创造承诺

Promise API 公开了一个 Promise 构造函数，您可以使用`new Promise()` :
对其进行初始化

```
let done = true

const isItDoneYet = new Promise(
  (resolve, reject) => {
    if (done) {
      const workDone = 'Here is the thing I built'
      resolve(workDone)
    } else {
      const why = 'Still working on something else'
      reject(why)
    }
  }
) 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，承诺检查了`done`全局常量，如果这是真的，我们返回一个解析的承诺，否则返回一个拒绝的承诺。

使用`resolve`和`reject`我们可以传递回一个值，在上面的例子中我们只是返回一个字符串，但是它也可以是一个对象。

* * *

## 一诺千金

在上一节中，我们介绍了如何创建承诺。

现在让我们来看看承诺是如何被*消费*或使用的。

```
const isItDoneYet = new Promise(
  //...
)

const checkIfItsDone = () => {
  isItDoneYet
    .then((ok) => {
      console.log(ok)
    })
    .catch((err) => {
      console.error(err)
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

运行`checkIfItsDone()`将执行`isItDoneYet()`承诺并等待它解决，使用`then`回调，如果有任何错误，它将在`catch`回调中处理它。

* * *

## 连锁承诺

一个承诺可以转化为另一个承诺，形成一个承诺链。

链接承诺的一个很好的例子是由 [Fetch API](https://flaviocopes.com/fetch-api) 给出的，它是 XMLHttpRequest API 之上的一层，我们可以使用它来获取资源，并在获取资源时对要执行的承诺链进行排队。

Fetch API 是一个基于承诺的机制，调用`fetch()`相当于使用`new Promise()`定义我们自己的承诺。

### 连锁承诺的例子

```
const status = (response) => {
  if (response.status >= 200 && response.status < 300) {
    return Promise.resolve(response)
  }
  return Promise.reject(new Error(response.statusText))
}

const json = (response) => response.json()

fetch('/todos.json')
  .then(status)
  .then(json)
  .then((data) => { console.log('Request succeeded with JSON response', data) })
  .catch((error) => { console.log('Request failed', error) }) 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们调用`fetch()`从域根中找到的`todos.json`文件中获取 TODO 项的列表，并且我们创建了一个承诺链。

运行`fetch()`返回一个[响应](https://fetch.spec.whatwg.org/#concept-response)，它有许多属性，在我们引用的属性中:

*   `status`，代表 HTTP 状态码的数值
*   `statusText`，状态消息，如果请求成功，则为`OK`

`response`也有一个`json()`方法，该方法返回一个承诺，该承诺将与作为 JSON 处理和转换的主体内容一起解析。

给定这些前提，事情是这样的:链中的第一个承诺是我们定义的函数，称为`status()`，它检查响应状态，如果不是成功响应(在 200 和 299 之间)，它拒绝这个承诺。

该操作将导致承诺链跳过列出的所有连锁承诺，并将直接跳到底部的`catch()`语句，记录`Request failed`文本和错误消息。

如果成功了，它调用我们定义的 json()函数。因为前一个承诺成功时返回了`response`对象，所以我们将它作为第二个承诺的输入。

在这种情况下，我们返回 JSON 处理的数据，因此第三个承诺直接接收 JSON:

```
.then((data) => {
  console.log('Request succeeded with JSON response', data)
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们只需将它记录到控制台。

* * *

## 处理错误

在前一节的例子中，我们有一个附加在承诺链上的`catch`。

当承诺链中的任何一项失败并引发错误或拒绝承诺时，控制权将转移到链中最近的`catch()`语句。

```
new Promise((resolve, reject) => {
  throw new Error('Error')
})
  .catch((err) => { console.error(err) })

// or

new Promise((resolve, reject) => {
  reject('Error')
})
  .catch((err) => { console.error(err) }) 
```

Enter fullscreen mode Exit fullscreen mode

### 级联错误

如果在`catch()`中引发了一个错误，可以追加第二个`catch()`来处理它，以此类推。

```
new Promise((resolve, reject) => {
  throw new Error('Error')
})
  .catch((err) => { throw new Error('Error') })
  .catch((err) => { console.error(err) }) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 精心策划的承诺

### `Promise.all()`

如果您需要同步不同的承诺，`Promise.all()`帮助您定义一个承诺列表，并在它们都解决后执行一些事情。

示例:

```
const f1 = fetch('/something.json')
const f2 = fetch('/something2.json')

Promise.all([f1, f2]).then((res) => {
    console.log('Array of results', res)
})
.catch((err) => {
  console.error(err)
}) 
```

Enter fullscreen mode Exit fullscreen mode

[ES2015 析构赋值](https://flaviocopes.com/ecmascript/#destructuring-assignments)语法允许你也做

```
Promise.all([f1, f2]).then(([res1, res2]) => {
    console.log('Results', res1, res2)
}) 
```

Enter fullscreen mode Exit fullscreen mode

你当然不局限于使用`fetch`，**任何承诺都行**。

### `Promise.race()`

当你传递给它的任何一个承诺被解析时运行，当 n 是你传递给它的承诺时，它运行附加的回调 n 次。

示例:

```
const f1 = fetch('/something.json')
const f2 = fetch('/something2.json')

Promise.race([f1, f2]).then((res) => {
    console.log(res)
})
.catch((err) => {
  console.error(err)
}) 
```

Enter fullscreen mode Exit fullscreen mode