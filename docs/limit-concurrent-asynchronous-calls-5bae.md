# 限制并发异步调用

> 原文：<https://dev.to/ycmjason/limit-concurrent-asynchronous-calls-5bae>

尽管 Javascript 被设计成单线程的，但是你仍然可以同时做一些事情。

例如，我们可以同时读取多个文件。

```
const readFile = require('util').promisify(require('fs').readFile);

const readAllFiles = async (paths) => {
  return await Promise.all(paths.map(p => readFile(p, 'utf8')));
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，读取文件在计算上是非常昂贵的；如果有超过 10k 的路径，您可能会听到您机器上的风扇在您的机器挣扎时加速。您的节点服务器/程序的响应速度也会明显变慢，因为在操作系统的线程池中有 10k 以上的文件读取操作与节点服务器竞争。

解决办法很简单。简单地限制线程池中文件读取操作的数量。换句话说，限制并发调用的数量为`readFile`。

让我们定义一个通用函数`asyncLimit(fn, n)`，它将返回一个与`fn`完全相同的函数，但是对`fn`的并发调用数量限制为`n`。我们将假设`fn`返回一个`Promise`。

```
const asyncLimit = (fn, n) => {
  return function (...args) {
    return fn.apply(this, args);
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

因为我们知道`asyncLimit`返回一个函数，这个函数做`fn`做的事情，我们首先把它写出来。注意，我们没有使用 arrow 函数，因为`fn`可能需要绑定到`this`。箭头函数没有自己的绑定。

如果你对 Javascript 中的`this`不熟悉，稍后阅读[我的文章](https://dev.to/ycmjason/let-me-explain-to-you-what-is-this-javascript-44ja)解释什么是`this`。现在，忽略它。

```
const asyncLimit = (fn, n) => {
  let pendingPromises = [];
  return function (...args) {
    const p = fn.apply(this, args);
    pendingPromises.push(p);
    return p;
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

由于`fn`返回一个`Promise`，我们可以通过遵守它们返回的承诺来跟踪每个调用的“过程”。我们信守列表中的承诺`pendingPromises`。

```
const asyncLimit = (fn, n) => {
  let pendingPromises = [];
  return async function (...args) {
    if (pendingPromises.length >= n) {
      await Promise.race(pendingPromises);
    }

    const p = fn.apply(this, args);
    pendingPromises.push(p);
    return p;
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们将返回函数标记为`async`，这使我们能够在函数中使用`await`。我们只想在并发调用少于`n`的情况下执行`fn`。`pendingPromises`包含所有以前的承诺。所以我们可以检查`pendingPromises.length`来找出有多少个并发调用。

如果是`pendingPromises.length >= n`，我们需要等到其中一个`pendingPromises`结束后再执行。所以我们加上了`await Promise.race(pendingPromises)`。

```
const asyncLimit = (fn, n) => {
  let pendingPromises = [];
  return async function (...args) {
    if (pendingPromises.length >= n) {
      await Promise.race(pendingPromises);
    }

    const p = fn.apply(this, args);
    pendingPromises.push(p);
    await p;
    pendingPromises = pendingPromises.filter(pending => pending !== p);
    return p;
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

一旦它们完成，我们就想摆脱`pendingPromises`中的承诺。首先我们执行`fn`，它返回`p`。然后我们将`p`添加到`pendingPromises`中。在这之后，我们可以做`await p`；`p`将在这一行之后结束。所以我们干脆从`pendingPromises`中`filter`出`p`。

我们差不多完成了。让我们回顾一下我们正在做的事情:

如果`pendingPromises.length < n`

1.  我们致电`fn`并获得承诺`p`
2.  将`p`推到`pendingPromises`上
3.  等待`p`完成
4.  从`pendingPromises`中移除`p`
5.  返回 p

如果`pendingPromises.length >= n`，我们将等到其中一个`pendingPromises`解决/拒绝后再做上述操作。

我们的代码有一个问题。让我们考虑以下情况:

```
const f = limitAsync(someFunction, 1);
f(); // 1st call, someFunction returns promise p1
f(); // 2nd call, someFunction returns promise p2
f(); // 3rd call, someFunction returns promise p3 
```

Enter fullscreen mode Exit fullscreen mode

第一次通话非常顺利，`pendingPromises.length`变成了`1`。

由于`pendingPromises.length >= 1`，我们知道第二个和第三个呼叫都将呼叫`await Promise.race([p1])`。这意味着当`p1`结束时，第二个和第三个调用都将得到通知并同时执行`someFunction`。

简单地说，我们的代码不会让第三个调用等待第二个调用完成！

我们知道第二次呼叫将首先得到通知，并从`await Promise.race([p1])`开始恢复。第二个调用执行`someFunction`并将其承诺推给`pendingPromises`，然后它将执行`await p`。

与第二次呼叫`await p`一样，第三次呼叫将从`await Promise.race([p1])`开始恢复。这就是问题所在。当前的实现允许第三个调用执行`someFunction`等等等等。

但是我们想要的是第三个调用再次检查`pendingPromises.length >= n`并执行`await Promise.race([p2])`。为此，我们可以简单地将`if`改为`while`。

所以最后的代码应该是:

```
const asyncLimit = (fn, n) => {
  let pendingPromises = [];
  return async function (...args) {
    while (pendingPromises.length >= n) {
      await Promise.race(pendingPromises).catch(() => {});
    }

    const p = fn.apply(this, args);
    pendingPromises.push(p);
    await p.catch(() => {});
    pendingPromises = pendingPromises.filter(pending => pending !== p);
    return p;
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

注意，我在`Promise.race`和`await p`中添加了`.catch(() => {})`。这是因为我们不关心承诺是解决了还是拒绝了，我们只想知道它们是否完成了。

如果你想用，我已经把它发布给了 npm。如果你想看看我是如何为这个功能添加测试的，这里有 [github](https://github.com/ycmjason/asyncLimit) 链接。

你怎么想呢?你按照教程做了吗？

编辑:

*   移除了`asyncLimit`的`async`。感谢 [@benjaminblack](https://dev.to/benjaminblack)