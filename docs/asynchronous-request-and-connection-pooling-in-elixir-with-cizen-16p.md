# 带有 Cizen 的 Elixir 中的异步请求和连接池

> 原文：<https://dev.to/ryo33/asynchronous-request-and-connection-pooling-in-elixir-with-cizen-16p>

## 简介

昨天，我真的很兴奋为这个帖子用 Cizen 编写了示例仙丹代码。Cizen 是一个用自动机，事件，(和乐趣)来构建应用程序的库！).使用 cizen，创建高度并发、可监控和可扩展的应用程序变得更加容易。我相信 Cizen 有潜力像 Elixir 一样让我们的软件开发更有趣。在这篇文章中，我们将构建一个 API 客户端，用 Cizen 实现异步请求和连接池。

## 自动机和事件

通过使用 Cizen，我们可以使用自动机(自动机的复数)来代替进程。自动机通过订阅和分派事件来相互通信，这与使用 pid 的直接消息传递的流程相反。

## API

在本文中，我们假设需要从 API 中获取每个类别的文章。API 的工作方式如下:

*   带有正文`"Elixir"`(类别名称)的 get_category_id 返回带有正文`"01"`(类别 id)的响应
*   带有正文`"01"`(类别 ID)的 get_articles 返回带有正文`["Let's Try Elixir", "Implement an API with Elixir"]`(文章列表)的响应

## 让我们做好准备

要做好准备，请运行以下命令:

```
$ mix new client
$ cd articles 
```

并将 Cizen 添加到依赖项:

```
# in mix.exs

  defp deps do
    [
      {:cizen, "~> 0.12.1"},
    ]
  end 
```

此外，您需要准备一个简单的 API 模拟:

```
# in a new file request.exs

defmodule API do
  @categories %{
    "Elixir" => "01",
    "Nature" => "02",
    "Cizen" => "03"
  }

  @articles %{
    "01" => ["Let's Try Elixir", "Implement an API with Elixir"],
    "02" => ["Beautiful Nature"],
    "03" => ["Getting Started with Cizen", "Asynchronous Request with Cizen"]
  }

  def get_category_id(text) do
    :timer.sleep(:rand.uniform(10) * 50) # Delay for an API call
    @categories[text]
  end

  def get_articles(category) do
    :timer.sleep(:rand.uniform(10) * 50) # Delay for an API call
    @articles[category]
  end
end 
```

## 单连接

让我们创建`Connection`自动机。`Connection`自动机重复接收`Connection.Request`事件，并为每个请求调度`Connection.Request.Reponse`事件。

首先，创建`Connection`模块并定义那些事件:

```
# append the following to request.exs

defmodule Connection do
  defmodule Request do
    defstruct [:connection_id, :type, :body]

    use Cizen.Request
    defresponse Response, :request_id do
      defstruct [:request_id, :body]
    end
  end
end 
```

`use Cizen.Request`使`Connection.Request`请求，`defresponse`定义其响应事件。

接下来，添加`Connection`自动机的实现。

```
# append the following into Connection module

use Cizen.Automaton

defstruct []

alias Cizen.{Event, Filter}
alias Cizen.Effects.{Dispatch, Receive, Subscribe}

def spawn(id, %__MODULE__{}) do
  # Subscribe Connection.Request events for this connection.
  perform id, %Subscribe{
    event_filter: Filter.new(
      fn %Event{body: %Connection.Request{connection_id: value}} -> value == id end
    )
  }

  :loop
end

def yield(id, :loop) do
  request_event = perform id, %Receive{}
  %Request{type: type, body: body} = request_event.body

  # Calls the API
  response = apply(API, type, [body])

  perform id, %Dispatch{
    body: %Connection.Request.Response{
      request_id: request_event.id,
      body: response
    }
  }

  :loop
end 
```

创建自动机时调用`spawn/2`，重复调用`yield/2`直到自动机完成。在`yield/2`，`Connection`逐个处理`Connection.Request`和`Connection.Request.Response`的急件。

现在，我们可以使用`Connection`向 API 发出请求。添加以下代码来运行请求。

