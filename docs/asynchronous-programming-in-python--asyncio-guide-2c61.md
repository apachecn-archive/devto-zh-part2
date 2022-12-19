# Python | Asyncio 中的异步编程(指南)

> 原文：<https://dev.to/djangostars/asynchronous-programming-in-python--asyncio-guide-2c61>

如果出于某种原因，您决定理解 Python 的异步部分，欢迎来到我们的“Asyncio How-to”。

注意:即使不知道异步范式的存在，您也可以成功地使用 Python。然而，如果你对事情如何在引擎盖下工作感兴趣，asyncio 绝对值得一查。

## 异步是怎么回事？

在经典的顺序编程中，你发送给解释器的所有指令都将被一个接一个地执行。很容易想象和预测这种代码的输出。但是…

假设您有一个从 3 个不同的服务器请求数据的脚本。有时，取决于谁知道什么，对那些服务器之一的请求可能花费意想不到的太多时间来执行。假设从第二台服务器获取数据需要 10 秒钟。当你在等待的时候，整个脚本实际上什么也没做。如果您可以编写一个脚本，不再等待第二个请求，而是简单地跳过它并开始执行第三个请求，然后返回到第二个请求，并从它离开的地方继续执行，会怎么样？就是这样。你可以通过切换任务来减少空闲时间。尽管如此，当你需要一个简单的脚本，很少或者没有 I/O 的时候，你不希望使用异步代码。

还有一点要提的是，所有的代码都是在单线程中运行的。因此，如果你期望程序的一部分将在后台执行，而你的程序将做别的事情，这不会发生。

## 入门

以下是 asyncio 主要概念的最基本定义:

*   **协程** —消耗数据但不生成数据的生成器。Python 2.5 引入了新的语法，使得向生成器发送值成为可能。我推荐查看大卫·比兹利[“关于协程和并发的奇妙课程”](http://www.dabeaz.com/coroutines/Coroutines.pdf)以获得关于协程的详细描述。

*   **任务** —协程的调度程序。如果你检查下面的源代码，你会看到它只是说`event_loop`尽快运行它的`_step`，而`_step`只是调用协程的下一步。

```
class Task(futures.Future):
    def __init__(self, coro, loop=None):
        super().__init__(loop=loop)
        ...
        self._loop.call_soon(self._step)

    def _step(self):
            ...
        try:
            ...
            result = next(self._coro)
        except StopIteration as exc:
            self.set_result(exc.value)
        except BaseException as exc:
            self.set_exception(exc)
            raise
        else:
            ...
            self._loop.call_soon(self._step) 
```

Enter fullscreen mode Exit fullscreen mode

*   **事件循环** —把它想象成 asyncio 中的中央执行器。

现在让我们看看所有这些是如何协同工作的。正如我已经提到的，异步代码运行在单线程中:

[![](../Images/6988e0ff8039e317dab646e3fba54072.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DWMjd7tI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://djangostars.com/blog/content/images/2017/04/new_v1_async.jpg)

正如你从图表中看到的:

*   事件循环正在线程中运行
*   它从队列中获取任务
*   每个任务调用协程的下一步
*   如果一个协同程序调用另一个协同程序(`await <coroutine_name>`)，当前协同程序被挂起，并发生上下文切换。保存当前协程的上下文(变量、状态),并加载被调用协程的上下文
*   如果协程遇到阻塞代码(I/O、sleep)，当前协程将被挂起，控制权将被传递回事件循环
*   事件循环从队列 2 中获取下一个任务，...n
*   然后，事件循环从离开的地方返回到任务 1

## 异步 vs 同步代码

让我们试着证明异步方法确实有效。我将比较两个几乎相同的脚本，除了`sleep`方法。在第一个示例中，我将使用标准的`time.sleep`，在第二个示例中，这里使用`asyncio.sleep`
睡眠，因为这是展示主要思想的最简单方式，asyncio 如何处理 I/O

这里我们在异步代码中使用同步睡眠:

```
import asyncio
import time
from datetime import datetime

async def custom_sleep():
    print('SLEEP', datetime.now())
    time.sleep(1)

async def factorial(name, number):
    f = 1
    for i in range(2, number+1):
        print('Task {}: Compute factorial({})'.format(name, i))
        await custom_sleep()
        f *= i
    print('Task {}: factorial({}) is {}\n'.format(name, number, f))

start = time.time()
loop = asyncio.get_event_loop()

tasks = [
    asyncio.ensure_future(factorial("A", 3)),
    asyncio.ensure_future(factorial("B", 4)),
]
loop.run_until_complete(asyncio.wait(tasks))
loop.close()

end = time.time()
print("Total time: {}".format(end - start)) 
```

Enter fullscreen mode Exit fullscreen mode

**输出:**

```
Task A: Compute factorial(2)
SLEEP 2017-04-06 13:39:56.207479
Task A: Compute factorial(3)
SLEEP 2017-04-06 13:39:57.210128
Task A: factorial(3) is 6

Task B: Compute factorial(2)
SLEEP 2017-04-06 13:39:58.210778
Task B: Compute factorial(3)
SLEEP 2017-04-06 13:39:59.212510
Task B: Compute factorial(4)
SLEEP 2017-04-06 13:40:00.217308
Task B: factorial(4) is 24

Total time: 5.016386032104492 
```

Enter fullscreen mode Exit fullscreen mode

现在同样的代码，但是使用了异步睡眠方法:

```
import asyncio
import time
from datetime import datetime

async def custom_sleep():
    print('SLEEP {}\n'.format(datetime.now()))
    await asyncio.sleep(1)

async def factorial(name, number):
    f = 1
    for i in range(2, number+1):
        print('Task {}: Compute factorial({})'.format(name, i))
        await custom_sleep()
        f *= i
    print('Task {}: factorial({}) is {}\n'.format(name, number, f))

start = time.time()
loop = asyncio.get_event_loop()

tasks = [
    asyncio.ensure_future(factorial("A", 3)),
    asyncio.ensure_future(factorial("B", 4)),
]
loop.run_until_complete(asyncio.wait(tasks))
loop.close()

end = time.time()
print("Total time: {}".format(end - start)) 
```

Enter fullscreen mode Exit fullscreen mode

**输出:**

```
Task A: Compute factorial(2)
SLEEP 2017-04-06 13:44:40.648665

Task B: Compute factorial(2)
SLEEP 2017-04-06 13:44:40.648859

Task A: Compute factorial(3)
SLEEP 2017-04-06 13:44:41.649564

Task B: Compute factorial(3)
SLEEP 2017-04-06 13:44:41.649943

Task A: factorial(3) is 6

Task B: Compute factorial(4)
SLEEP 2017-04-06 13:44:42.651755

Task B: factorial(4) is 24

Total time: 3.008226156234741 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，异步版本快了 2 秒。当使用异步睡眠时(每次我们调用`await asyncio.sleep(1)`，控制被传递回事件循环，运行队列中的另一个任务(任务 A 或任务 B)。

在标准睡眠的情况下——什么都不会发生，一个线程只是挂起。事实上，由于一个标准的睡眠，当前线程发布了一个 python 解释器，它可以与其他线程一起工作，如果它们存在的话，但这是另一个话题。

### 坚持异步编程的几个理由

像脸书这样的公司大量使用异步。脸书的 React Native 和 RocksDB 认为是异步的。比方说，你认为 Twitter 一天处理超过 50 亿次会话是可能的吗？那么，为什么不重构代码或改变方法，让软件工作得更快呢？

这篇文章是玛丽亚·亚基莫娃写的。这篇关于 python 中的[异步编程的文章最初发表在 Django Stars 博客上。您还可以访问我们的内容平台](https://djangostars.com/blog/asynchronous-programming-in-python-asyncio/)[产品部落](https://producttribe.com/)，它是由专业人士为那些参与产品开发和成长过程的人创建的。

我们随时欢迎您提出问题，分享您想阅读的话题！