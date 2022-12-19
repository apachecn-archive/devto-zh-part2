# 解构灵药的基因服务器

> 原文：<https://dev.to/appsignal/deconstructing-elixirs-genservers-4h8a>

与面向对象语言中使用的对象实例不同，Elixir 使用 [*GenServers*](https://elixir-lang.org/getting-started/mix-otp/genserver.html) (通用服务器)在单独的进程中存储状态。GenServers 可以保持状态并异步运行代码，这对于保持数据可用而无需在函数间传递数据非常有用。

但是这是如何工作的呢？在这一集的灵药炼金术中，我们将解构灵药的`GenServer`模块，看看它是如何运作的。我们将了解 GenServer 如何与其他进程通信，以及它如何通过自己实现部分功能来跟踪自己的状态。

## 键值存储

在我们深入到`GenServer`模块之前，我们先试着构建一个*键值存储*，它可以通过键存储和检索值。

```
defmodule KeyValue do
  # 1
  def init(state \\ %{}) do
    {:ok, state}
  end

  # 2
  def handle_cast({:put, key, value}, state) do
    {:noreply, Map.put(state, key, value)}
  end

  # 3
  def handle_call({:get, key}, _from, state) do
    {:reply, Map.fetch!(state, key), state}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这个例子有三个*回调函数*来初始化存储，将新值放入存储，并通过键查询值。

1.  `init/1`函数接受一个状态或默认为一个空地图。`GenServer` API 要求在 ok 元组中返回状态。

2.  `handle_cast/2`回调用于*向我们的服务器发送*一条异步消息，而无需等待响应。这个服务器用匹配`{:put, key, value}`的消息来响应 cast，并在 map 中放入一个保存状态的新键。它返回一个`:noreply` -tuple 来指示函数没有返回一个带有更新状态的响应。

3.  为了进行同步调用，使用了`handle_call/3`回调。这个例子用一个匹配`{:get, key}`的消息来响应*调用*，该消息在状态中找到键，并将它连同状态一起返回到一个`:reply`元组中。

为了尝试我们的键值存储，我们使用了`GenServer`上的`start/2`、`cast/3`和`call/2`函数。在内部，它们使用键值存储中定义的内部回调。

```
iex(1)> {:ok, pid} = GenServer.start(KeyValue, %{})
{:ok, #PID<0.113.0>}
iex(2)> GenServer.cast(pid, {:put, :foo, "bar"})
:ok
iex(3)> GenServer.call(pid, {:get, :foo})
{:ok, "bar"} 
```

Enter fullscreen mode Exit fullscreen mode

我们初始化一个新的存储，它在一个 ok 元组中返回 *pid* (进程 id)。pid 用于在稍后调用`cast`和`call`来设置和检索值时引用我们的存储过程。

### 便利功能

由于不得不通过`GenServer`的函数调用我们的服务器变得乏味，所以通常通过向服务器添加函数来抽象它们。

```
defmodule KeyValue do
  use GenServer

  def start do
    GenServer.start(KeyValue, %{})
  end

  def put(server, key, value) do
    GenServer.cast(server, {:put, key, value})
  end

  def get(server, key) do
    GenServer.call(server, {:get, key})
  end

  # Callbacks

  def init(state) do
    {:ok, state}
  end

  def handle_cast({:put, key, value}, state) do
    {:noreply, Map.put(state, key, value)}
  end

  def handle_call({:get, key}, _from, state) do
    {:reply, Map.fetch!(state, key), state}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

通过添加`start/0`、`get/2`和`put/3`函数，我们在调用其 API 时就不用担心服务器的内部实现了。

```
iex(2)> {:ok, pid} = KeyValue.start
{:ok, #PID<0.113.0>}
iex(3)> KeyValue.put(pid, :foo, "bar")
:ok
iex(4)> KeyValue.get(pid, :foo)
"bar" 
```

Enter fullscreen mode Exit fullscreen mode

## 发电服务器内部

为了理解我们的键值服务器是如何工作的，我们将深入到`GenServer`模块中。它依靠*消息传递*和*递归*进行通信和保持状态。

### 流程间的消息传递

正如我们在讨论 Elixir 中的[进程时所讨论的，进程通过相互发送消息来进行通信。在 Elixir 中工作时，大多数消息传递都被抽象掉了，但是您可以通过使用`send/2`函数向另一个进程发送消息。](https://blog.appsignal.com/2017/05/18/elixir-alchemy-demystifying-processes-in-elixir.html) 

```
send(pid, :hello) 
```

Enter fullscreen mode Exit fullscreen mode

该函数接收收件人的`pid`和要发送的消息。通常，当需要传递多个参数时，消息是原子或元组。

在另一个过程中，收件人在其邮箱中接收邮件。它可以使用`receive`函数对传入的消息做出反应。

```
receive do
  :hello ->
    IO.puts "Hello there!"
end 
```

Enter fullscreen mode Exit fullscreen mode

通过使用模式匹配，可以对不同的邮件采取不同的操作。例如，在`GenServer`中，带有`:"$gen_call"`和`:"$gen_cast"`关键字的元组用于查询和更新内部状态。

消息传递是构建服务器所需的第一个要素。在一个 GenServer 中，消息被传递给一个单独的衍生进程来更新和查询其状态。

### 状态通过递归实现

除了接受消息，由 GenServer 产生的进程使用循环来跟踪其初始状态。

```
def loop(state) do
  receive do
    message -> loop(message)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，默认状态被传递给`loop/1`函数，该函数调用`receive`函数来检查邮箱中的新消息。如果没有，它将阻塞进程并等待一个进程进入。当接收到一条消息时，该函数以接收到的消息作为其新状态来调用自身。状态被更新，流程回到等待消息的状态。

GenServers 内部有一个循环方法，它读取传入的消息并调用相应的回调函数。之后，它用更新后的状态再次调用自己。

## 实现我们自己的 GenServer

既然我们已经了解了 GenServer 是如何工作的，我们可以部分地实现我们自己版本的`GenServer`模块，以了解消息传递和递归状态是如何协同工作来使我们的键值存储工作的。

在 Elixir 本身中，`GenServer`模块主要是 Erlang 的经过测试的`gen_server`的包装器，但是我们将在 Elixir 中实现我们的模块。

```
defmodule MyGenServer do
  # 1
  def start(module, state) do
    {:ok, state} = module.init(state)
    {:ok, spawn(__MODULE__, :loop, [state, module])}
  end

  # 2
  def loop(state, module) do
    state =
      receive do
        {:"$gen_cast", from, message} ->
          {:noreply, state} = module.handle_cast(message, state)
          state

        {:"$gen_call", {pid, reference} = from, message} ->
          {:reply, reply, state} = module.handle_call(message, from, state)
          send(pid, {reference, reply})
          state
      end

    loop(state, module)
  end

  # ...

end 
```

Enter fullscreen mode Exit fullscreen mode

1.  `start/2`函数启动服务器进程。它在传递的模块上调用`init/1`函数以允许它设置初始状态，然后[产生](https://hexdocs.pm/elixir/Kernel.html#spawn/3)进程，该进程以`state`和`module`变量作为参数运行`loop/2`函数。

2.  `loop/2`功能接受输入的消息。

    *   当一个`:"$gen_cast"`-消息进来时，在传递给`start/2`函数的模块上调用`handle_cast/2`函数(在我们的例子中是`KeyValue`)。它期望返回一个带有新状态的`:noreply`元组。然后，新状态用于再次运行`loop/2`功能，以等待更多消息。
    *   `:"$gen_call"`-消息调用`handle_call/3`函数。这里需要一个`:reply`-元组，包含回复和新状态。然后，回复被发送回发送消息的流程。然后，像处理强制转换时一样，更新后的状态用于再次调用`loop/2`函数。

我们定制的 GenServer 的服务器部分已经完成。现在，我们可以用定制的 GenServer 启动我们的键值存储了。因为我们的定制 GenServer 的 API 与 Elixir 的 API 相匹配，所以我们可以使用内置版本与由我们的定制 GenServer 启动的键值存储进行通信。

```
iex> {:ok, pid} = MyGenServer.start(KeyValue, %{})
{:ok, #PID<0.119.0>}
iex> GenServer.cast(pid, {:put, :foo, "bar"})
:ok
iex> GenServer.call(pid, {:get, :foo})
"bar" 
```

Enter fullscreen mode Exit fullscreen mode

为了理解`cast/2`和`call/2`函数，我们也将在我们的 GenServer 上实现它们。

```
defmodule MyGenServer do
  # ...

  # 1
  def cast(pid, message) do
    send(pid, {:"$gen_cast", {self(), nil}, message})
    :ok
  end

  # 2
  def call(pid, message) do
    send(pid, {:"$gen_call", {self(), nil}, message})

    receive do
      {_, response} -> response
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

1.  为了向我们的服务器发送消息，我们将以 cast 格式向我们的服务器发送一条消息，并返回一个 ok-atom。
2.  由于调用需要响应，我们将等待使用`receive`函数发回的消息，并返回该消息。

```
iex> {:ok, pid} = MyGenServer.start(KeyValue, %{})
{:ok, #PID<0.119.0>}
iex> MyGenServer.cast(pid, {:put, :foo, "bar"})
:ok
iex> MyGenServer.call(pid, {:get, :foo})
"bar" 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以使用我们的实现来启动我们的键值存储并与之通信，而不需要 Elixir 的`GenServer`模块。

```
defmodule MyGenServer do
  def start(module, state) do
    {:ok, state} = module.init(state)
    {:ok, spawn(__MODULE__, :loop, [state, module])}
  end

  def loop(state, module) do
    state =
      receive do
        {:"$gen_cast", from, message} ->
          {:noreply, state} = module.handle_cast(message, state)
          state

        {:"$gen_call", {pid, reference} = from, message} ->
          {:reply, reply, state} = module.handle_call(message, from, state)
          send(pid, {reference, reply})
          state
      end

    loop(state, module)
  end

  def cast(pid, message) do
    send(pid, {:"$gen_cast", {self(), nil}, message})
    :ok
  end

  def call(pid, message) do
    send(pid, {:"$gen_call", {self(), nil}, message})

    receive do
      {_, response} -> response
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 在仙丹中使用基因服务器

现在，我们可以将键值存储中的便利功能切换到我们自己的 GenServer 实现上，以验证一切仍然正常。

```
defmodule KeyValue do
  def start do
    MyGenServer.start(KeyValue, %{})
  end

  def put(server, key, value) do
    MyGenServer.cast(server, {:put, key, value})
  end

  def get(server, key) do
    MyGenServer.call(server, {:get, key})
  end

  # Callbacks

  def init(state) do
    {:ok, state}
  end

  def handle_cast({:put, key, value}, state) do
    {:noreply, Map.put(state, key, value)}
  end

  def handle_call({:get, key}, _from, state) do
    {:reply, Map.fetch!(state, key), state}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> {:ok, pid} = KeyValue.start
{:ok, #PID<0.113.0>}
iex> KeyValue.put(pid, :foo, "bar")
:ok
iex> KeyValue.get(pid, :foo)
"bar" 
```

Enter fullscreen mode Exit fullscreen mode

这就结束了我们对 Elixir 的 GenServer 内部的探究。我们的例子实现了 Elixir 的内置功能`GenServer`的一部分。尽管我们的实现只涵盖了`GenServer`和`gen_server`在 Elixir 和 Erlang 中所做的部分工作，但它让我们得以一窥 GenServer 的内部工作方式。

*注*:我们自己的 GenServer 实现功能，但除了教育目的，不应该用于任何其他目的。而是使用 Elixir 内置的 GenServer。它是基于 Erlang 的`gen_server`，已经在生产应用中使用了几十年。

我们是否澄清了关于 Elixir 的基因服务器的一些困惑？我们很想知道你对这篇文章的看法，所以请不要犹豫[让我们知道](https://twitter.com/appsignal)。我们也很想知道你是否有任何想了解更多的长生不老药。