```
# append the following to request.exs

defmodule Main do
  use Cizen.Effectful

  def main do
    alias Cizen.Effects.{Request, Start}

    connection_id = handle fn id ->
      perform id, %Start{saga: %Connection{}}
    end

    ["Elixir", "Nature", "Cizen"]
    |> Enum.map(fn category ->
      fn ->
        handle fn id ->
          # Request to get a category ID
          response_event = perform id, %Request{body: %Connection.Request{
            connection_id: connection_id,
            type: :get_category_id,
            body: category
          }}
          %Connection.Request.Response{body: category_id} = response_event.body

          # Request to get articles
          response_event = perform id, %Request{body: %Connection.Request{
            connection_id: connection_id,
            type: :get_articles,
            body: category_id
          }}
          %Connection.Request.Response{body: articles} = response_event.body

          # Returns the articles
          articles
        end
      end
    end)
    |> Enum.map(&Task.async(&1))
    |> Enum.map(&Task.await(&1, :infinity))
    |> Enum.map(&IO.inspect(&1))
  end
end

# Run
Main.main() 
```

`Main.main/0`尝试获取“仙丹”、“自然”、“此曾”三个类别的文章，使用`Task.async`和`Task.await`异步运行这三个过程。

现在，我们可以用
运行`request.exs`

```
$ mix run request.exs 
```

它输出:

```
["Let's Try Elixir", "Implement an API with Elixir"]
["Beautiful Nature"]
["Getting Started with Cizen", "Asynchronous Request with Cizen"] 
```

## 请求记录器

在使用连接池之前，我们应该创建一个请求记录器来监控复杂的异步请求。

添加`RequestLogger` :

```
# append the following above Main module in request.exs

defmodule RequestLogger do
  use Cizen.Automaton
  alias Cizen.{Event, Filter}
  alias Cizen.Effects.{All, Receive, Subscribe}
  alias Cizen.StartSaga

  defstruct []

  def spawn(id, %__MODULE__{}) do
    perform id, %All{effects: [
      %Subscribe{
        event_filter: Filter.new(
          fn %Event{body: %StartSaga{saga: %Connection{}}} -> true end
        )
      },
      %Subscribe{
        event_filter: Filter.new(
          fn %Event{body: %Connection.Request{}} -> true end
        )
      },
      %Subscribe{
        event_filter: Filter.new(
          fn %Event{body: %Connection.Request.Response{}} -> true end
        )
      },
    ]}

    %{
      connections: %{}, # connection ID => connection number
      requests: %{} # request ID => connection number
    }
  end

  defp tabs(n), do: Stream.cycle([" "]) |> Enum.take(20*n) |> Enum.join()
  def yield(id, state) do
    event = perform id, %Receive{}
    case event.body do
      %StartSaga{id: connection_id} ->
        put_in(state.connections[connection_id], Map.size(state.connections))
      %Connection.Request{connection_id: connection_id} = request ->
        connection_number = state.connections[connection_id]
        IO.puts("""
        #{tabs(connection_number)}request
        #{tabs(connection_number)}#{request.type}
        #{tabs(connection_number)}body: #{request.body}
        """)
        request_id = event.id
        put_in(state.requests[request_id], connection_number)
      %Connection.Request.Response{request_id: request_id} = response ->
        {connection_number, state} = pop_in(state.requests[request_id])
        IO.puts("""
        #{tabs(connection_number)}response
        #{tabs(connection_number)}#{inspect response.body}
        """)
        state
    end
  end
end 
```

这看起来有点困难，但它只是将`Connection.Request`事件和`Connection.Request.Response`事件记录到 stdout。此外，它使用水平轴显示使用的连接，使用垂直轴显示时间顺序。

要使用记录器，请更新`Main.main/0`；

*   在开始连接的线上添加`perform id, %Start{saga: %RequestLogger{}}`:

```
 connection_id = handle fn id ->
      perform id, %Start{saga: %RequestLogger{}} # here
      perform id, %Start{saga: %Connection{}}
    end 
```

*   并且，移除`|> Enum.map(&IO.inspect(&1))`以避免与记录输出混淆:

