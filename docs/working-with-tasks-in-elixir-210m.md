# 在 Elixir 中处理任务

> 原文：<https://dev.to/jackmarchant/working-with-tasks-in-elixir-210m>

在撰写 Elixir 中的[理解并发性时，我开始比以前更加了解流程。与他们更密切地合作加强了我自己头脑中的概念。
在 Elixir 的标准库中，有一些抽象通用代码的模块，如果没有这些模块，你会发现自己经常重复这些代码。
当你想写异步代码的时候，你可能会关心代码的结果，有时候可能不会。](https://www.jackmarchant.com/articles/understanding-concurrency-in-elixir) [任务](https://hexdocs.pm/elixir/Task.html)模块让这一切变得简单。为了更好地理解任务是如何工作的，我想我会创建一个简单的(幼稚的)模块，实现一个与任务类似的 API。

## 重新执行任务模块

考虑下面这个模块，我把它叫做`Job`。

```
defmodule Job do
  def async(fun) when is_function(fun) do
    parent = self()

    spawn_link(fn ->
      send(parent, {self(), fun.()})
    end)
  end

  def await(job, timeout \\ 5000) do
    receive do
      {^job, result} -> result
    after
      timeout -> {:error, "no result"}
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

`Job.async/1`接受单个函数作为参数，这是将异步执行的工作。您可以运行该函数，而不关心结果:

```
iex> Job.async(fn -> "Hi" end)
<#PID> 
```

Enter fullscreen mode Exit fullscreen mode

它返回一个进程标识符(PID ),这是调用`spawn_link/1`的结果，传递一个函数，该函数又向父进程发送一条消息。我们已经将`async`和`await`的实现分开，这样如果你愿意等待结果，你可以选择将 PID 传递给`await`。

让我们看看那会是什么样子:

```
iex> Job.async(fn -> "Hi" end) |> Job.await()
"Hi" 
```

Enter fullscreen mode Exit fullscreen mode

当我们在作业 PID 上进行模式匹配以识别正在接收的消息和`job`的结果时，结果的值是调用传递给`Job.async/1`的函数的结果。

在这种情况下，结果可以立即看到，但是如果初始函数实际上正在执行异步工作，那么它将等待一段超时时间，然后放弃。这是`await`功能的`after`部分。

```
iex> Job.async(fn -> :timer.sleep(6000) end) |> Job.await(5000)
{:error, "no result"} 
```

Enter fullscreen mode Exit fullscreen mode

我们得到一个错误，因为超时已经过去，给定函数中的计时器暂停处理直到 6 秒过去，而`Job.await/2`函数在 5 秒后放弃等待。

## 结论

希望`Job`模块可以帮助您理解`Task`模块在幕后做什么，在某种程度上，它不是完整的实现，使用任务还会有更多的功能，比如过程监督、流等等。也就是说，无论如何，熟悉进程间的消息传递是很有用的。