# JavaScript 异步编程和回调

> 原文：<https://dev.to/flaviocopes/javascript-asynchronous-programming-and-callbacks-32jh>

[![JavaScript Asynchronous Programming and Callbacks](../Images/c3cd9d23eeceec135d227dc46df6c553.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1NtGvobC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://flaviocopes.com/javascript-callbacks/banner.jpg)

## 编程语言中的异步性

计算机在设计上是异步的。

异步意味着事情可以独立于主程序流发生。

在目前的消费类计算机中，每个程序都运行一个特定的时间段，然后它停止执行，让另一个程序继续执行。这个东西在一个循环中运行得如此之快，以至于不可能注意到，我们认为我们的计算机同时运行许多程序，但这是一种错觉(除了在多处理器机器上)。

程序在内部使用*中断*，这是一个发送给处理器以引起系统注意的信号。

我不会深入探讨这个问题的内部原因，但请记住，程序异步是正常的，在需要关注之前暂停执行，同时计算机可以执行其他事情。当一个程序在等待网络的响应时，它不能停止处理器，直到请求结束。

正常情况下，编程语言是同步的，有些语言通过语言或库提供了管理异步的方法。C，Java，C#，PHP，Go，Ruby，Swift，Python，默认都是同步的。其中一些通过使用线程来处理异步，产生了一个新的进程。

## JavaScript

JavaScript 默认为**同步**，单线程。这意味着代码不能创建新的线程并并行运行。

代码行是连续执行的，一个接一个，例如:

```
const a = 1
const b = 2
const c = a * b
console.log(c)
doSomething() 
```

Enter fullscreen mode Exit fullscreen mode

但是 JavaScript 诞生于浏览器内部，它最初的主要工作是响应用户的动作，比如`onClick`、`onMouseOver`、`onChange`、`onSubmit`等等。它如何用同步编程模型做到这一点呢？

答案就在它的环境中。通过提供一组能够处理这种功能的 API，**浏览器**提供了一种方法。

最近，Node.js 引入了非阻塞 I/O 环境，将这个概念扩展到文件访问、网络调用等等。

## 回调

你不能知道用户什么时候会点击一个按钮，所以你要做的是，你**为点击事件**定义一个事件处理器。该事件处理程序接受一个函数，该函数将在事件被触发时被调用:

```
document.getElementById('button').addEventListener('click', () => {
  //item clicked
}) 
```

Enter fullscreen mode Exit fullscreen mode

这就是所谓的**回调**。

回调是一个简单的函数，作为一个值传递给另一个函数，只在事件发生时执行。我们能做到这一点是因为 JavaScript 有第一类函数，这些函数可以赋给变量并传递给其他函数(称为**高阶函数**)

通常将所有客户端代码包装在`window`对象的`load`事件监听器中，该监听器仅在页面准备就绪时运行回调函数:

```
window.addEventListener('load', () => {
  //window loaded
  //do what you want
}) 
```

Enter fullscreen mode Exit fullscreen mode

回调在任何地方都可以使用，而不仅仅是在 DOM 事件中。

一个常见的例子是使用定时器:

```
setTimeout(() => {
  // runs after 2 seconds
}, 2000) 
```

Enter fullscreen mode Exit fullscreen mode

XHR 请求也接受回调，在这个例子中，通过将一个函数分配给一个属性，该函数将在特定事件发生时被调用(在这个例子中，请求的状态改变):

```
const xhr = new XMLHttpRequest()
xhr.onreadystatechange = () => {
  if (xhr.readyState === 4) {
    xhr.status === 200 ? console.log(xhr.responseText) : console.error('error')
  }
}
xhr.open('GET', 'https://yoursite.com')
xhr.send() 
```

Enter fullscreen mode Exit fullscreen mode

## 处理回调中的错误

你如何处理回调错误？一个非常常见的策略是使用 Node.js 所采用的:任何回调函数中的第一个参数都是错误对象:**错误优先回调**

如果没有错误，对象是`null`。如果有错误，它包含一些错误描述和其他信息。

```
fs.readFile('/file.json', (err, data) => {
  if (err !== null) {
    //handle error
    console.log(err)
    return
  }

  //no errors, process data
  console.log(data)
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 回调的问题

回调对于简单的案例非常有用！

然而，每个回调都增加了一层嵌套，当你有很多回调的时候，代码会很快变得复杂:

```
window.addEventListener('load', () => {
  document.getElementById('button').addEventListener('click', () => {
    setTimeout(() => {
      items.forEach((item) => {
        //your code here
      })
    }, 2000)
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

这只是一个简单的 4 层代码，但我见过更多层的嵌套，这并不有趣。

我们如何解决这个问题？

## 回调的替代品

从 ES6 开始，JavaScript 引入了几个特性来帮助我们处理不涉及回调的异步代码:

*   [承诺](https://dev.to/flaviocopes/understanding-javascript-promises-1n19-temp-slug-7431121) (ES6)和**发电机**
*   [异步/等待](https://flaviocopes.com/javascript-async-await/) (ES8)