```
 |> Enum.map(&Task.async(&1))
    |> Enum.map(&Task.await(&1, :infinity))
    # |> Enum.map(&IO.inspect(&1)) removed 
```

现在，`$ mix run request.exs`输出如下所示:

```
request
get_category_id
body: Nature

request
get_category_id
body: Cizen

request
get_category_id
body: Elixir

response
"02"

request
get_articles
body: 02

response
"03"

request
get_articles
body: 03

response
"01"

request
get_articles
body: 01

response
["Beautiful Nature"]

response
["Getting Started with Cizen", "Asynchronous Request with Cizen"]

response
["Let's Try Elixir", "Implement an API with Elixir"] 
```

它显示了`Connection.Request`事件是异步调度的，连接按顺序处理它们。在下一节中，我们将多个连接与连接池一起使用。

## 连接池

现在，让我们创建`ConnectionPool`自动机。它将推送的连接排队，并通过借用事件借给它们。

首先定义`Connection`模块，定义相关事件:

```
# append the following above Main module in request.exs

defmodule ConnectionPool do
  defmodule BorrowConnection do
    defstruct []
    use Cizen.Request
    defresponse Lend, :borrow_id do
      defstruct [:borrow_id, :connection_id]
    end
  end

  defmodule PushConnection, do: defstruct [:connection_id]
end 
```

`ConnectionPool.BorrowConnection`是请求事件，`ConnectionPool.BorrowConnection.Lend`是响应事件。

`ConnectionPool.PushConnection`是注册新连接或归还借用连接的事件。

接下来，添加`ConnectionPool`自动机的实现。

```
# append the following into the ConnectionPool module

use Cizen.Automaton
alias Cizen.{Event, Filter}
defstruct []

alias Cizen.Effects.{All, Dispatch, Receive, Subscribe}

def spawn(id, %__MODULE__{}) do
  perform id, %All{effects: [
    %Subscribe{
      event_filter: Filter.new(fn %Event{body: %BorrowConnection{}} -> true end)
    },
    %Subscribe{
      event_filter: Filter.new(fn %Event{body: %PushConnection{}} -> true end)
    }
  ]}

  :loop
end

def yield(id, :loop) do
  borrow_event = perform id, %Receive{
    event_filter: Filter.new(fn %Event{body: %BorrowConnection{}} -> true end)
  }

  push_event = perform id, %Receive{
    event_filter: Filter.new(fn %Event{body: %PushConnection{}} -> true end)
  }
  %PushConnection{connection_id: connection_id} = push_event.body

  perform id, %Dispatch{
    body: %BorrowConnection.Lend{
      borrow_id: borrow_event.id,
      connection_id: connection_id
    }
  }

  :loop
end 
```

最后，更新`Main.main/0`以使用带有`ConnectionPool` :
的多个连接

```
# replace Main.main/0 with the following

def main do
  alias Cizen.Effects.{Dispatch, Request, Start}

  handle fn id ->
    perform id, %Start{saga: %RequestLogger{}}
    perform id, %Start{saga: %ConnectionPool{}}
    # Starts 3 connections
    for _ <- 1..3 do
      connection_id = perform id, %Start{saga: %Connection{}}
      perform id, %Dispatch{
        body: %ConnectionPool.PushConnection{connection_id: connection_id}
      }
    end
  end

  ["Elixir", "Nature", "Cizen"]
  |> Enum.map(fn category ->
    fn ->
      handle fn id ->
        # Borrow a connection
        lend_event = perform id, %Request{body: %ConnectionPool.BorrowConnection{}}
        connection_id = lend_event.body.connection_id
        # Request
        response_event = perform id, %Request{body: %Connection.Request{
          connection_id: connection_id,
          type: :get_category_id,
          body: category
        }}
        %Connection.Request.Response{body: category_id} = response_event.body
        # Return the connection
        perform id, %Dispatch{
          body: %ConnectionPool.PushConnection{connection_id: connection_id}
        }

        # Borrow a connection
        lend_event = perform id, %Request{body: %ConnectionPool.BorrowConnection{}}
        connection_id = lend_event.body.connection_id
        # Request
        response_event = perform id, %Request{body: %Connection.Request{
          connection_id: connection_id,
          type: :get_articles,
          body: category_id
        }}
        %Connection.Request.Response{body: articles} = response_event.body
        # Return the connection
        perform id, %Dispatch{
          body: %ConnectionPool.PushConnection{connection_id: connection_id}
        }

        articles
      end
    end
  end)
  |> Enum.map(&Task.async(&1))
  |> Enum.map(&Task.await(&1))
end 
```

