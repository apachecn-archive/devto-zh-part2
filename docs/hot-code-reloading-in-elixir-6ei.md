# Elixir 中的热代码重载

> 原文：<https://dev.to/appsignal/hot-code-reloading-in-elixir-6ei>

Elixir 源于 Erlang，它的健壮性和可靠性经常被认为是它最大的优点。最典型的例子就是它能够升级应用程序而无需重启。

作为 Erlang 最令人惊叹的功能之一，*热代码重新加载*有时被比作更换行驶中的汽车的车轮，它在 [Erlang:电影](https://www.youtube.com/watch?v=BXmOlCy0oBM)中通过电话和热错误修复得到了庄严的展示。但是，它是如何在引擎盖下工作的呢？

在本期的 Elixir Alchemy 中，我们将深入研究热代码重载，看看 Erlang 的代码服务器如何处理 Elixir 中的无缝代码升级。为了理解这一切是如何工作的，我们将从模块级别开始，一步步向上。我们开始吧！

## 升级模块

热代码重载的魔力的第一部分是 Erlang 的代码服务器能够同时运行一个模块的多个版本。它允许现有的进程运行完成，而不必重新启动或改变它们的运行代码。

为了说明这一点，让我们看一个名为`Counter`的模块的例子。顾名思义，它使用一个递归函数从 0 开始计数，休眠一秒钟，打印当前数字，并用递增 1 的数字调用自己。

```
defmodule Counter do
  def count(n) do
    :timer.sleep(1000)
    IO.puts("- #{inspect(self())}: #{n}")
    count(n + 1)
  end
end 
```

在启动 IEx ( `$ iex -S mix`)之后，我们在一个单独的进程中生成一个进程来启动计数器循环。我们将模块(`Counter`)、函数名(`:count`)和参数(`[0]`)传递给`spawn/3`函数。

```
iex(1)> spawn(Counter, :count, [0])
#PID<0.107.0>
- #PID<0.107.0>: 0
- #PID<0.107.0>: 1
- #PID<0.107.0>: 2
- #PID<0.107.0>: 3
…
iex(2)> 
```

在保持计数器运行的同时，我们更新计数器模块，将`lib/counter.ex`中的数字从 1 增加到 2。完成这些并保存文件后，我们在 IEx 会话中重新编译模块。

```
…
- #PID<0.107.0>: 2
- #PID<0.107.0>: 3
iex(2)> r Counter
{:reloaded, Counter, [Counter]}
- #PID<0.107.0>: 4
- #PID<0.107.0>: 5
- #PID<0.107.0>: 6
…
iex(3)> 
```

模块被重新编译，但是现有的计数器仍然增加 1，这意味着旧版本的代码仍然在这个进程中运行。如果我们产生一个运行计数器的新进程，它*将*增加 2，因此它将运行新版本。

```
…
- #PID<0.107.0>: 4
- #PID<0.107.0>: 5
- #PID<0.107.0>: 6
iex(3)> spawn(Counter, :count, [0])
#PID<0.114.0>
- #PID<0.107.0>: 7
- #PID<0.114.0>: 0
- #PID<0.107.0>: 8
- #PID<0.114.0>: 2
- #PID<0.107.0>: 9
- #PID<0.114.0>: 4
…
iex(5)> 
```

这个例子展示了 Erlang 的代码服务器。通过保持模块的旧版本，第一个进程(`#PID<0.107.0>`)像以前一样继续运行，但是新产生的进程(`#PID<0.114.0>`)自动使用新版本。

### 二郎码服务器

Erlang 的*代码服务器*负责在运行的系统中加载编译好的代码。在任何时候，代码服务器都可以在内存中保存一个模块的两个版本。当一个模块被加载时，它成为该模块的*当前*版本。如果该模块的先前版本已经存在，它会被标记为*旧版本*。

模块的当前版本和旧版本可以同时运行，但是从旧版本导出的函数会被新版本的函数所替换。这确保了每个外部函数调用总是调用当前版本模块上的函数。

如果加载模块的新版本时，进程已经在运行，它将停留在旧版本上，并且它的所有局部函数调用将由模块的旧版本处理。

## 热重装发电机组

让我们更进一步，把我们的例子变成一个 GenServer。像前面例子中的`Counter`模块一样，`CountServer`通过每秒递增其状态来递增计数。

```
defmodule CountServer do
  use GenServer

  def start_link do
    GenServer.start_link(__MODULE__, 0)
  end

  def init(state) do
    Process.send_after(self(), :increment, 1000)
    {:ok, state}
  end

  def handle_info(:increment, n) do
    incremented = n + 1
    IO.puts("- #{inspect(self())}: #{incremented}")

    Process.send_after(self(), :increment, 1000)

    {:noreply, incremented}
  end
end 
```

由于`CountServer`是一个 GenServer，我们不需要在一个单独的进程中手动启动它。相反，我们在新的 IEx 会话中调用`CountServer.start_link/0`来启动计数器。

```
iex(1)> {:ok, pid} = CountServer.start_link()
{:ok, #PID<0.130.0>}
- #PID<0.130.0>: 1
- #PID<0.130.0>: 2
- #PID<0.130.0>: 3
…
iex(2)> 
```

让我们像上一个例子那样更新它。我们将`CountServer`更新为递增 2，而不是 1。然后，在运行的 IEx 会话中，我们重新编译该模块。

```
…
- #PID<0.130.0>: 2
- #PID<0.130.0>: 3
iex(2)> r CountServer
{:reloaded, CountServer, [CountServer]}
- #PID<0.130.0>: 5
- #PID<0.130.0>: 7
- #PID<0.130.0>: 9
…
iex(3)> 
```

这一次，正在运行的 GenServer *进行了*更新。重新编译模块后，计数器开始递增 2，而不是 1，无需重新启动或启动新的计数器。

### 本地和外部函数调用

第一个例子有一个递归函数产生在一个进程中，而第二个例子有一个 GenServer 产生一个进程来保持它的状态。

正如我们在[解构 GenServer](https://blog.appsignal.com/2018/06/12/elixir-alchemy-deconstructing-genservers.html)时了解到的，GenServer 的模块及其衍生状态在不同的进程中运行。在第二个例子中，保存在 GenServer 进程中的状态通过调用`CountServer`模块来更新。

这种差异对于代码重载非常重要。*本地函数调用*，和第一个例子一样，模块调用自己的函数，都是在老版本的模块上执行的。*外部函数调用*，如调用`CountServer`模块的 GenServer 进程，总是在模块的当前版本上完成。

这解释了为什么第一个例子没有重新加载现有的模块，而第二个例子在新模块加载后立即重新加载。

## 转换状态

尽管 GenSever 例子中的状态被`CountServer`模块的重新加载版本正确地转换了，但是还有一个场景要看。当新版本的实现需要不同的状态时会发生什么？

举个例子，假设我们需要我们的`CountServer`从现在开始只产生偶数。我们当前的实现每秒增加 2，所以大部分工作已经完成。

然而，如果我们有一个仍以 1 为增量运行的进程，我们就有在错误的时间升级的风险，导致它产生奇数。当我们将模块升级到新版本时，我们需要确保更新状态。

Elixir 的`GenServer`模块有一个名为`code_change/3`的回调，用于在代码发生变化时更新状态。

```
defmodule CountServer do
  use GenServer

  # ...

  def code_change(_old_vsn, state, _extra) when rem(state, 2) == 1 do
    {:ok, state - 1}
  end

  def code_change(_old_vsn, state, _extra) do
    {:ok, state}
  end
end 
```

这个例子实现了`code_change/3`回调。如果状态是奇数，它将从当前状态中减去 1，使其成为偶数。

`_old_vsn`参数保存我们正在升级的模块的旧版本。它可用于从特定版本升级，`_extra`参数可用于升级时的额外参数。为简洁起见，这里忽略这两者。

为了调用`code_change/3`回调，我们必须显式地改变流程的代码。为了做到这一点，我们暂时中止这个过程，运行代码更改，然后再次继续。

```
…
- #PID<0.130.0>: 7
- #PID<0.130.0>: 9
iex(3)> :sys.suspend(pid)
:ok
iex(4)> r CountServer
{:reloaded, CountServer, [CountServer]}
iex(5)> :sys.change_code(pid, CountServer, nil, [])
:ok
iex(6)> :sys.resume(pid)
:ok
- #PID<0.130.0>: 10
- #PID<0.130.0>: 12
- #PID<0.130.0>: 14
…
iex(3)> 
```

*注意*:当发布代码时，这是为你的应用中的每个模块自动完成的。不需要在 IEx 外部显式调用`change_code/4`函数。

## 向后兼容

在新版本的模块上执行外部函数调用，可以让您的应用程序逐渐切换到新版本的模块。当执行外部函数调用时，强制在旧版本模块上延迟的进程调用当前版本，确保延迟的进程不会产生更多延迟的进程。然而，当新代码与旧模块不向后兼容时，在运行的系统中混合新旧代码会导致问题。

让我们改进一下我们的计数器。我们不会对附加值进行硬编码，而是允许它作为参数传递。

```
defmodule CountServer do
  use GenServer

  def start_link do
    GenServer.start_link(__MODULE__, 1)
  end

  def init(state) do
    send(self(), {:increment, 1})
    {:ok, state}
  end

  def handle_info({:increment, value}, state) do
    new_state = state + value
    IO.puts(new_state)
    Process.send_after(self(), {:increment, 1}, 1000)

    {:noreply, new_state}
  end

  # ...
end 
```

此示例允许传递一个要递增的值。我们还确保更新了`init/1`和`handle_info/2`中的消息，以确保它们使用新的格式。

```
- #PID<0.130.0>: 12
- #PID<0.130.0>: 14
iex(2)> r CountServer
{:reloaded, CountServer, [CountServer]}
iex(3)>
15:09:01.313 [error] GenServer #PID<0.130.0> terminating
** (FunctionClauseError) no function clause matching in CountServer.handle_info/2
    (odd) lib/count_server.ex:13: CountServer.handle_info(:increment, 14)
    (stdlib) gen_server.erl:637: :gen_server.try_dispatch/4
    (stdlib) gen_server.erl:711: :gen_server.handle_msg/6
    (stdlib) proc_lib.erl:249: :proc_lib.init_p_do_apply/3
Last message: :increment
State: 14
** (EXIT from #PID<0.128.0>) shell process exited with reason: an exception was raised:
    ** (FunctionClauseError) no function clause matching in CountServer.handle_info/2
        (odd) lib/count_server.ex:13: CountServer.handle_info(:increment, 14)
        (stdlib) gen_server.erl:637: :gen_server.try_dispatch/4
        (stdlib) gen_server.erl:711: :gen_server.handle_msg/6
        (stdlib) proc_lib.erl:249: :proc_lib.init_p_do_apply/3

Interactive Elixir (1.7.2) - press Ctrl+C to exit (type h() ENTER for help)
iex(1)> 
```

这个例子产生了一个`FunctionClauseError`，因为旧版本的代码将`:increment`作为消息传递。因为我们删除了接受它的条款，`CountServer`崩溃了，因为没有`handle_info/2`来处理这个案例。

相反，我们应该保留一个接受`:increment`消息的子句，以便为以前的版本提供向后兼容性，从而进行彻底升级。

```
defmodule CountServer do
  use GenServer

  # ...

  def handle_info(:increment, n) do
    handle_info({:increment, 2}, n)
  end

  def handle_info({:increment, value}, n) do
    incremented = n + value
    IO.puts("- #{inspect(self())}: #{incremented}")

    Process.send_after(self(), {:increment, 2}, 1000)

    {:noreply, incremented}
  end

  # ...
end 
```

## 先看代码重载

热代码重载所需的逻辑通常已经存在，但是被抽象掉了。例如，GenServers 用返回未改变状态的存根实现了`code_change/3`回调。

在这一集里，我们从被升级模块的角度进行了观察。在下一集，我们将看看升级整个应用程序，生产版本和凤凰应用程序。订阅[仙丹炼金术](https://appsignal.com/elixir-alchemy)，让下一集直接发送到你的收件箱。