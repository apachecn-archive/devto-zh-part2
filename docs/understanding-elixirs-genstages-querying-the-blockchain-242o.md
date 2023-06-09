# 理解灵药的起源:质疑区块链

> 原文：<https://dev.to/appsignal/understanding-elixirs-genstages-querying-the-blockchain-242o>

在本期的《炼丹术》中，我们将深入了解《炼丹术》的基因阶段模块。在这个过程中，我们将解释背压，并编写一个 Genstage 来查询区块链。让我们从讨论使用 GenStage 如何解决缓冲问题开始。

# 什么是 GenStage？

假设您正在使用来自外部来源的数据。该源可以是任何“可流式传输”的东西，比如逐行读取文件、数据库中的表，甚至是对第三方 API 的一系列请求。

在这种情况下，您需要将数据流式传输到您的系统中，并可能对每个数据点进行一些处理，通常使用一个缓冲区读入一些项，处理整个批处理，然后将一个新的集合提取到缓冲区中。我记得，从我学习 C/C++的时候起，这将是一种常见的，尽管可以说是幼稚的做事方式。

使用这种方法，您可能会遇到两个问题之一:缓冲区可能太小，或者缓冲区可能太大。

1.  **缓冲区太小**
    如果一次读取的项目太少，就会出现这种情况。由于您在阅读和处理项目之间来回切换，任务切换会带来性能损失。在读取文件的例子中，您的硬件或操作系统可能会读取比您实际请求的更多的数据，从而导致次优的性能，此外还必须在以后获取文件的相同部分。

