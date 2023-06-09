# 如何:对 Masonite 队列使用 RabbitMQ

> 原文：<https://dev.to/masonite/how-to-use-rabbitmq-with-masonite-20-queues-4105>

# 简介

此功能需要[mason ite](https://github.com/MasoniteFramework/masonite)T0】

队列是保持你的应用程序快速和简洁的一个极好的方法。任何不需要返回值和/或时间密集型的东西都可以放在队列中，以后再运行。使用队列可以减少加载页面的时间，并改善用户体验。在许多情况下，使用队列是有益的。这些包括但不限于:

*   当用户注册时，更新数据库中的任意值(如帐户登录信息)
*   发送欢迎或促销电子邮件
*   触发 API 调用来启动某些动作链，比如触发外部 web 挂钩

使用消息队列有许多原因。Masonite 附带了默认的`async`队列驱动程序，它只是接受一个任务并异步运行它。这对于像启动 API 这样的小事是有益的，但对于可能需要几秒钟的较大任务来说仍然不好，因为这些任务只是使用了一个单独的线程，不具备完整消息队列的能力和灵活性。

或者， [Masonite](https://github.com/MasoniteFramework/masonite) 也支持一个`amqp`驱动程序，这是一个由几个消息队列使用的协议，包括流行的 RabbitMQ。

我们将探讨如何使用 [Masonite](https://github.com/MasoniteFramework/masonite) 设置 RabbitMQ，并创建一些可以传递给我们的队列的作业，这些作业将通过 RabbitMQ 进行处理

# 安装

首先，我们需要本地的 RabbitMQ。为了做到这一点，我们将前往[安装页面，并根据您的操作系统下载 RabbitMQ](https://www.rabbitmq.com/download.html) 。Windows 将在该页面上要求安装程序，但如果您使用的是 Mac OSX，您可以简单地使用`brew`:

```
$ brew install rabbitmq 
```

# 运行 RabbitMQ

安装后，您可以运行它:

```
$ rabbitmq-server 
```

如果您得到类似命令未找到的信息，那么[看看这个堆栈溢出回答](https://stackoverflow.com/questions/23050120/rabbitmq-command-doesnt-exist)。您可能需要将其添加到您的路径中。

# 配置

## pip 安装

首先，为了成功使用`amqp`驱动程序，你需要安装`pika`。这是一个低级的`amqp`库，Masonite 用它通过 worker 和 publisher 连接到 RabbitMQ。

```
$ pip install pika 
```

## 配置文件

Masonite 配置很简单。在`config/queue.py`中，您需要将驱动程序设置为`amqp`，并在`DRIVERS`常量中设置一些默认配置选项:

```
DRIVER = 'amqp'
...
DRIVERS = {
    'amqp': {
        'username': 'guest',
        'password': 'guest',
        'host': 'localhost',
        'port': '5672',
        'channel': 'default',
    }
} 
```

这是本地连接 RabbitMQ 所需的默认设置。RabbitMQ 会默认使用`guest`的用户名和密码。

## 高级配置:

如果您将 RabbitMQ 用于第三方服务或生产中，一些 RabbitMQ 实例可能需要一个`vhost`而不需要端口。在这种情况下，我们的配置将稍微高级一些:

```
DRIVERS = {
    'amqp': {
        'username': 'guest',
        'vhost': '/',
        'password': 'guest',
        'host': 'localhost',
        'port': None,
        'channel': 'default',
    }
} 
```

注意，`port`接受一个值`None`，而`vhost`可以被完全忽略或者保留。`/`是默认的`vhost`，但在生产系统中，它可能与您的用户名相同。

## 运行职工

worker 位于 Masonite 应用程序和 RabbitMQ 之间。这个工人将坐下来监听 Masonite 作业并运行它们。您应该在新的终端中执行此操作:

```
$ craft queue:work 
```

为了处理作业，在 Masonite 应用程序运行期间，这个 worker 始终需要运行。如果该工作线程没有运行，则不会处理作业。

需要注意的是，如果工作线程没有运行，而 Masonite 收到了一个作业，它将缓存该作业，并在工作线程再次启动备份时运行。这就是所谓的“持久”队列。

一旦成功运行，您应该会看到如下内容:

[![](img/eebf6ba71b8fa3f69917a345d1a60217.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ns832qyO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9qk5gxvqadyxk3a7pntr.png)

请注意，我们正在收听`default`频道。我们可以指定收听哪个频道:

```
$ craft queue:work --channel rabbit 
```

这将在`rabbit`频道上监听。无论我们使用哪个通道，在我们的配置文件中都需要相同。如果我们正在收听`rabbit`，我们配置文件中的频道需要是`rabbit`:

```
DRIVERS = {
    'amqp': {
        'username': 'guest',
        'vhost': '/',
        'password': 'guest',
        'host': 'localhost',
        'port': None,
        'channel': 'rabbit',
    }
} 
```

## 创造就业岗位

好极了。现在我们已经设置了 RabbitMQ，我们正在收听即将到来的作业，现在让我们继续并创建一个作业。为此我们可以用手艺为我们搭起一个脚手架:

```
$ craft job HelloWorld 
```

这将在`app/jobs/HelloWorld.py`中创建一个新任务:

```
''' A HelloWorld Queue Job '''

from masonite.queues.Queueable import Queueable

class HelloWorld(Queueable):

    def __init__(self):
        pass

    def handle(self):
        pass 
```

现在我们有了一个`handle`方法，我们将把我们的逻辑放入其中。我们想将一条消息传入我们的`handle`方法，并将其打印到终端。所以我们应该这样设置:

```
class HelloWorld(Queueable):

    ...

    def handle(self, message):
        print("hello " + message) 
```

似乎很容易。现在让我们将该作业发送到队列中！

## 发送任务

首先确保我们的队列正在运行。如果不是，则再次运行:

```
craft queue:work 
```

为了将作业从控制器发送到队列，我们可以使用 IOC 容器中的队列:

```
from app.jobs.HelloWorld import HelloWorld
...
def show(self, Queue):
    Queue.push(HelloWorld, args=['world']) 
```

`args`将传递我们传递给`handle`方法的任何 iterable。我们应该在终端内部看到:

[![](img/0455948a462261b5b88e84fb582e0759.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eK1qxT3W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mamr5qb37co7e4k6ozu8.png)

恭喜你！您刚刚在 RabbitMQ 中处理了您的第一个 Masonite 作业！

# 高级

本节将更深入地介绍您可能需要了解的内容:

## 构造函数

作业构造函数通过容器来解析。所以我们可以把我们需要的任何东西放在构造函数中，它将从容器中被解析:

```
from masonite.request import Request:

class HelloWorld(Queueable):

    def __init__(self, request: Request):
        self.request = request 
```

现在，无论我们何时推动这项工作，它都会得到解决:

```
from app.jobs.HelloWorld import HelloWorld
...
def show(self, Queue):
    Queue.push(HelloWorld) 
```

可选地，我们也可以把我们需要的东西传入构造函数。在这种情况下，将**而不是**被解决:

```
from app.jobs.HelloWorld import HelloWorld
from masonite.request import Request
...
def show(self, Queue, request: Request):
    Queue.push(HelloWorld(request)) 
```

## 多重职务。

`Queue.push()`接受多份工作:

```
from app.jobs import SomeJob, AnotherJob
...
def show(self, Queue):
    # do your normal logic
    Queue.push(SomeJob, AnotherJob(1,2)) 
```

在这里传入`args`将把它们传入两个 job`handle`方法:

```
def show(self, Queue):
    # do your normal logic
    Queue.push(SomeJob, AnotherJob(1,2), args=['var1', 'var2']) 
```

这仅在两个作业`handle`方法定义相同时有用。如果一个作业需要不同数量的参数，那么您需要分两次发送它们:

```
def show(self, Queue):
    # do your normal logic
    Queue.push(SomeJob, args=['var1', 'var2'])
    Queue.push(AnotherJob(1,2), args=['var1', 'var2', 'var3']) 
```