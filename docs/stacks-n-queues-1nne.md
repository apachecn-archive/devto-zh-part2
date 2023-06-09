# 堆栈 n 队列

> 原文：<https://dev.to/ryanfarney3/stacks-n-queues-1nne>

嘿伙计们！本周，我想继续在 JavaScript 中介绍一些数据结构，因为我在上一篇文章中得到了一些很好的反馈。上周我们讨论了链表，你可以在这里找到。

我相信栈和队列是一些更简单的数据结构，所以也许我应该从这些开始。尽管如此，让我们定义它们，浏览一个创建它们的例子，并从表面上看一个实际案例。

### 队列

队列可以被认为是一个容器，数据从一端进入，从另一端出来。我想说这非常类似于午餐排队。你站在队伍中，当队伍向前移动时，你将呆在那个位置，直到你完成并离开队伍…他们说这是先进先出。进入队列的第一条记录也将是第一条输出的记录。在这里，没有跳过或切断线路的想法。因此，队列是这样工作的。您可以在前面添加记录，也可以从后面删除记录。

在 JS 中，当我们想要实现一个队列时，我们通常使用一个数组，我们可以限制它只能添加或删除元素。为什么我们有目的地限制这个数组，使它只能在线的前面添加一个元素，从后面删除一个元素？在实际设置中，我会说它主要归结为编写清晰的代码，以便另一个工程师不会误解您的算法，认为他们可以像处理任何其他数组一样处理它。但是，在面试中更常见的是排队问题。

在接近编写一些代码的时候，让我们考虑一下如何操作一个数组来添加第一个元素并删除最后一个元素。嗯，我们很幸运，因为有数组方法来处理那个…队列(我们都知道它要来了) [Array.unshift()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/unshift) 和 [Array.pop()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/pop) 。如果你不知道他们具体做什么，请参考链接的文档。但是，如果你想相信我，unshift 会在数组前面添加一个元素，pop 会删除最后一个元素。

让我们从头开始排队。

我们将从创建 out 队列并将其内部数据设置为数组开始。

```
 function Queue() {
  this.data = []
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将研究我们的添加功能。这里我们将简单地用. unshift.
在队列的开头添加一个元素

```
Queue.prototype.add = function(element) {
  this.data.unshift(element)
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们使用. pop.
从队列中删除最后一个元素

```
Queue.prototype.remove = function() {
  return this.data.pop()
} 
```

Enter fullscreen mode Exit fullscreen mode

### 书库

堆栈非常类似于队列。因此，上面的大部分内容都适用。您仍然在处理一个有序的记录列表，只是这次是 FILO 或“先进后出”。我会把它想象成一堆盘子。当你一个接一个的叠盘子时，第一个盘子会在叠盘子的底部。所以，如果你想再次得到第一个盘子，你必须一个接一个地把盘子从盘子堆的顶部移走，直到你再次回到第一个盘子。

同样，当我们实现自己的堆栈时，让我们考虑一下如何操作一个数组，使其看起来像上面描述的那样。肯定还会有更多的方法。我们使用 Array.push()向堆栈中添加一条记录，再次使用 Array.pop()移除顶部的记录，并返回顶部的记录而不弹出它，这样如何？我们需要创建自己的解决方案。

就像我们上面做的一样，我们将从定义我们的堆栈开始，因为它有一个空的数据数组。

```
function Stack() {
  this.data = []
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们希望能够将一个元素添加到堆栈的末尾，而不是开始，所以我们使用. push.

```
Stack.prototype.add = function(element) {
  return this.data.push(element)
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们删除一个元素时，我们仍然希望删除“顶部”或最后一个元素，所以我们可以使用。再次弹出！

```
Stack.prototype.remove = function() {
  return this.data.pop()
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，如果我们希望能够看到堆栈顶部的内容，我们可以查看堆栈。

```
Stack.prototype.peek = function() {
  return this.data[this.data.length - 1]
} 
```

Enter fullscreen mode Exit fullscreen mode

### 实际用途

看起来在面试中，你会经常遇到堆积如山和/或排起长队的情况。然而，在事件循环中，你会想到两者都被使用的地方。这绝对不是一个需要深入探讨的话题，但也是另一个常见的面试问题。事件循环包含调用堆栈、事件表和事件队列。我不久前写了一篇名为[“分解调用栈”](https://medium.com/@ryanfarney/breaking-down-the-call-stack-e68b5633fbad)的博客，以获得更多信息，但我也强烈推荐你查看[这篇](https://hackernoon.com/understanding-js-the-event-loop-959beae3ac40)文章，如果你好奇的话，可以了解更多关于事件循环的概要。

### 结论

Stacks n Queues 是两种非常简单且相似的数据结构，最大的区别在于数据输入的方式，这影响了数据最终是如何被移除的。