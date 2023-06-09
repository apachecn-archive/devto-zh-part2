# 发现异步挂钩

> 原文：<https://dev.to/captainsafia/discovering-async-hooks-1ohk>

所以有一天，我在浏览 Node 的文档。JS 当我不小心点击了导航栏中标题为[“Async Hooks”](https://nodejs.org/dist/latest-v9.x/docs/api/async_hooks.html)的东西。我很好奇。我快速浏览文档，看看是否能从粗略的浏览中得到什么，并在心里记下以便以后了解更多。

这就是我来这里的原因！我想我会深入这个异步挂钩业务，看看发生了什么。

事实证明，异步挂钩目前是节点中的一个实验性特性。这意味着它们的 API 接口可能会发生巨大变化，或者它们可能会从 Node 中完全删除。所以，是的，这是最前沿的东西。现在还不要相信你的产品代码！

该文档陈述了以下内容。

> `async_hooks`模块提供了一个 API 来注册跟踪 Node.js 应用程序内部创建的异步资源的生命周期的回调。

有意思！所以，如果我用简单的术语来表达上面的陈述。异步挂钩允许您挂钩异步资源中发生的事件。该文档描述了 HTTP 服务器资源或文件读取器资源的示例。例如，可能与异步 HTTP 服务器相关的事件是新连接。类似地，文件 I/O 资源可能发生多次的事件是对文件的写入。所以使用异步钩子，你可以做一些事情，比如每当有人连接到你的 HTTP 服务器时发送一个通知，或者每当有人写入一个文件时发送一封电子邮件。这基本上是一种在 Node 中插入异步事件生命周期的方法。

在这一点上，我很想知道`async_hooks`模块是如何工作的，所以我去了我信任的老朋友 NodeJS 代码库。在这里定义了`async_hooks`模块的核心逻辑[。如果您查看该文件的提交历史，您会注意到第一次提交是在 2017 年 5 月推出的。相对于你在节点代码库中看到的许多东西来说，这是相当新的。](https://github.com/nodejs/node/blob/bff5d5b8f0c462880ef63a396d8912d5188bbd31/lib/async_hooks.js)

用于`async_hooks`模块的[公共 API](https://github.com/nodejs/node/blob/bff5d5b8f0c462880ef63a396d8912d5188bbd31/lib/async_hooks.js#L205-L208) 由三个不同的函数组成，所以我想我应该开始深入研究它们。

```
module.exports = {
  // Public API
  createHook,
  executionAsyncId,
  triggerAsyncId,
  // Embedder API
  AsyncResource,
}; 
```

第一个函数`createHook`负责根据用户提供的参数创建一个新的异步对象。

```
function createHook(fns) {
  return new AsyncHook(fns);
} 
```

用户提供的参数是一组函数，`fns`，它们定义了在异步资源生命周期的不同阶段执行的回调。例如，当一个新的异步资源(比如一个 HTTP 服务器)将要被创建时，调用一个`init`回调；当一个新的异步事件(比如一个 HTTP 服务器的连接)发生时，调用一个`before`回调。

`executionAsyncId`似乎从关联数组的查找中返回一些数据。

```
function executionAsyncId() {
  return async_id_fields[kExecutionAsyncId];
} 
```

对文档的一些窥探表明，它从当前执行上下文返回了`asyncId`。所以基本上，这个值提供了异步资源当前位置的洞察力。例如，如果资源刚刚被初始化，或者如果资源刚刚发生了异步事件，那么 asyncId 将会不同。

还有另一个由公共 API 导出的与`asyncId` s 相关的函数。`triggerAsyncId`函数也从表查找中返回数据。

```
function triggerAsyncId() {
  return async_id_fields[kTriggerAsyncId];
} 
```

根据文档中对该函数的描述，我认为该函数返回调用当前正在执行的回调函数的 ID。基本上，它为您提供了一种确定子回调的“父”的简便方法。我猜您可能会使用`executionAsyncId`和`triggerAsyncId`作为调试工具，在给定时间跟踪应用程序中回调调用的状态。

这个`async_hooks`模块还有另一个维度。这是 C++的维度。像节点生态系统中的许多重要模块一样，有许多 C++代码在承担重任，创造奇迹。我将不再深入探讨`async_hooks`的 C++实现，但我将在另一篇博客文章中对此进行探讨。

敬请期待！