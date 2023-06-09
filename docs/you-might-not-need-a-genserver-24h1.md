# 您可能不需要 GenServer

> 原文：<https://dev.to/jackmarchant/you-might-not-need-a-genserver-24h1>

当您浏览 Elixir 文档或阅读博客帖子(如本文)时，毫无疑问您会遇到 GenServer。它可能是 Elixir 标准库中使用最频繁的模块之一，因为它是一个很好的关于过程抽象的教学工具。然而，知道什么时候向你友好的邻居求助是令人困惑的。

GenServer 是典型的客户端与服务器交互的一般实现，其中客户端是一个进程，服务器是您的 GenServer。这种抽象的存在是因为如果没有它，我们将不得不编写更多的样板代码来接收来自其他进程的消息。

我越来越喜欢 GenServer 和 Elixir 社区，但是在某些情况下你可能不需要 GenServer。

让我们快速看一下任务和 GenServer 之间的区别，并找出哪个模块最适合。

### 任务

任务是一个简单而强大的工具，可以改变代码的执行方式，并引入 somme 轻量级并发。

您可以异步运行一个函数，隔离任何故障:

```
Task.async(fn -> raise "the roof" end) 
```

Enter fullscreen mode Exit fullscreen mode

这条线有几个优点:

该任务将在一个新的进程中运行，让您可以自由地做您想做的所有其他事情。运行函数过程中出现的任何失败或错误都将被隔离到任务的进程中，因此您的主进程不会在任务之后停止执行任何代码。
与此相反，我们可以等待结果，在未来的某个时刻:

```
task = Task.async(&my_function/1)
# .. some other code
result = Task.await(task) 
```

Enter fullscreen mode Exit fullscreen mode

通过以这种方式使用任务，我们可以推迟对结果的检索，同时执行一些其他代码。一旦你提取了结果，这个过程将会退出。

一个任务在其生命周期中只执行一个功能，并不意味着是一个长时间运行的进程，也不涉及任何进程间的通信。这样做的好处是，编写一次性任务更容易，测试也更简单。在大多数情况下，您只想异步运行一个函数，一个任务就足够了。

### 代理

代理是抽象状态的过程。如果您所需要的只是在相对较短的时间内(在内存中)保存一个值，那么代理是一个完美的选择。代理实际上是一个被抽象成自己的模块的 GenServer。所以，虽然你得到了使用发电机的所有好处

如果我们想在代理中保存一个值，您可以使用`Agent.get/3`存储它，使用`Agent.update/3`存储值。这些函数已经在代理 API 中为您定义了——如果您选择用 GenServer 实现相同的功能，您将不得不自己定义这些函数。

```
{:ok, pid} = Agent.start(fn -> "hello" end)
Agent.get(pid, fn state -> state end)
"hello"

Agent.update(pid, fn state -> state <> " world" end)
Agent.get(pid, fn state -> state end)
"hello world" 
```

Enter fullscreen mode Exit fullscreen mode

超时作为第三个参数包含在每个函数中，因为它们是同步的，所以调用者必须等待作为第二个参数传递的函数完成执行。如果我们想使用代理中的状态运行一个函数，我们需要使用`Agent.cast/2`，它的工作方式与`GenServer.cast/3`类似。

```
{:ok, pid} = Agent.start(fn -> "hello" end)
Agent.cast(pid, fn _ -> "world" end)
:ok
Agent.get(pid, fn state -> state end)
"world" 
```

Enter fullscreen mode Exit fullscreen mode

### 最后的想法

如你所见，它几乎拥有你在 GenServer 中看到的所有典型功能，但是你不需要担心进程间的通信。您可以简单地专注于构建应用程序的功能。Elixir 越来越受欢迎的部分原因是，您可以在构建应用程序时围绕常见问题使用抽象，并且只有当您需要细粒度控制时，底层模块才可供您使用，对于 GenServer 尤其如此。

当然，GenServer 擅长解决的问题比我在这里写的任何东西都要复杂得多，但重点并不是要说明应用程序有多复杂，而是说明你可以多快地开始使用一些更简单的替代方案，并且只在绝对需要的时候使用 GenServer。