2.  **缓冲区过大**
    在这种情况下，您对数据源的要求过高。您最终可能会造成瓶颈(例如，必须等待硬盘读取您请求的所有内容)，或者无法以高效的方式处理所有数据。如果你听说过[缓冲区溢出](https://en.wikipedia.org/wiki/Buffer_overflow)(一个常见的性能和安全问题)，这就是它。您正在阅读的内容超过了您的系统所能跟上的，导致了各种各样的问题，从性能到实际的失败。

## 解决方法:背压

术语*背压*是指系统建立输入，然后一旦缓冲区满了就停止接收新数据，当系统准备好处理它时再恢复接收的行为。

这是 Elixir 的 GenStage 背后的核心思想。

# 基因阶段

GenStage 是建立在 GenServer 之上的一个抽象，提供了一种简单的方法来创建生产者/消费者架构，同时自动管理背压的概念。

在 GenStage 中，您创建了多个生产者和消费者的管道。生产者生成数据点，或者从源中读取数据点，然后将它们传递给管道。然后，它们可以通过一个或多个消费者发送，这些消费者将完成您需要完成的任何处理。

背压的概念应用于在生成器中创建项目的方式。当管道准备好接收新的项目时，生产者的`handle_demand/2`函数被调用，请求特定数量的项目。

请求的数量由内部决定(尽管您可以指定一个最大值)，只要管道中有空间，就会调用该函数。如果项目处理时间太长，生产者最终会闲置一段时间，从而减轻系统的压力。

# 用例

作为一个 GenStage 有用的例子，让我们考虑从外部数据源读取数据块。在这种情况下，我们将使用[以太坊](https://www.ethereum.org/)区块链，因为它非常符合这个概念。

区块链由一系列块组成，每个块包含多个事务。如果我们想要处理整个区块链(例如，查找涉及给定地址的所有事务，或者在与您的应用程序集成时持续监听它)，GenStage 是一个完美的选择。

在这种情况下，每个块可以被视为单个数据项。让我们看看这是如何实现的。

## 查询区块链

我们将使用 [Infura 的](https://infura.io/)公共 HTTP API 与以太坊区块链进行交互。让我们从构建其接口的包装器开始。为此，我将使用[特斯拉](https://github.com/teamon/tesla)库(这只是个人偏好，请随意选择)。

```
defmodule EthSync.Infura do
  use Tesla

  plug(Tesla.Middleware.BaseUrl, "https://ropsten.infura.io/")

  # encode/decode body as json
  # Infura doesn't set the "content-type" header to "application/json"
  # so we need to tell Tesla that we want text/plain requests to be decoded as well
  plug(Tesla.Middleware.JSON, decode_content_types: ["text/plain; charset=utf-8"])

  @doc "Get an entire block"
  def get_block(number) do
    case call(:eth_getBlockByNumber, [to_hex(number), true]) do
      {:ok, nil} ->
        {:error, :block_not_found}

      error ->
        error
    end
  end

  @doc "Sends a JSON-RPC call to the server"
  defp call(method, params \\ []) do
    case post("", %{jsonrpc: "2.0", id: "call_id", method: method, params: params}) do
      {:ok, %Tesla.Env{status: 200, body: %{"result" => result}}} ->
        {:ok, result}

      {:error, _} = error ->
        error
    end
  end

  @doc "Converts integer values to hex strings"
  def to_hex(decimal), do: "0x" <> Integer.to_string(decimal, 16)
end 
```

为此我们只需要一个端点:获取块的数据，给出它在链上的索引。块号必须以十六进制格式给出，所以我们还需要一个助手方法来处理转换。

我们可以通过`iex` :
来验证这是否有效

```
iex(1)> EthSync.Infura.get_block(1)
{:ok,
 %{
   "number" => "0x1",
   "transactions" => [],
   # ...
 }
}

iex(2)> EthSync.Infura.get_block(1_000_000_000_000)
{:error, :block_not_found} 
```

## 建筑生产者

我们的生产者将是一个负责获取以太坊块的进程。

```
defmodule EthSync.Producer2 do
  alias EthSync.Infura
  use GenStage

  def init(_) do
    {:producer, 1}
  end

  def handle_demand(demand, next_block) when demand > 0 do
    IO.puts("Demanding #{demand}")

    blocks =
      next_block..(next_block - 1 + demand)
      |> Enum.map(fn n ->
        IO.puts("Fetching block #{n}")
        Infura.get_block(n)
      end)

    {:noreply, blocks, next_block + length(blocks)}
  end
end 
```

## 建筑消费者

消费者将接收块列表，然后处理它们。在这个例子中，我们将使用`:timer.sleep/1`来模拟处理时间，因为我们没有做任何实际的工作。请记住，收到的块列表不一定与生成器中发送的相同。项目可以根据 GenStage 的内部规则进行缓冲。也可能发生这样的情况，你有多个消费者，商品在他们之间被分割。

```
defmodule EthSync.Consumer do
  alias EthSync.Infura
  use GenStage

  def init(_) do
    {:consumer, nil}
  end

  def handle_events(blocks, _from, state) do
    blocks
    |> Enum.each(fn
      {:ok, %{"number" => n}} ->
        IO.puts("Received block #{n}")
        :timer.sleep(1_000)
    end)

    {:noreply, [], state}
  end
end 
```

## 把一切都布线起来

要启动管道，我们需要为我们的生产者和消费者启动流程，然后将它们链接在一起，这样前者生产的产品就可以发送给后者:

```
iex> {:ok, producer} = GenStage.start_link(Producer2, [])
{:ok, #PID<0.160.0>}

iex> {:ok, consumer} = GenStage.start_link(Consumer2, [])
{:ok, #PID<0.162.0>}

iex> GenStage.sync_subscribe(consumer, to: producer, max_demand: 3)
{:ok, #Reference<0.2486793675.579338241.116277>}
Demanding 3
Received block 0x1
Received block 0x2
Received block 0x3
Demanding 1
Received block 0x4
Received block 0x5
Demanding 1 
```

请注意，即使我们一开始就启动了生产者，它也只是在我们将消费者连接到它时才开始获取块。那是因为在那之前没有需求。此外，即使我们指定了`max_demand: 3`，那也不一定是一直请求的数量。因为我们只有一个消费者，处理每个块需要 1 秒钟，所以 GenStage 足够聪明，不会用太多的块溢出它。它会根据需要调整事件的数量。

## 消耗了清凉？

通过生产者、消费者以及将他们连接在一起，我们创建了一个基本的 GenServer 示例。我们喜欢 GenStages 如何提供一种优雅的方式来创建自动管理背压的生产者/消费者架构。

*本文由客座作者[米盖尔·帕哈斯](http://naps62.github.io/)撰写。米盖尔是专业的总工程师[@子视觉](https://twitter.com/subvisual)，组织 [@rubyconfpt](https://twitter.com/rubyconfpt) 和 [@MirrorConf](https://twitter.com/mirrorconf) 。*

如果你想阅读更多的炼丹术，一定要订阅邮件列表！