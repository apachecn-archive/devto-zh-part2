# 队列数据结构

> 原文：<https://dev.to/mkhy19/queue---data-structure-2n8b>

## 队列是类似数组和堆栈的数据结构。但是在这里，添加到队列中的第一个元素将是从队列中移除的第一个元素。所以队列称为先进先出(FIFO)或先来先服务

[![Alt Stack](img/4b45dafe6ee88f8626d81696ef914961.png "queue")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Dr1pYG2r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/mohamedkhaledyousef/Crash-courses/blob/master/img/queue.jpeg%3Fraw%3Dtrue)

## 我们可以在队列中:

**Enqueue(Key):将关键字添加到集合**
**Key Dequeue():移除并返回最近最少添加的关键字**
**Boolean Empty():表示，，是否有元素？**

## 队列实现:

我们可以用几种方式实现队列(链表、数组、双栈)。

## 用链表实现队列:

### 有两件事我们必须要知道:

**第一个**:当我们排队的时候，我们要推到链表的后面
**第二个**:为了确保队列列表是空的，我们要确保头部是否为空

## 这里的代码使用链表: