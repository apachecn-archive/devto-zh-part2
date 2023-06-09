# Elixir TCP 服务器和客户端示例

> 原文：<https://dev.to/oivoodoo/elixir-tcp-server-example--foo>

这篇文章部分是关于未完成的项目 [observerio](//github.com/observerio/observer-server) 的，我觉得这些天我没有足够的空闲时间去完成。

我一直致力于该项目，以获得游戏的不同策略的实时测试，结果这个想法进入了项目，该项目具有 http api，tcp 服务器，ember 仪表板和基于 go 的核心库(使用 xgo 编译来支持跨平台)。

打算分享几个对某人有用的例子。

如果有人问我关于选择的语言，我无法解释为什么我决定在后端使用它。大概是我之前在创业应用 [musicfeed](http://musicfeed.co/) 的经历给了我灵感。显然对我来说，Elixir 有非常酷的语法、社区和文档。但是，如果您决定将 gen_server 作为第一语言来学习，那么它和其他工具的概念就很难理解了。如果有人告诉你不需要学习 erlang，那么当 Elixir 库的数量增长如此之快时，这种说法可能是部分正确的。

我已经决定使用 tcp 服务器和客户端通过内部 sdk 发送来自手机游戏的变量和日志。当我们收到变量或日志时，tcp 服务器通过 pubsub(网关)向订阅的 websocket 处理程序发布消息。如果 websocket 已连接(活动客户端)，它会自动将更改发布到 ember dashboard。或许这听起来更像是一个游戏而不是制作的想法，但我这样做只是为了好玩:)

让我们回顾一下 tcp 服务器实现(如果您看到可能的问题，请随意应用更改，它是开源的):

[tcp.ex](https://github.com/observerio/observer-server/blob/master/lib/web/tcp.ex)

*   定义 tcp 服务器和客户端的管理程序(应该用于通过 pubsub 创建 websocket 和 tcp 客户端之间的通信)

```
 defmodule Web.Tcp.ServerSupervisor do
  use Supervisor

  def start_link do
    Supervisor.start_link(__MODULE__, [], name: :tcp_server_supervisor)
  end

  def init(_) do
    children = [
      worker(Web.Tcp.Server, [])
    ]

    supervise(children, strategy: :one_for_one)
  end
end

defmodule Web.Tcp.ClientSupervisor do
  use Supervisor

  def start_link do
    Supervisor.start_link(__MODULE__, [], name: :tcp_client_supervisor)
  end

  def start_client(token) do
    Supervisor.start_child(:tcp_client_supervisor, [token])
  end

  def init(_) do
    children = [
      worker(Web.Tcp.Client, [])
    ]

    # We also changed the `strategy` to `simple_one_for_one`.
    # With this strategy, we define just a "template" for a child,
    # no process is started during the Supervisor initialization, just
    # when we call `start_child/2`
    supervise(children, strategy: :simple_one_for_one)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

*   定义服务器工作进程，并将模块名`Web.Tcp.Handler`作为入口点传递给连接的客户端

```
defmodule Web.Tcp.Server do
  require Logger

  def start_link do
    Logger.debug("[tcp] starting server on port :#{_port()}")
    opts = [port: _port()]
    {:ok, _} = :ranch.start_listener(
      :tcp, _acceptors_size(), :ranch_tcp, opts, Web.Tcp.Handler, [])
  end

  def _port do
    String.to_integer Application.get_env(:web, :tcp_port)
  end

  def _acceptors_size do
    String.to_integer Application.get_env(:web, :tcp_acceptors_size)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

*   定义将作为已连接 tcp 客户端的工作器的模块。在 init 上，它将通过 api 键(每个用户)订阅 pubsub 通道。它将允许从 pubsub 接收消息并将其发送回 tcp 客户端套接字。

```
defmodule Web.Tcp.Client do
  require Logger
  require Poison

  alias Web.Pubsub

  def start_link(token) do
    GenServer.start_link(__MODULE__, token, name: String.to_atom(token))
  end

  def init(token) do
    Pubsub.subscribe("#{token}:vars:callback")
    {:ok, %{token: token, messages: []}}
  end

  def handle_info(%{vars: vars}, %{token: token, messages: messages} = state) do
    Logger.debug("[tcp.client] received message: #{inspect(vars)}")
    message = _pack(token, "vars", vars)
    Logger.debug("[tcp.client] packed message: #{inspect(message)}")

    messages = messages ++ [message]

    Logger.debug("[tcp.client] begin send message: #{inspect(messages)}")
    state = token |> _get_socket |> _send_back(messages, state)
    Logger.debug("[tcp.client] done send message: #{inspect(messages)}")

    Logger.debug("[tcp.client] messages: #{inspect(messages)}")

    {:noreply, state}
  end

  def terminate(reason, status) do
    Logger.debug("[tcp.client] reason: #{inspect(reason)}, status: #{inspect(status)}")
    :ok
  end

  defp _send_back({:ok, socket}, messages, state) do
    :ok = _send(socket, messages)
    %{state | messages: []}
  end
  defp _send_back(:enqueue, messages, state) do
    %{state | messages: messages}
  end

  defp _send(s, []), do: :ok
  defp _send({socket, transport} = s, [message | messages]) do
    transport.send(socket, message)
    _send(s, messages)
  end

  def _pack(token, "vars", vars) do
    vars = vars
    |> Poison.encode!
    |> Base.encode64

    "v:#{token}:#{vars}\n"
  end

  defp _get_socket(token) do
    Logger.debug("[tcp.socket] search for socket, transport by token: #{inspect(token)}")
    response = case Registry.lookup(Registry.Sockets, token) do
      [{_, socket}] -> {:ok, socket}
      [] -> :enqueue
    end
    Logger.debug("[tcp.client] _get_socket: #{inspect(response)}")
    response
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

*   接收端的 tcp 处理程序将在注册表中注册套接字，并启动新的客户端工作器通过 api 键订阅 pubsub 通道，并在 pubsub 和 tcp 客户端之间进行通信。

```
defmodule Web.Tcp.Handler do
  require Logger

  alias Web.Pubsub

  @moduledoc """
  `Handler` is waiting lines separated by \n new line, in case if handler don't
  see new line it starts to accumulate data until it receives new line.

  `Registry.Sockets` contains api_key -> socket records for easy back communication
  from dashboard page to tcp clients.
  """

  def start_link(ref, socket, transport, opts) do
    pid = spawn_link(__MODULE__, :init, [ref, socket, transport, opts])
    {:ok, pid}
  end

  def init(ref, socket, transport, _opts = []) do
    :ok = :ranch.accept_ack(ref)

    case transport.peername(socket) do
      {:ok, _peer} -> loop(socket, transport, "")
      {:error, reason} -> Logger.error("[tcp.handler] init receive error reason: #{inspect(reason)}")
    end
  end

  @timeout 5_000

  def loop(socket, transport, acc) do
    # Don't flood messages of transport, receive once and leave the remaining
    # data in socket until we run recv again.
    transport.setopts(socket, [active: :once])

    # before to proceed with receive block on messages we should call
    # once transport.messages() to ping ranch
    {ok, closed, error} = transport.messages()

    receive do
      {ok, socket, data} ->
        Logger.debug("[tcp.handler] received data: #{inspect(data)}")

        acc <> data
        |> String.split("\n")
        |> Enum.map(&(String.trim(&1)))
        |> _process(socket, transport)

        loop(socket, transport, "")
      {closed, socket} ->
        Logger.debug("[tcp.handler] closed socket: #{inspect(socket)}")
      {error, socket, reason} ->
        Logger.error("[tcp.handler] socket: #{inspect(socket)}, closed becaose of the error reason: #{inspect(reason)}")
      {:error, error} ->
        Logger.error("[tcp.handler] error: #{inspect(error)}")
      {'EXIT', parent, reason} ->
        Logger.error("[tcp.handler] exit parent reason: #{inspect(reason)}")
        Process.exit(self(), :kill)
      message ->
        Logger.debug("[tcp.handler] message on receive block: #{inspect(message)}")
    end
  end

  defp _kill(), do: Process.exit(self(), :kill)

  defp _process([], socket, transport), do: loop(socket, transport, "")
  defp _process([""], socket, transport), do: loop(socket, transport, "")
  defp _process([line, ""], socket, transport) do
    _protocol(line, socket, transport)
    loop(socket, transport, "")
  end
  defp _process([line], socket, transport), do: loop(socket, transport, line)
  defp _process([line | lines], socket, transport) do
    _protocol(line, socket, transport)
    _process(lines, socket, transport)
  end

  defp _protocol(line, socket, transport) do
    Logger.debug("[_protocol] line: #{line}")

    case line |> Web.Tcp.Protocol.process do
      {:verified, api_key} ->
        _register_socket(api_key, socket, transport)

        Web.Tcp.ClientSupervisor.start_client(api_key)

        Logger.debug("[tcp.server] transport should respond with OK")

        case transport.send(socket, "OK\n") do
          {:error, reason} ->
            Logger.error(inspect(reason))
          _ ->
        end
      {:error, reason} ->
        Logger.error("[tcp] #{inspect(reason)}")
      :error ->
        Logger.error("error on processing: #{inspect(line)}")
      _ ->
    end
  end

  def _register_socket(api_key, socket, transport) do
    Logger.debug("[tcp.handler] _register_socket token: #{api_key}")

    case Registry.register(Registry.Sockets, api_key, {socket, transport}) do
      {:error, {:already_registered, _pid}} ->
        Registry.update_value(Registry.Sockets, api_key, fn (_) -> {socket, transport} end)
      {:error, reason} ->
        Logger.error("[tcp] reason: #{inspect(reason)}")
      _ ->
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

*   `Web.Tcp.Protocol`定义通过 tcp 客户端传递的可能命令的解析器和处理器。

示例:

```
l:logs:<base64 json messages>
i:vars:<base64 json variables>
v:api_key - client should pass api key to verify that we have the registered client 
```

Enter fullscreen mode Exit fullscreen mode

```
defmodule Web.Tcp.Protocol do
  require Logger
  require Poison

  alias Web.Gateway
  alias Web.Db.Users

  @moduledoc """
    Server messages:

      - `l:logs`
        logs - should come as json array and encoded base64

      - `i:vars`
        vars - should come as json dictionary and encoded by base64

      - `v:api_key`
        api_key - should verify key using our registry

    Client messages:
      - `i:s:name:value` - var set by name value inside of app
  """
  def process("l:" <> <<api_key :: bytes-size(12)>> <> ":" <> logs) do
    Logger.debug("[protocol] api_key: #{api_key}, logs: #{inspect(logs)}")
    logs
    |> Base.decode64!
    |> Poison.decode!
    |> Gateway.logs(api_key)
  end

  def process("i:" <> <<api_key :: bytes-size(12)>> <> ":" <> vars) do
    Logger.debug("[protocol] api_key: #{api_key}, vars: #{inspect(vars)}")
    vars
    |> Base.decode64!
    |> Poison.decode!
    |> Gateway.vars(api_key)
  end

  def process("v:" <> <<api_key :: bytes-size(12)>>) do
    if Users.verify_key(api_key) do
      {:verified, api_key}
    else
      {:error, "not registered user with api_key: #{api_key}"}
    end
  end

  def process(_), do: :error
end 
```

Enter fullscreen mode Exit fullscreen mode

*   最后是用于审查基本预期行为的测试用例 [tcp_test.ex](https://github.com/observerio/observer-server/blob/master/test/tcp_test.exs) :

```
defmodule Web.TcpTest do
  use ExUnit.Case
  doctest Web.Tcp

  alias Web.Db.Users

  require RedisPoolex
  require Logger
  require Poison
  require Tirexs.Query

  alias RedisPoolex, as: Redis

  setup do
    Redis.query(["FLUSHDB"])

    {:ok, api_key} = Users.register(%{email: "user1@example.com", password: "12345678"})

    port = Application.get_env(:web, :tcp_port)
    host = "127.0.0.1" |> String.to_char_list

    {:ok, socket} = :gen_tcp.connect(host, port, [active: false])
    {:ok, socket: socket, api_key: api_key}
  end

  test "should register user session", %{socket: socket, api_key: api_key} do
    :ok = :gen_tcp.send(socket, "v:" <> api_key <> "\n")
    {:ok, reply} = :gen_tcp.recv(socket, 0, 1000)
    assert reply == 'OK'
  end

  test "should bulk insert logs on tcp request", %{socket: socket, api_key: api_key} do
    :ok = :gen_tcp.send(socket, "v:" <> api_key <> "\n")
    :ok = :gen_tcp.send(socket, "l:" <> api_key <> ":" <> ([%{message: "testing1", timestamp: 123123123}, %{message: "testing2", timestamp: 123123123}] |> Poison.encode! |> Base.encode64) <> "\n")

    :timer.sleep(2000)

    assert {:ok, 200, %{hits: %{hits: hits}} = response} = Tirexs.Query.create_resource([index: "logs-#{api_key}", search: ""])
    assert Enum.count(hits) == 2
  end

  # TODO: add marker support to create separate sessions on multiple devices.
  # we could have separate dashboards for the different devices.
  test "should store vars on tcp request", %{socket: socket, api_key: api_key} do
    :ok = :gen_tcp.send(socket, "v:" <> api_key <> "\n")
    :ok = :gen_tcp.send(socket, "i:" <> api_key <> ":" <> ([%{name: "testing1", type: "string", value: "example"}, %{name: "testing2", type: "integer", value: "-1"}] |> Poison.encode! |> Base.encode64) <> "\n")

    :timer.sleep(2000)

    vars = Redis.query ["HKEYS", "#{api_key}:vs"]
    assert Enum.count(vars) == 4
    assert vars == ["testing1:type", "testing1:value", "testing2:type", "testing2:value"]
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在接下来的例子中，我将分享 Pubsub 和 Gateway 实现的模块，这很容易复习。

一切都是为了好玩，在使用相同的方法时，应该非常小心，不要每天练习长生不老药，结果在某些地方可能是错误的方向，可以改进。

感谢您的阅读！