现在，`$ mix run request.exs`输出如下所示:

```
request
get_category_id
body: Cizen

                    request
                    get_category_id
                    body: Elixir

                                        request
                                        get_category_id
                                        body: Nature

response
"03"

request
get_articles
body: 03

                    response
                    "01"

                    request
                    get_articles
                    body: 01

                                        response
                                        "02"

                                        request
                                        get_articles
                                        body: 02

                                        response
                                        ["Beautiful Nature"]

                    response
                    ["Let's Try Elixir", "Implement an API with Elixir"]

response
["Getting Started with Cizen", "Asynchronous Request with Cizen"] 
```

我们成功了！这些请求由 3 个连接同时处理。

## 代码可读性

我假设你注意到了上面的`Main.main/0`代码太复杂了，可能是因为我们有两步`perform/2`用于启动连接，三步用于请求。在这样的情况下`Chain`效应和`Map`效应会帮助我们使我们的代码清晰易懂。

首先，添加`Main.start_connection/0` :

```
# add the following into the Main module

defp start_connection do
  alias Cizen.Effects.{Chain, Dispatch, Start}
  %Chain{effects: [
    %Start{saga: %Connection{}},
    fn connection_id ->
      %Dispatch{
        body: %ConnectionPool.PushConnection{connection_id: connection_id}
      }
    end
  ]}
end 
```

它加入了两个效果，一个用于启动连接，另一个用于分派`ConnectionPool.PushConnection`。

接下来，添加`Main.request/2` :

```
# add the following into the Main module

defp request(type, body) do
  alias Cizen.Effects.{Chain, Dispatch, Map, Request}
  effect = %Chain{effects: [
    %Request{body: %ConnectionPool.BorrowConnection{}},
    fn lend_event ->
      connection_id = lend_event.body.connection_id
      %Request{body: %Connection.Request{
        connection_id: connection_id,
        type: type,
        body: body
      }}
    end,
    fn lend_event, _ ->
      connection_id = lend_event.body.connection_id
      %Dispatch{
        body: %ConnectionPool.PushConnection{connection_id: connection_id}
      }
    end
  ]}
  %Map{
    effect: effect,
    transform: fn [_, response_event, _] -> response_event end
  }
end 
```

它加入了三个效果，一个用于借用连接，另一个用于请求，另一个用于返回连接。之后，它还会转换结果，以便`perform id, request(type, body)`会返回请求的响应。

最后，更新`Main.main/0`来使用它们。

```
# replace Main.main/0 with the following

def main do
  alias Cizen.Effects.Start

  handle fn id ->
    perform id, %Start{saga: %RequestLogger{}}
    perform id, %Start{saga: %ConnectionPool{}}
    # Starts 3 connections
    for _ <- 1..3 do
      perform id, start_connection()
    end
  end

  ["Elixir", "Nature", "Cizen"]
  |> Enum.map(fn category ->
    fn ->
      handle fn id ->
        response_event = perform id, request(:get_category_id, category)
        %Connection.Request.Response{body: category_id} = response_event.body

        response_event = perform id, request(:get_articles, category_id)
        %Connection.Request.Response{body: articles} = response_event.body

        articles
      end
    end
  end)
  |> Enum.map(&Task.async(&1))
  |> Enum.map(&Task.await(&1, :infinity))
end 
```

现在，我们的代码比旧代码可读性更好。

## 链接

*   [这篇文章的完整代码](https://gitlab.com/ryo33/cizen-request-example)
*   [吉塔实验室上的禅宗](https://gitlab.com/cizen/cizen)
*   [Cizen on Hex.pm](https://hex.pm/packages/cizen)
*   [Cizen 的在线文档](https://hexdocs.pm/cizen)