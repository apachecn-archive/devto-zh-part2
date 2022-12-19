# 如何在 Python 中使用队列

> 原文：<https://dev.to/hasansajedi/how-to-use-queue-in-python-15mp>

本文将帮助您理解队列数据结构以及如何实现它。

队列是一个简单的数据结构概念，可以很容易地应用到我们的日常生活中，比如你在星巴克排队买咖啡。让我们根据这个例子做一些观察:

[![Queue](../Images/10f8348cd7215d0eabb4b64c33d4b30a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--L_PcpqSM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0x1l1l0t3qxuv1hn37wm.png)

## 现在，让我们以编程的方式来看以上几点:

*   队列从两端开放，意味着元素从后面添加，从前面移除
*   首先添加的元素首先被移除(先进先出- FIFO)。如果所有元素都被移除，那么队列是空的，如果试图从空队列中移除元素，则会抛出警告或错误消息。
*   如果队列已满，并且您向队列中添加了更多元素，则必须抛出警告或错误消息。

### 算法

1.  声明一个列表和一个整数 MaxSize，表示队列的虚拟最大大小
2.  头部和尾部最初设置为 0
3.  尺寸方法
    *   计算队列中元素的数量-> Size = Tail - Head
4.  重置方法:
    *   将尾部和头部重置为 0
    *   创建新队列(将队列初始化为新列表)
5.  入队操作:
    *   检查大小是否小于 MaxSize:
        *   如果是，将数据添加到队列中，然后将 Tail 加 1
        *   如果没有，打印队列已满消息
6.  出列操作:
    *   检查大小是否大于 0:
        *   如果是，从列表中弹出第一个元素，并将 Head 增加 1
        *   如果没有:
            *   呼叫重置方法
            *   打印队列空消息

```
class Queue:

    #Constructor
    def __init__(self):
        self.queue = list()
        self.maxSize = 8
        self.head = 0
        self.tail = 0

    #Adding elements
    def enqueue(self,data):
        #Checking if the queue is full
        if self.size() >= self.maxSize:
            return ("Queue Full")
        self.queue.append(data)
        self.tail += 1
        return True     

    #Deleting elements 
    def dequeue(self):
        #Checking if the queue is empty
        if self.size() <= 0:
            self.resetQueue()
            return ("Queue Empty") 
        data = self.queue[self.head]
        self.head+=1
        return data

    #Calculate size
    def size(self):
        return self.tail - self.head

    #Reset queue
    def resetQueue(self):
        self.tail = 0
        self.head = 0
        self.queue = list()

q = Queue()
print(q.enqueue(1))#prints True print(q.enqueue(2))#prints True print(q.enqueue(3))#prints True print(q.enqueue(4))#prints True print(q.enqueue(5))#prints True print(q.enqueue(6))#prints True print(q.enqueue(7))#prints True print(q.enqueue(8))#prints True print(q.enqueue(9))#prints Queue Full! print(q.size())#prints 8 print(q.dequeue())#prints 8 print(q.dequeue())#prints 7 print(q.dequeue())#prints 6 print(q.dequeue())#prints 5 print(q.dequeue())#prints 4 print(q.dequeue())#prints 3 print(q.dequeue())#prints 2 print(q.dequeue())#prints 1 print(q.dequeue())#prints Queue Empty
#Queue is reset here print(q.enqueue(1))#prints True print(q.enqueue(2))#prints True print(q.enqueue(3))#prints True print(q.enqueue(4))#prints True 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:元素 9 没有被添加到队列中，因此队列的大小仍然是 8