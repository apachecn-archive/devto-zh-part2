# Python 中的多重处理与多线程:你需要知道的。

> 原文：<https://dev.to/timber/multiprocessing-vs-multithreading-in-python-what-you-need-to-know-3hfj>

***TLDR:*** 如果你不想理解幕后的解释，这里有你一直在等待的:如果你的程序是网络绑定的，你可以使用`threading`，如果是 CPU 绑定的，你可以使用`multiprocessing`。

我们创建本指南是因为当我们寻找线程和多处理之间的差异时，我们发现那里的信息很难理解。他们做得太深入了，没有真正触及到有助于我们决定使用什么和如何实现它的信息的实质。

## 什么是线程？你为什么想要它？

从本质上来说，Python 是一种线性语言，但是当您需要更多的处理能力时，线程模块就派上了用场。虽然 Python 中的线程不能用于并行 CPU 计算，但它非常适合 web 抓取等 I/O 操作，因为处理器处于空闲状态，等待数据。

线程化改变了游戏规则，因为许多与网络/数据 I/O 相关的脚本花费大部分时间等待来自远程数据源的数据。因为下载可能不被链接(即，抓取单独的网站)，所以处理器可以并行地从不同的数据源下载，并在最后组合结果。对于 CPU 密集型进程，使用线程模块没有什么好处。

[![threadingSameDataspace](img/eeea62bf45ea70a2764118593360ef90.png)T2】](//images.ctfassets.net/h6vh38q7qvzk/6RnMqNoKacW2OAAOqw0QW4/a390ea001017ee3492c1c814fa0a7659/threadingSameDataspace.jpeg)

幸运的是，标准库中包含了线程:

```
import threading
from queue import Queue
import time 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用`target`作为可调用对象，`args`向函数传递参数，`start`启动线程。

```
def testThread(num):
    print num

if __name__ == '__main__':
    for i in range(5):
        t = threading.Thread(target=testThread, arg=(i,))
        t.start() 
```

Enter fullscreen mode Exit fullscreen mode

如果你以前从未见过`if __name__ == '__main__':`，它基本上是一种确保嵌套在其中的代码只有在脚本直接运行(而不是导入)时才会运行的方法。

### 锁

您通常希望您的线程能够使用或修改线程间的公共变量，但是要做到这一点，您必须使用一种叫做`lock`的东西。每当一个函数想要修改一个变量时，它就锁定这个变量。当另一个函数想要使用一个变量时，它必须等到该变量被解锁。

[![lockExplanation](img/1dd6ef98856f7b964754cbc243c13063.png)T2】](//images.ctfassets.net/h6vh38q7qvzk/3PLJjBuuYMuuc6UUy8U2qW/b4a0d411cdc1c31c5841230885a3b055/lockExplanation.jpeg)

想象两个函数都用 1 迭代一个变量。锁允许您确保一个函数可以访问变量，执行计算，并在另一个函数可以访问同一个变量之前写回该变量。

当使用线程模块时，这也可能在打印时发生，因为文本可能会变得混乱(并导致数据损坏)。您可以使用打印锁来确保一次只有一个线程可以打印。

```
print_lock = threading.Lock()

def threadTest():
    # when this exits, the print_lock is released
    with print_lock:
        print(worker)

def threader():
  while True:
    # get the job from the front of the queue
    threadTest(q.get())
    q.task_done()

q = Queue()
for x in range(5):
    thread = threading.Thread(target = threader)
    # this ensures the thread will die when the main thread dies
    # can set t.daemon to False if you want it to keep running
    t.daemon = True
    t.start()

for job in range(10):
    q.put(job) 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们有 10 个想要完成的工作和 5 个将要完成工作的工人。

### 多线程并不总是完美的解决方案

我发现许多向导倾向于跳过使用他们刚刚试图教你的工具的负面影响。理解使用所有这些工具的利弊是很重要的。例如:

1.  管理线程会带来开销，所以您不希望将它用于基本任务(如示例)
2.  增加了程序的复杂性，这使得调试更加困难

## 什么是多重处理？它与线程有什么不同？

如果没有多重处理，Python 程序会因为`GIL`(全局解释器锁)而难以最大化系统规格。Python 的设计没有考虑到个人计算机可能有多个内核(向您展示该语言有多古老)，因此 GIL 是必要的，因为 Python 不是线程安全的，并且在访问 Python 对象时有一个全局强制锁。尽管并不完美，但这是一种非常有效的内存管理机制。*我们能做什么？*

多重处理允许你创建可以并行运行的程序(绕过 GIL ),并使用整个 CPU 内核。尽管它与线程库有着本质的不同，但语法非常相似。多重处理库为每个进程提供了自己的 Python 解释器和 GIL。

因此，与线程相关的常见问题(如数据损坏和死锁)不再是问题。由于进程不共享内存，它们不能同时修改同一个内存。

### 我们开始吧:

```
import multiprocessing
def spawn():
  print('test!')

if __name__ == '__main__':
  for i in range(5):
    p = multiprocessing.Process(target=spawn)
    p.start() 
```

Enter fullscreen mode Exit fullscreen mode

如果您有一个共享数据库，您需要确保在开始新的进程之前，您正在等待相关进程完成。

```
for i in range(5):
  p = multiprocessing.Process(target=spawn)
  p.start()
  p.join() # this line allows you to wait for processes 
```

Enter fullscreen mode Exit fullscreen mode

如果你想把参数传递给你的进程，你可以用 args
来完成

```
import multiprocessing
def spawn(num):
  print(num)

if __name__ == '__main__':
  for i in range(25):
    ## right here
    p = multiprocessing.Process(target=spawn, args=(i,))
    p.start() 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个简单的例子，因为正如你注意到的，数字并没有按照你期望的顺序出现(没有`p.join()`)。

和线程一样，多重处理也有缺点...你必须选择你的毒药:

1.  数据在进程间来回移动会产生 I/O 开销
2.  整个内存被复制到每个子进程中，这对更重要的程序来说是一个很大的开销

## 应该用什么？

如果您的代码有大量的 I/O 或网络使用:

*   多线程是您的最佳选择，因为它的开销很低

如果你有图形用户界面

*   多线程，这样你的 UI 线程就不会被锁定

如果您的代码受 CPU 限制:

*   你应该使用多重处理(如果你的机器有多个核心)

免责声明:我们是一家伐木公司。如果你试用我们的产品，我们会很高兴的(真的很棒！)，但我们要宣传的也就这些了。

[![](img/aa5c715399ff218855ddd92755157269.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---Pfx3BIg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/h6vh38q7qvzk/5BUP5dDcrKae4yyaoy8ocE/ba33ae45edec6325109f05a44407a2e2/footer.png)