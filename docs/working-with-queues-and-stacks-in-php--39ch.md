# 在 PHP 中使用队列和堆栈

> 原文：<https://dev.to/rafaacioly/working-with-queues-and-stacks-in-php--39ch>

这篇帖子的内容灵感来自于 [Vaidehi Joshi](https://dev.to/vaidehijoshi) 的帖子《排队还是不排队》以及来自于基础 CS 系列视频[堆栈和队列](https://dev.to/vaidehijoshi/stacks-and-queues--basecs-video-series--20oj)

[![vaidehijoshi image](img/c1af460640a8ad9ea29e58d942edc772.png)](/vaidehijoshi) [## 排队还是不排队

### vaide hi Joshi Feb 14 ' 179 分钟阅读

#backgroundjobs #queues #computerscience #datastructures](/vaidehijoshi/to-queue-or-not-to-queue)

在 Vaidehi 的文章中，她解释了队列是如何工作的，并展示了一些“函数/方法”来使一些东西入队和出队，这是一个简单而直接的例子来展示它在 PHP 中是如何工作的(我假设你已经看过 CS 视频)。

PHP 附带了一个名为`SplQueue`的标准库(Spl 代表标准 PHP 库)

> SplQueue 继承自`SplDoublyLinkedList`。所以，`SplQueue`的对象支持方法`push()`和`pop()`。但是请注意，如果你在一个`SplQueue`对象上使用`push()`和`pop()`方法，**的行为就像一个堆栈而不是一个队列**
> 
> *   Manu Manjunath [(原解释来自 PHP 官方文档)](http://php.net/manual/pt_BR/class.splqueue.php#114336)

长话短说，`SplDoublyLinkedList`意味着同一个库同时为`queue`和`stacks`工作。

# 书库

如上所述，如果你在一个`SplQueue`对象上使用`push()`和`pop()`，行为将是一个堆栈而不是一个队列，参见:

```
$stack = new SplQueue();
$stack->push(1);
$stack->push(2);
$stack->push(3);
$stack->pop(); // remove the last one

print_r($stack);
/*
OUTPUT

SplQueue Object
(
    [flags:SplDoublyLinkedList:private] => 4
    [dllist:SplDoublyLinkedList:private] => Array
        (
            [0] => 1
            [1] => 2
        )
)

*/ 
```

Enter fullscreen mode Exit fullscreen mode

请注意，3 被弹出，而*不是* 1。

# 队列

使用`SplQueue`作为队列的方法有:`enqueue()`和`dequeue()`，参见:

```
$queue = new SplQueue();
$queue->enqueue('A');
$queue->enqueue('B');
$queue->enqueue('C');
$queue->dequeue(); // remove the first one

print_r($q);
/*
OUTPUT

SplQueue Object
(
    [flags:SplDoublyLinkedList:private] => 4
    [dllist:SplDoublyLinkedList:private] => Array
        (
            [0] => B
            [1] => C
        )
)

*/ 
```

Enter fullscreen mode Exit fullscreen mode

# `Queue`和`Stack`助手方法:

```
$queue->enqueue('dev'); // or $stack->push('dev');
$queue->enqueue('.to'); // or $stack->push('.to');

$queue->count(); // (int) 2
$queue->isEmpty(); // false

$queue->valid(); // false because right now the pointer is in the last position

// move the cursor to the first position, if you run valid() again after this it will be true
$queue->rewind();

$queue->current(); // "dev", current() just work if you rewind() before call it

$queue->unshift(3); // add the value to the beginning
$queue->current(); // "dev", because we need to move the cursor back to the first position
$queue->rewind();
$queue->current(); // 3

$queue->add(2, 'random-text'); // Add/insert a new value at the specified index

print_r($queue); // Show only 3, "dev" and "to" because we don't rewind() yet.

$queue->rewind(); // move the cursor back to the first position

print_r($queue);
/* 
OUTPUT

SplQueue Object
(
    [flags:SplDoublyLinkedList:private] => 4
    [dllist:SplDoublyLinkedList:private] => Array
        (
            [0] => 3
            [1] => dev
            [2] => random-text
            [3] => .to
        )
)

*/ 
```

Enter fullscreen mode Exit fullscreen mode

## 遍历队列和堆栈:

```
$queue = new SplQueue();
$queue->enqueue('dev');
$queue->enqueue('.to');
$queue->unshift('https://');
$queue->rewind(); // always rewind the queue/stack, so PHP can start from the beginning.

while($queue->valid()){
    echo $queue->current()."\n"; // Show the first one
    $queue->next(); // move the cursor to the next element
}

/* 
OUTPUT

https://
dev
.to

*/ 
```

Enter fullscreen mode Exit fullscreen mode

有很多方法你可以使用，我不能在这里全部展示，你可以在关于`SplQueue` 的[官方文档中看到每一种方法](http://php.net/manual/pt_BR/class.splqueue.php)

那么，你现在在用什么编程语言呢？这有内置队列和堆栈吗？如果有，怎么用？如果不是，你是怎么做的？写一篇文章，向我们展示如何用你最喜欢的编程语言处理队列和堆栈！😄

-
感谢你的朗读，我正在学习英语，如果有任何拼写错误，请告诉我。