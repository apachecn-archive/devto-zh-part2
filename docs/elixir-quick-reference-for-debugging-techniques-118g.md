# elixir——调试技术快速参考

> 原文：<https://dev.to/leandrocp/elixir-quick-reference-for-debugging-techniques-118g>

[多](https://elixir-lang.org/getting-started/debugging.html) [有](http://blog.plataformatec.com.br/2016/04/debugging-techniques-in-elixir-lang/) [有](https://speakerdeck.com/gcauchon/debugging-elixir-efficently)说过关于仙丹调试技术，但是在这篇文章中，我想快速概述一下所有可能的选项，作为你需要调试仙丹代码时的参考。说够了，让我们逐一检查:

## IO.inspect

最简单的技巧:

```
my_list = [1, 2, 3]
IO.inspect(my_list)

# Outputs
[1, 2, 3] 
```

[IO.inspect/2](https://hexdocs.pm/elixir/IO.html#inspect/2)也可以在管道内使用，因为[返回被检测的项目](https://github.com/elixir-lang/elixir/blob/v1.6.5/lib/elixir/lib/io.ex#L311)。这里的技巧是使用选项标签:输出一个标识每个检查的字符串:

```
[1, 2, 3]
|> IO.inspect(label: "before")
|> Enum.map(&(&1 * 2))
|> IO.inspect(label: "after")
|> Enum.sum

# Outputs
before: [1, 2, 3]
after: [2, 4, 6] 
```

## IO.inspect 与绑定

[binding/1](https://hexdocs.pm/elixir/Kernel.html#binding/1) 在你想看到当前函数的所有变量名和值的时候非常有用:

```
def some_fun(a, b, c) do
  IO.inspect binding()
  ...
end

iex> some_fun(:foo, "bar", :baz)
[a: :foo, b: "bar", c: :baz] 
```

## 顶点

类似于 IO.inspect/2,[apex](https://github.com/BjRo/apex)是一个值得一提的 lib，尤其是因为它的 [adef](https://github.com/BjRo/apex#awesome-def-aka-adef) 宏:

```
# import apex adef macro
import Apex.AwesomeDef

# change def to adef
adef test(data, options \\ []) do
  data
end

# when you call your function, you'll receive detailed information about its execution
iex(1)> Apex.test "foo"
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
Function Elixir.Apex.test was called
defined in /Users/bjoernrochel/Coding/Laboratory/apex/lib/apex.ex:12
----------------------------------------------------------------------------------------------------
Parameters:
----------------------------------------------------------------------------------------------------
[
  [0] "foo"
  [1] []
]

----------------------------------------------------------------------------------------------------
Result:
----------------------------------------------------------------------------------------------------
"foo" 
```

## IEx.pry

但这可能会非常乏味，并且仅限于像 IO.inspect 或 apex 这样的数据检查，这时[IEx.pry/0](https://hexdocs.pm/iex/IEx.html#pry/0)就会出现，因为它允许你窥探当前代码。将下面的代码放在您想要窥探的行:

```
def some_fun(a, b, c) do
  require IEx; IEx.pry
  ...
end 
```

现在在 IEx 会话中执行代码:如果您使用的是 Phoenix Framework，那么可以使用 iex -S mix 或 iex -S mix phx.server。提示:如果您正在运行数据库种子之类的任务，您可以通过运行 iex -S mix run priv/repo/seeds.exs 或任何其他脚本来窥探这些代码。

一旦代码执行到 IEx.pry 点，就会打开一个交互式 shell，允许您与当前代码进行交互。

如果你想了解更多关于用 IEx.pry 调试 Phoenix 的技巧，请访问[用 IEx.pry 调试 Phoenix](https://medium.com/@diamondgfx/debugging-phoenix-with-iex-pry-5417256e1d11)。

## :调试器

与 IEx.pry 非常相似，它在断点处停止执行，并允许您检查当前代码，但是[:调试器](http://erlang.org/doc/apps/debugger/debugger_chapter.html)为您提供了一个漂亮的可视化界面，就像 ide 中的界面一样:

[![gif from http://blog.plataformatec.com.br/2016/04/debugging-techniques-in-elixir-lang/)](../Images/bd1d5375a9849796cbe0ac1272b3c741.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B3pmplW5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://blog.plataformatec.com.br/wp-content/uploads/2016/04/debugger-elixir.gif)

gif 来自[http://blog . platafomatec . com . br/2016/04/debugging-techniques-in-elixir-lang](http://blog.plataformatec.com.br/2016/04/debugging-techniques-in-elixir-lang)

要打开这个调试器，你需要启动它并设置一个断点:

```
# given that you have this module
defmodule MyApp.Example do
  def sum(x, y) do
    x + y
  end
end

# start a new iex session
$ iex -S mix

# then start :debugger
iex> :debugger.start()

# prepare your module for debugging
iex> :int.ni(MyApp.Example)

# set a break point at the line you want to capture
iex> :int.break(MyApp.Example, 3)

# and finally call your function
iex> MyApp.Example.sum(1,2) 
```

## :系统获取状态和系统获取状态

这个只适用于进程。顾名思义， [:sys.get_state/1](http://erlang.org/doc/man/sys.html#get_state-1) 获取一个进程的当前状态，不仅可以从一个 GenServer 获取，也可以从任何类型的进程获取:

```
iex(1)> defmodule Example, do: use GenServer
iex(2)> {:ok, pid} = GenServer.start_link(Example, %{ping: "pong"})
iex(3)> :sys.get_state(pid)
%{ping: "pong"} 
```

*摘录自[的片段，查看仙丹](https://til.hashrocket.com/posts/urmxev1dh5-looking-at-the-state-of-processes-in-elixir)的流程状态。*

如果你需要比当前状态更多的数据，只需调用 [:sys.get_status/1](http://erlang.org/doc/man/sys.html#get_status-1) ，它将返回整个过程的信息:

```
{:status, #PID<0.134.0>, {:module, :gen_server},
  [
    [
      "$initial_call": {Sequence.Server, :init, 1},
      "$ancestors": [#PID<0.118.0>, #PID<0.57.0>]
    ],
    :running,
    #PID<0.118.0>,
    [statistics: {{{2017, 12, 23}, {14, 11, 13}}, {:reductions, 14}, 3, 0},
    [
      header: 'Status for generic server <0.134.0>',
      data: [
        {'Status', :running},
        {'Parent', #PID<0.118.0>},
        {'Logged events', []}
    ],
    data: [{'State', 103}]
  ]

# Excerpt From: Dave Thomas. “Programming Elixir ≥ 1.6" 
```

额外收获:您可以在运行时使用 [:sys.replace_state/2](http://erlang.org/doc/man/sys.html#replace_state-2) 来替换流程状态，这对于测试一些特定的情况非常方便。

## [T1】process . info](#processinfo)

你也可以使用[Process.info/1](https://hexdocs.pm/elixir/Process.html#info/1)来获得关于一个特定进程的信息:

```
iex> defmodule Example, do: use GenServer
iex> {:ok, pid} = GenServer.start_link(Example, %{ping: "pong"})
iex> Process.info pid
[
  current_function: {:gen_server, :loop, 7},
  initial_call: {:proc_lib, :init_p, 5},
  status: :waiting,
  message_queue_len: 0,
  messages: [],
  links: [#PID<0.610.0>],
  dictionary: [
    "$initial_call": {Example, :init, 1},
    "$ancestors": [#PID<0.610.0>, #PID<0.70.0>]
  ],
  trap_exit: false,
  error_handler: :error_handler,
  priority: :normal,
  group_leader: #PID<0.60.0>,
  total_heap_size: 233,
  heap_size: 233,
  stack_size: 10,
  reductions: 27,
  garbage_collection: [
    max_heap_size: %{error_logger: true, kill: true, size: 0},
    min_bin_vheap_size: 46422,
    min_heap_size: 233,
    fullsweep_after: 65535,
    minor_gcs: 0
  ],
  suspending: []
] 
```

## :系统跟踪

还是说进程调试，我们拥有的另一个资源是 [:sys.trace/2](http://erlang.org/doc/man/sys.html#trace-2) 用来跟踪进程调用，会显示每个调用以及调用进程的状态变化:

```
iex> :sys.trace pid, true
:ok
iex> GenServer.call(pid, ​:next_number​)
*DBG* <0.69.0> got call next_number from <0.25.0>
*DBG* <0.69.0> sent 105 to <0.25.0>, new state 106
105

# Excerpt From: Dave Thomas. “Programming Elixir ≥ 1.6” iBooks. 
```

## 酒厂命令

在您将应用程序部署为一个包版本之后，获取关于它的信息可能会非常有帮助，特别是当某些东西没有按预期工作时。如果您已经使用[酒厂](https://github.com/bitwalker/distillery)进行了部署，您很快就会发现 mix 的工作方式与您在本地机器上的工作方式不同，这是意料之中的。但是你可以通过调用`bin/<app_name> <command>`在那个版本上执行一些命令，两个更有用的命令是:`remote_console`在正在运行的版本上启动一个 IEx 会话，并帮助给你一个所有[命令的列表](https://github.com/bitwalker/distillery/tree/1.5.2/priv/libexec/commands)。

## :观察者

虽然它不完全是一个调试工具，但是值得一提的是 [:observer](http://erlang.org/doc/man/observer.html) 可以帮助您了解正在运行的系统。我不会深究细节，因为有很多文章已经这样做了，所以我在这里只留下启动观察器的函数调用:

```
iex> :observer.start 
```

### 注释和来源

如果你有更多的提示和技巧，请留下评论，这样我就可以更新这个帖子，帮助传播更多的长生不老药知识。:)

一些代码片段摘自伟大的官方文档或官方入门教程。

书籍[编程仙丹](https://pragprog.com/book/elixir/programming-elixir)也被用作来源。

今天我从 Hashrocket 学到的也是一个很好的提示来源。