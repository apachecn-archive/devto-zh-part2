# 数据结构:堆栈和队列

> 原文：<https://dev.to/rinsama77/data-structure-stack-and-queue-4ecd>

堆栈和队列通常在程序运行期间用作程序员的工具，通常在任务完成后被删除。

## 堆栈

一个*栈*是一个遵循“后进先出”或 *LIFO* 模型的抽象数据结构。一些现实世界的例子是“点击返回”到上一个网页，和文本编辑器的撤销功能。

栈上有 3 个基本操作:
1。Push:在堆栈上插入一个数据项。
2。取出:从栈顶取出一个项目。
3。Peek:从堆栈顶部读取一个项目的值，而不移除它。
[![](../Images/e709c466cd2abdf23e9788d1b6ead22a.png)T6】](https://www.programiz.com/dsa/stack)

在编程中，你可以使用一个*数组*或者一个*链表*来实现一个栈。(下面是一个用 java 实现堆栈的例子)

```
class StackX {
    private int maxSize;
    private long[] stackArray;
    private int top;

    public StackX(int s) {                // constructor
        maxSize = s;                      // set the array size
        stackArray = new long[maxSize];   // create an array
        top = -1;                         // no items yet
    }

    public void push(long j) {        // put items on top of the stack
        stackArray[++top] = j;        // increment top when item inserted
    }
    public long pop() {               // take item from the top of the stack
        return stackArray[top--];     // access item, then decrement top
    }
    public long peek() {              // peek at the top of the stack
        return stackArray[top];
    }
    public boolean isEmpty() {        // true if the stack is empty
        return (top == -1);
    }
    public boolean isFull() {         // true if the stack is full
        return (top == maxSize -1);
    } 
```

#### 堆栈应用

*   **反转数据**；例如，反转字符串
*   **解析**:将数据分解成独立的片段，以便进一步处理；例如，检查分隔符匹配[，{，(，)，}，]。
    T3![](../Images/3cabfcff24d2a5206deeb9de549ba8dc.png)T5】

    > 工作原理:
    > 
    > *   从字符串中一次读取一个字符
    > *   如果你遇到一个*开始定界符* [，{，(，把它放到一个栈上
    > *   如果遇到一个*结束分隔符*，从栈顶弹出该项
    > *   如果它们不匹配(开始和结束分隔符)，就会出现错误。

    例如:a{b(c[d]e)f}h

| 线 | 堆 |
| --- | --- |
| a | 空的 |
| { | { |
| b | { |
| （ | {( |
| c | {( |
| [ | {([ |
| d | {([ |
| ] | {( |
| e | {( |
| ) | { |
| f | { |
| { | 空的 |
| h | 空的 |

*   **推迟**:数据的使用必须推迟一段时间。比如解析一个算术表达式；3+(5-9)*4

    > 我们将使用的符号:
    > 
    > *   前缀-> + a b
    > *   中缀-> a + b
    > *   后缀 a b +
    > 
    > +、-、*、/，这些是*运算符*。
    > 当我们把数字(1，2，3，...)的*操作数*。
    > 
    > 优先(优先级、等级)关系:
    > 
    > *   +、-具有相同的优先级
    > *   *、/具有相同的优先级，并且高于+、-

我觉得用视频来解释它的工作原理应该更容易，所以我们开始吧...
[https://www.youtube.com/embed/z3VsmufB_QI](https://www.youtube.com/embed/z3VsmufB_QI)
我们之所以要这么做，是因为计算机只能通过表达式前进或后退。

让我们看看如何将中缀表达式转换成后缀表达式..
[https://www.youtube.com/embed/vXPL6UavUeA](https://www.youtube.com/embed/vXPL6UavUeA)T2】

这是如何评价后缀表达式的视频..
[https://www.youtube.com/embed/QCnANUfgC-w](https://www.youtube.com/embed/QCnANUfgC-w)T2】

更详细的解释，点击。

*   **回溯**:用于很多搜索应用，八皇后问题等。寻路示例:[https://www.youtube.com/embed/iaBEKo5sM7w](https://www.youtube.com/embed/iaBEKo5sM7w)n 皇后问题示例:[https://www.youtube.com/embed/kX5frmc6B7c](https://www.youtube.com/embed/kX5frmc6B7c)

## 队列

*队列*是遵循“先进先出”或 *FIFO* 模型的抽象数据结构。一些真实世界的例子包括打印一个文件(队列中有一个文件)、进程调度器、等待队列。

对队列的基本操作:
1。enquee/Add/Put:在队列的*后面*或后面插入一个数据项。
2。dequee/Delete/Get:从队列的*前端*移除一个项目。
3。Peek:从队列的*前端*读取一个项目的值，而不移除它。

现在，我们来看看下面 java 中一个队列的实现！

```
class Queue {
    private int maxSize;
    private long[] queArray;
    private int front;
    private int back;
    private int nItems;

    public Queue(int s) {                    // Constructor
        maxSize = s;
        quaArray = new long[maxSize];
        front = 0;
        back = -1;
        nItems = 0;
    }

    public void insert(long j) {            // Put an item at the back of the queue
        if(back == maxSize -1) back = -1;   // Deal with wraparound
        queArray[++back] = j;               // Increment back and insert the item
        nItems++;                           // One more item
    }
    public long remove() {                  // Take item from the front of the queue
        long temp = queArray[front++];      // Get the item and increment front
        if(front == maxSize) front = 0;     // Deal with wraparound
        nItems--;                           // One less item
        return temp;
    }
    public long peekFront() {
        return queArray[front];
    } 
```

#### OS 中的队列

当我们有一个处理器，但有许多进程要执行。为了让进程看起来同时运行，我们必须将 CPU 时间分成几个时间段，并创建一个包含要执行的作业的队列。

#### 优先级队列

队列中的项目将按键值排序(区分优先级)。有两种类型的优先级队列。

1.  升序-优先级队列-具有最小键的项目具有最高优先级
2.  降序优先级队列——具有最大关键项目的项目具有最高优先级

###### 在优先级队列中插入一个项目:O(n)

我们不是在队列的后面插入，而是根据项目相对于其他项目的值来插入。

[![](../Images/9003644e0832dc3b30503b66a4edebc8.png)T2】](https://www.javamadesoeasy.com/2015/01/priority-queues.html)

###### #移除/删除项目:O(n)

就像正常的队列一样，我们删除队列中最前面的一个。

[![](../Images/36e5a23c9fd54f657ce73996aa1c83bb.png)T2】](https://www.javamadesoeasy.com/2015/01/priority-queues.html)

* * *

本帖到此结束:)下期见~