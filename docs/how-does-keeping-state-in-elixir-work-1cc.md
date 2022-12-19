# 长生不老药如何保持状态？

> 原文：<https://dev.to/domnikl/how-does-keeping-state-in-elixir-work-1cc>

2011 年，当我刚接触 Erlang 和函数式编程时，我遇到的最大问题是这样的:

> 当函数式编程中没有状态时，如何存储、检索和更新状态？"

我读到了 [gen_server](http://erlang.org/doc/man/gen_server.html) 的内容，在这一点上，我意识到尽管每个人都在说“函数式编程中没有状态”，但实际上你确实有机制将状态存储在 Erlang/Elixir 中以备后用。

所以我一头扎进去，学习它是怎么做的。

## 怎么做到的

实际上，其背后的概念并不像火箭科学那么复杂。基本上，它由一个递归调用自身并与想要存储和更新状态的其他进程通信的函数组成。如果需要更改状态，将使用新的状态进行下一次调用。

让我们构建一个堆栈模块，保存它的值，并允许用户从它那里获取`push`和`pop`值。

我们从一个简单的函数开始，该函数通过递归调用自身并接收基本消息来处理状态的变化，从而运行并保持状态:

```
defmodule Stack do
  def loop(state) do
    receive do
      {_from, :push, value} ->
        loop([value | state])

      {from, :pop} ->
        [h | t] = state
        send(from, {:reply, h})
        loop(t)
    end

    loop(state)
  end
end 
```

`loop/1`等待新消息到达，并使用`receive`进行阻塞，直到该流程有消息需要进行模式匹配。如您所见，我们目前支持的两种操作有两种模式。

第一个将匹配`{_from, :push, value}`，它处理对堆栈的推送。不需要发送 pid `_from`，因为这是一个异步操作。它开始下一次迭代，添加新的`value`作为`state`的新的第一个元素，再次等待新消息到达。

另一个模式`{from, :pop}`处理弹出操作。消息发送者`from`将接收回一条提取了`state`的第一个元素的消息，而列表的其余部分将被交给对`loop/1`的另一个调用，该调用将等待另一条消息到达。

# 测试一下

让我们来看看我们的堆栈实现。启动`iex`，把上面的模块粘贴进去，我们就可以开始了！要启动堆栈，只需将函数生成为一个进程:

```
iex> pid = spawn(Stack, :loop, [[]])
#PID<0.108.0> 
```

`spawn/3`期望一个模块(堆栈)，一个原子形式的函数名(:loop)和一个参数列表，在我们的例子中是一个空列表。这就产生了最初的调用，本质上就是`Stack.loop([])`。返回的`pid`是一个进程标识符，所有消息都需要发送到这个标识符。

为了将一个新值压入堆栈，我们需要给那个`pid`发送一个这样的消息:

```
iex> send(pid, {self(), :push, 1})
{#PID<0.88.0>, :push, 1} 
```

`self()`返回当前进程的值(在我们的例子中是 iex 的`pid`), send 将返回发送的消息。对于此操作，我们不需要它，但无论如何发送它是一个好的做法。此外，我们还没有看到堆栈的任何变化，但是请记住:推送操作是异步的。

现在让我们通过从堆栈中弹出一个值来查看堆栈中的变化:

```
iex> send(pid, {self(), :pop})
{#PID<0.88.0>, :push, 1} 
```

在这里，`self()`是必需的，因为我们期望一条消息返回到包含该值的 iex-pid。再次，`send`返回发送的消息，其他什么都不会发生。那是因为我们还没看我们回的信息。让我们使用 iex 助手`flush`来检索我们主进程的邮箱:
的内容

```
iex> flush
{:reply, 1}
:ok 
```

这就是了。正如所料，堆栈包含值`1`。

# 干净的客户端界面

由于我们的堆栈现在正在工作，我们应该围绕它构建一个漂亮、干净的 API。想要使用堆栈模块的用户不应该关心需要将哪些消息发送到进程。相反，他/她应该只使用函数将值放入堆栈:

让我们扩展这个模块，添加一些短函数来构建 API 和一个干净的客户端用户界面。

```
defmodule Stack do
    ...

    def start_link(state) do
        spawn_link( __MODULE__ , :loop, [init(state)])
    end

    def init(state), do: state

    def push(pid, value) do
        send(pid, {self(), :push, value})
        :ok
    end

    def pop(pid) do
        send(pid, {self(), :pop})

        receive do
            {:reply, value} -> value
        end
    end
end 
```

如果您将此与使用 [GenServer](https://hexdocs.pm/elixir/GenServer.html) 时需要实现的回调进行比较，那么您会看到它使用了相同的命名和函数签名。这不是偶然的——这是为了向您展示 GenServer 与上面的代码完全一样。当然，除了更多的错误处理和更通用的方式。但是概念保持不变。

顺便说一句。由于[尾部调用优化](http://learnyousomeerlang.com/recursion),`loop/1`函数的调用堆栈在内存使用方面是恒定的，因此永远运行时不会有问题。

在这里，我将让您尝试一些状态消息和递归函数。长生不老药快乐！