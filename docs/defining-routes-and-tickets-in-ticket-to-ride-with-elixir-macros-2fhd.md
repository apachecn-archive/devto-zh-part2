# 使用 Elixir Macros 在票证中定义路线和票证

> 原文：<https://dev.to/alakra/defining-routes-and-tickets-in-ticket-to-ride-with-elixir-macros-2fhd>

不久前，我决定使用宏来定义路线和票，因为我当时设想的数据结构，我实现了乘坐灵药的票。

宏的用法如下所示:

```
defmodule TtrCore.Board.Routes do
  use TtrCore.Board.Router

  defroute Atlanta, to: Charleston, distance: 2
  defroute Atlanta, to: Miami, distance: 5, trains: [:passenger]
  defroute Atlanta, to: Raleigh, distance: 2, trains: [:any, :any]
  defroute Atlanta, to: Nashville, distance: 1
  defroute Atlanta, to: New.Orleans, distance: 4, trains: [:box, :tanker]
  defroute Boston, to: Montreal, distance: 2, trains: [:any, :any]
  defroute Boston, to: New.York, distance: 1, trains: [:coal, :box]

  # And more routes...
end 
```

我意识到我的实现对于游戏所需的计算来说太复杂了，所以我放弃了这个数据结构，取而代之的是一个更简单的数据结构。

以下是原文:

```
defmodule TicketToRide.Router do
  alias TicketToRide.{NoOriginFoundError,
                      NoDestinationSpecifiedError,
                      NoDistanceSpecifiedError}

  defmodule Origin do
    defstruct [
      name: nil,
      destinations: %{}
    ]
  end

  defmodule Destination do
    defstruct [
      name: nil,
      distance: 0,
      trains: MapSet.new
    ]
  end

  defmacro __using__ (_opts) do
    quote do
      import unquote( __MODULE__ )
      Module.register_attribute __MODULE__ , :origins, accumulate: false, persist: false
      Module.put_attribute __MODULE__ , :origins, %{}
      @before_compile unquote( __MODULE__ )
    end
  end

  # API

  defmacro defroute(name, args \\ []) do
    quote do
      @origins Map.merge(@origins, update_origins(@origins, unquote(name), unquote(args)))
    end
  end

  defmacro __before_compile__ (_env) do
    quote do
      def all, do: @origins
      def get(name), do: Map.fetch(@origins, name)
    end
  end

  def update_origins(origins, name, args) do
    source = get(origins, name, autocreate: true)

    destination = get(origins, args[:to], autocreate: true)
    destination_options = [
      to: name,
      distance: args[:distance],
      trains: args[:trains]
    ]

    %{ name => update(source, args),
       args[:to] => update(destination, destination_options) }
  end

  # Private

  defp get(origins, name, opts \\ [autocreate: false]) do
    case Map.fetch(origins, name) do
      {:ok, origin} -> origin
      :error -> get_on_error(name, opts[:autocreate])
    end
  end

  defp get_on_error(name, autocreate) do
    if autocreate do
      %Origin{name: name}
    else
      raise NoOriginFoundError, name: name
    end
  end

  defp update(origin, args) do
    destination_name = extract_destination_name(origin, args)
    trains = extract_trains(args)

    destination = update_destination(origin, destination_name, trains, args)
    destinations = Map.put(origin.destinations, destination_name, destination)

    %{origin | destinations: destinations}
  end

  defp update_destination(origin, destination, trains, args) do
    case Map.fetch(origin.destinations, destination) do
      {:ok, dest} ->
        %{dest | trains: MapSet.union(dest.trains, trains)}
      :error ->
        distance = extract_distance(origin, args)
        %Destination{name: destination, distance: distance, trains: trains}
    end
  end

  defp extract_destination_name(origin, args) do
    case args[:to] do
      nil -> raise NoDestinationSpecifiedError, from: origin.name
      destination -> destination
    end
  end

  defp extract_distance(origin, args) do
    case args[:distance] do
      nil -> raise NoDistanceSpecifiedError, from: origin.name, to: args[:to]
      distance -> distance
    end
  end

  defp extract_trains(args) do
    case args[:trains] do
      nil -> MapSet.new([:any])
      trains -> MapSet.new(trains)
    end
  end
end 
```

以及新的:

```
defmodule TtrCore.Board.Router do
  @moduledoc false

  alias TtrCore.Board.Route

  defmacro __using__ (_opts) do
    quote do
      import unquote( __MODULE__ )
      Module.register_attribute __MODULE__ , :routes, accumulate: true, persist: true
      @before_compile unquote( __MODULE__ )
    end
  end

  defmacro defroute(from, args \\ []) do
    to = args[:to]
    distance = args[:distance]
    trains = args[:trains] || [:any]

    quote do
      Enum.each(unquote(trains), fn train ->
        @routes {unquote(from), unquote(to), unquote(distance), train}
      end)
    end
  end

  defmacro __before_compile__ (_env) do
    quote do
      @spec get_routes() :: [Route.t]
      def get_routes, do: @routes

      @spec get_claimable_routes([Route.t]) :: [Route.t]
      def get_claimable_routes(claimed), do: @routes -- claimed
    end
  end
end 
```

主要的变化是围绕数据结构来构建模块属性`@routes`。我使用了列表中的嵌套映射，现在我使用了元组列表。

我尝试使用嵌套地图的原因是因为我想将每个城市映射到一个可能的目的地列表。这听起来像是一个好主意，可以找出地图上哪些城市与另一个城市相连，以计算最终得分，但结果证明这与游戏的大部分内容无关。

还有编译时自定义错误检查的特性。如果我没有遵循宏的格式，宏会抛出一个相关的错误消息，告诉我我做错了什么。但是由于数据很简单，编译器的 AST 检查足以指出我哪里出错了。

我在计算最终分数的时候只需要计算最长的路线和票点。在新旧实现中，都需要一个`map/reduce`来计算哪个玩家获得了最长的路线。我推断，数据复杂度越低，算法复杂度就越低。

所以我改了它。