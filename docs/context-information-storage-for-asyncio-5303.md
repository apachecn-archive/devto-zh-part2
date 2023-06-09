# asyncio 的上下文信息存储

> 原文：<https://dev.to/sqreenio/context-information-storage-for-asyncio-5303>

在 [Sqreen](https://www.sqreen.io/) ，我们正在构建一个基于动态工具的代理。它从应用程序内部检测安全事件(注入、跨站点脚本等)。)并允许用户配置操作(阻止攻击、记录堆栈跟踪等。)而不需要修改代码。Python 中动态插装背后的机制在[之前的博客文章](https://blog.sqreen.io/dynamic-instrumentation-agent-for-python/)中有所描述。动态插装也用于应用性能管理(APM)解决方案，如 [Datadog](https://www.datadoghq.com/) 、 [Instana](https://www.instana.com/) 和 [New Relic](https://newrelic.com/) 。

检测代码允许我们在调用有潜在危险的函数之前执行回调。例如，为了[防止 SQL 注入](https://blog.sqreen.io/block-sql-injections-not-customers/)，我们用安全层
透明地包装方法 Cursor.execute

```
def sqreen_execute(self, sql_stmt, *sql_params):
    # Before executing the SQL statement, check it was not built with
    # malicious, unescaped request parameters in it.
    if has_malicious_param(sql_stmt, request.params):
        # If there is, this is an SQL injection. Abort!
        raise SQLinjection(remote_addr=request.remote_addr)
    else:
        # If not, we can safely call the original method.
        return self.execute(sql_stmt, *sql_params) 
```

Enter fullscreen mode Exit fullscreen mode

让我们假设被检测的代码包含一个易受攻击的模式，如:

```
@app.route('/posts')
def posts(request):
    sql_stmt = 'SELECT * FROM posts WHERE id=%s' % request.params['id']

    # With dynamic instrumentation, sqreen_execute is transparently called
    # instead of cursor.execute.
    posts = cursor.execute(sql_stmt)

    return posts_template.render(posts) 
```

Enter fullscreen mode Exit fullscreen mode

那么名义上的要求/帖子/？id=42 将被执行(虽然未转义，请求参数不是恶意的)但是恶意的请求/posts/？id=42 或者 1=1 不会。这样，我们就能够保护应用程序而不破坏它！

## 存储上下文信息

正如我们在上面看到的，函数 sqreen_execute 需要知道当前的请求来测试 SQL 语句的安全性。它怎么能得到它呢？

因为我们的函数透明地替换了 Cursor.execute，所以它需要有相同的签名，因此我们不能将请求作为参数传递给 sqreen_execute。一些 web 框架提供了获取当前请求的功能，但不是全部，我们努力寻找一个通用的解决方案。

我们可以做的是插入一个中间件(或者使用框架的请求处理机制)来将当前请求存储在一个全局变量中:

```
CURRENT_REQUEST = None

def set_request(request):
    global CURRENT_REQUEST
    current_request = request

def get_request():
    return CURRENT_REQUEST 
```

Enter fullscreen mode Exit fullscreen mode

但是有一个问题:出于明显的性能原因，web 框架能够同时处理几个请求。所以上面的模式不起作用:我们可能会收到第一个请求 request_1(并将其存储在 CURRENT_REQUEST 中)，在服务它之前会收到第二个请求 request_2(在 CURRENT_REQUEST 中覆盖 request_1)。当我们在 request_1 中寻找 SQL 注入时，我们会用 request_2 把它弄乱！因此，我们需要一个更强大、并发安全的机制来存储当前请求。

### 线程-本地存储

为了解决这个问题，我们需要知道并发是如何实现的。大多数 Python web 框架都使用线程:特别是最受欢迎的 [Django](https://www.djangoproject.com/) 、 [Flask](http://flask.pocoo.org/) 和 [Pyramid](https://trypyramid.com/) 就是这种情况。他们实现了与 web 服务器的通用通信协议，称为 WSGI (Web 服务器网关接口)，最初在 [PEP 333](https://www.python.org/dev/peps/pep-0333/) 中有所描述。

WSGI 服务器也使用线程和进程来产生几个应用程序实例。多重处理在这里不是问题，因为每个进程将处理自己的 CURRENT_REQUEST 副本。因此，我们必须找到一个解决方案，让服务线程存储它当前正在处理的请求，而不影响其他线程。

Python 为此提供了一个解决方案。标准库中的函数 threading.local [返回一个名称空间对象，其值是线程特定的。这允许我们实现线程安全的请求存储，如下所示:](https://docs.python.org/3/library/threading.html#threading.local) 

```
import threading

RUNTIME_STORAGE = threading.local()
RUNTIME_STORAGE.request = None

def set_request(request):
    RUNTIME_STORAGE.request = request

def get_request():
    return RUNTIME_STORAGE.request 
```

Enter fullscreen mode Exit fullscreen mode

### asyncio 呢？

在 Python 3.4 中，引入了一个新的并发模型: [asyncio](https://docs.python.org/3/library/asyncio.html) 。它为单线程、异步编程提供了基础设施，包括:

*   用 async def 定义的协同程序函数，可以使用 await 关键字和另一个协同程序暂停其执行，并在另一个协同程序完成后继续执行。
*   调度和执行协程的事件循环。

下面是一个异步代码的例子。

```
import asyncio

async def compute(x, y):
    print("Compute %s + %s ..." % (x, y))
    await asyncio.sleep(1.0)
    return x + y

async def print_sum(x, y):
    result = await compute(x, y)
    print("%s + %s = %s" % (x, y, result))

loop = asyncio.get_event_loop()
loop.run_until_complete(print_sum(1, 2))
loop.close() 
```

Enter fullscreen mode Exit fullscreen mode

有两个协程函数，print_sum 和 compute。在执行时

*   事件循环进入 print_sum 并立即移交给 compute。
*   compute 打印计算结果并移交给 asyncio.sleep。
*   下一秒什么都不做。如果在事件循环中安排了其他任务，它们可以同时执行，这是阻塞函数 time.sleep 所做不到的。
*   计算恢复并完成。
*   print_sum 恢复并完成。

当涉及 io 时，asyncio 是一个很好的并发模型:当正在执行的代码在等待答案(例如，DB 结果)时被阻塞，程序可以切换到其他任务，稍后再回来。与线程相比，它的系统开销更少，并且在涉及慢速 IO 操作时通常更快。

这使得 asyncio 非常适合网络运营，尽管相对年轻，围绕它已经开发了几个 web 框架。其中，我们最近在代理中加入了对 [aiohttp](https://aiohttp.readthedocs.io/) 的支持。这是一个非常有趣且具有挑战性的任务，因为到目前为止我们还没有对 aiohttp 的支持，我们遇到的一个重要问题是请求存储机制。

下面是可能发生的情况:我们收到第一个请求 request_1，并开始在协程中处理它。在某个时刻，协程被挂起，事件循环移交给另一个处理 request_2 的协程。重要的一点是，这两个协程是在*同一个线程*中执行的，所以 threading.local 包含两者相同的数据。当第一个协程恢复时，RUNTIME_STORAGE.request 被设置为 request_2:这正是我们想要阻止的。

## 第一次尝试:还是用任务吧！

我们需要的是一种类似 threading.local 的机制，它与 asyncio 一起工作，即让我们存储上下文变量并跟踪每个异步执行上下文的值。

不幸的是，Python 中目前没有内置的机制来处理这个问题。已经有不同的提议在 Python 的未来版本中提供一个通用的解决方案( [PEP 550](https://www.python.org/dev/peps/pep-0550/) ， [PEP 567](https://www.python.org/dev/peps/pep-0567/) )，但是与此同时，我们必须自己设计一个解决方案。

让我们更深入地研究一下 asyncio 的内部。被调度执行的协程被包装到 asyncio 中。任务[对象](https://docs.python.org/3/library/asyncio-task.html#task)，负责在事件循环中执行协程对象。

[![https://docs.python.org/3/_images/tulip_coro.png](img/34511f9626bcad516f6cf0c2842d1fad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pAwvGajS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/blog.sqreen.io/wp-content/uploads/2018/02/https-docs-python-org-3-_images-tulip_coro-png.png%3Fw%3D840%26ssl%3D1)

*示例的顺序图*

还有一个函数 asyncio。Task.current_task 返回当前运行的任务。Mmh…我们可以用它来映射当前的请求到处理它的任务。类似这样的事情可能会起作用:

```
import asyncio

TASK_REQUESTS = {}

def set_request(request):
    task = asyncio.Task.current_task()
    TASK_REQUESTS[id(task)] = request

def get_request():
    task = asyncio.Task.current_task()
    return TASK_REQUESTS.get(id(task)) 
```

Enter fullscreen mode Exit fullscreen mode

对于这种实现，我们还需要一种机制来确保任务完成后删除请求，以避免累积旧请求并导致内存泄漏。避免处理它的一种方法是将请求*存储在任务对象*中，作为一个额外的属性:

```
def set_request(request):
     task = asyncio.Task.current_task()
     setattr(task, 'current_request', request)

 def get_request():
     task = asyncio.Task.current_task()
     return getattr(task, 'current_request', None) 
```

Enter fullscreen mode Exit fullscreen mode

那么，这有用吗？我们来测试一下！

```
import random

class Request:
    # Dummy request object, for the sake of testing.
    pass

async def handle_request(request):
    set_request(request)
    await asyncio.sleep(random.uniform(0, 2))
    await check_request(request)

async def check_request(request):
    # Check that the stored request corresponds to the current request. If not,
    # an AssertionError is raised and the test is interrupted with an error.
    assert get_request() is request, "wrong request"

NUM_REQUESTS = 1000

loop = asyncio.get_event_loop()
coros = [handle_request(Request()) for _ in range(NUM_REQUESTS)]
loop.run_until_complete(asyncio.gather(*coros))
loop.close()
print("Success!") 
```

Enter fullscreen mode Exit fullscreen mode

这个测试模拟了一千个并发请求。每一个都在一个专用的协程 handle_request 中处理。这个函数存储请求，然后暂停一段随机的时间(这模拟了一个异步操作，比如一个数据库访问，并确保协程执行流是交错的)。恢复时，调用嵌套的协同例程 check_request，确保 get_request 返回正确的请求。否则，测试会因错误而中断。

这里有一些好消息:基于任务的请求存储使测试运行顺利。线程本地存储也失败了，这是意料之中的，但表明测试是相关的。那么，我们解决问题了吗？

## 任务间的上下文继承

让我们尝试一些更扭曲的东西:

```
async def handle_request(request):
    set_request(request)
    await asyncio.gather(
        asyncio.sleep(random.uniform(0, 2)),
        check_request(request),
    ) 
```

Enter fullscreen mode Exit fullscreen mode

这个版本的 handle_request 不是顺序执行 asyncio.sleep 和 check_request，而是并发运行它们。这应该没什么大不了的:代码更具并发性，但不会影响请求处理。特别是，对于每个请求，check_request 仍然在 set_request 之后调用*。*

然而，这个新的测试失败了！当我们引入 asyncio.gather 时，出现了一些问题，但是什么问题呢？

嗯，还记得被调度的协程被打包成任务吗？这正是这里所发生的:asyncio.gather 围绕参数 asyncio.sleep()和 check_request()创建任务，这些任务由事件循环执行。

```
async def handle_request(request):
    set_request(request)                              # Running in task 1.
    await asyncio.gather(
        asyncio.sleep(random.uniform(0, 2)),          # Create child task 2.
        check_request(request),                       # Create child task 3.
    )

async def check_request(request):
    assert get_request() is request, "wrong request"  # Running in task 3. 
```

Enter fullscreen mode Exit fullscreen mode

结果是 set_request 和 get_request 在不同的任务中被调用，使得测试失败。这不是因为请求交错，因为我们可以通过将 NUM_REQUESTS 设置为 1 来检查:测试一直失败。

事实上，当从子任务调用 get_request 时，我们需要一种机制来从父任务检索请求，如果它没有在子任务中定义的话。但是 asyncio 不允许我们访问父任务，所以这是行不通的。

另一方面，asyncio 让我们做的事情是替换被调用来创建新任务的函数，也称为*任务工厂*。这个函数在父任务的上下文中被调用，并返回一个新的子任务。好吧，让我们用它来装饰当前请求的子任务！

下面是一个“请求感知”任务工厂的样子:

```
def request_task_factory(loop, coro):
    # This is the default way to create a child task.
    child_task = asyncio.tasks.Task(coro, loop=loop)

    # Retrieve the request from the parent task...
    parent_task = asyncio.Task.current_task(loop=loop)
    current_request = getattr(parent_task, 'current_request', None)

    # ...and store it in the child task too.
    setattr(child_task, 'current_request', current_request)

    return child_task 
```

Enter fullscreen mode Exit fullscreen mode

要安装任务工厂，我们还需要在运行循环之前调用 loop . set _ task _ factory(request _ task _ factory)。这是我们代码的最终版本:

```
import asyncio
import random

class Request:
    pass

def set_request(request):
    task = asyncio.Task.current_task()
    setattr(task, 'current_request', request)

def get_request():
    task = asyncio.Task.current_task()
    return getattr(task, 'current_request', None)

def request_task_factory(loop, coro):
    child_task = asyncio.tasks.Task(coro, loop=loop)
    parent_task = asyncio.Task.current_task(loop=loop)
    current_request = getattr(parent_task, 'current_request', None)
    setattr(child_task, 'current_request', current_request)
    return child_task

async def handle_request(request):
    set_request(request)
    await asyncio.gather(
        asyncio.sleep(random.uniform(0, 2)),
        check_request(request),
    )

async def check_request(request):
    assert get_request() is request

NUM_REQUESTS = 1000

loop = asyncio.get_event_loop()
loop.set_task_factory(request_task_factory)
coros = [handle_request(Request()) for _ in range(NUM_REQUESTS)]
loop.run_until_complete(asyncio.gather(*coros))
loop.close() 
```

Enter fullscreen mode Exit fullscreen mode

它的工作完美无缺！

## 而现在呢？

我们现在有了解决代理中请求存储问题的基础。因为我们希望代理尽可能透明地工作，并且不需要用户修改代码，所以还有两个小问题需要解决:

*   我们希望自动建立任务工厂。这将通过动态仪器来完成。
*   但是如果自定义任务是由用户设置的，我们不想覆盖它。相反，我们将自己完成它。

先说第二个问题。我们可以定义一个通用函数 wrap_request_task_factory，它将任务工厂作为参数，并返回一个支持请求传播的变量。包装函数的代码确实和上面 request_task_factory 的代码很接近:

```
from functools import wraps

def wrap_request_task_factory(task_factory):

    @wraps(task_factory)
    def wrapped(loop, coro):
        child_task = task_factory(loop, coro)
        parent_task = asyncio.Task.current_task(loop=loop)
        current_request = getattr(parent_task, 'current_request', None)
        setattr(child_task, 'current_request', current_request)
        return child_task

    return wrapped 
```

Enter fullscreen mode Exit fullscreen mode

那么，request_task_factory 的定义可以简化为:

```
@wrap_request_task_factory
def request_task_factory(loop, coro):
    asyncio.Task.current_task(loop=loop) 
```

Enter fullscreen mode Exit fullscreen mode

是时候回到动态仪器了。通过挂钩导入系统，我们可以透明地用自定义类替换导入的类。因此，让我们定义一个函数 patch_loop_cls，它创建一个具有所需行为的自定义循环类:

```
def wrap_loop_cls(loop_cls):

    class SqreenLoop(loop_cls):

        def __init__(self, *args, **kwargs):
            super().__init__(*args, **kwargs)
            # We want to use request_task_factory to be the default task
            # factory.
            super().set_task_factory(request_task_factory)

        def set_task_factory(self, task_factory):
            # If the user sets up a custom task factory, let's wrap it with
            # request propagation.
            wrapped_task_factory = wrap_request_task_factory(task_factory)
            super().set_task_factory(wrapped_task_factory)

    return SqreenLoop 
```

Enter fullscreen mode Exit fullscreen mode

这个循环类透明地替换了基类。默认情况下，它使用正确的任务工厂，并允许用户在保留请求管理层的同时对其进行更改。

## 结束语

我们已经在一个名为 [AioContext](https://github.com/sqreen/AioContext) 的 Python 库中发布了大部分工作(不包括插装部分)。它带有通用的上下文对象，行为类似于字典。如果不再需要上下文，它还允许恢复原始任务工厂，并将上下文存储为任务工厂的额外属性，以避免扰乱 asyncio。任务类本身。文档可在[这里](https://aiocontext.readthedocs.io/)获得。

```
import asyncio
import aiocontext
import random

class Request:
    pass

CONTEXT = aiocontext.Context()

async def handle_request(request):
    CONTEXT['current_request'] = request
    await asyncio.gather(
        asyncio.sleep(random.uniform(0, 2)),
        check_request(request),
    )

async def check_request(request):
    assert CONTEXT['current_request'] is request

NUM_REQUESTS = 1000

loop = asyncio.get_event_loop()
aiocontext.wrap_task_factory(loop)
CONTEXT.attach(loop)
coros = [handle_request(Request()) for _ in range(NUM_REQUESTS)]
loop.run_until_complete(asyncio.gather(*coros))
loop.close() 
```

Enter fullscreen mode Exit fullscreen mode

这项工作受到了 Manual Miranda 的博客文章[从 Flask 到 aiohttp](https://medium.com/@SkyscannerEng/from-flask-to-aiohttp-22f1ddc5dd5e) 和图书馆 [aiotask-context](https://github.com/Skyscanner/aiotask-context) 的强烈启发。我们要感谢他的巨大贡献。

asyncio 的上下文信息存储这篇文章最早出现在 [Sqreen 博客|现代应用安全](https://blog.sqreen.io)上，作者是 Vivien Maisonneuve。