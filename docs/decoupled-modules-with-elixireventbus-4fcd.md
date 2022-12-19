# 使用 Elixir EventBus 的解耦模块

> 原文：<https://dev.to/mustafaturan/decoupled-modules-with-elixireventbus-4fcd>

Elixir EventBus 是一个库，它允许不同的模块在不了解彼此的情况下相互通信。一个模块/函数可以创建一个事件结构，并传递给 EventBus，而不知道哪些模块将被使用。

[![](../Images/f0e890eda48f69d022fa689341e58818.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--v-ESyvbI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dofuyx6s747owdpaf9im.png)

模块也可以监听 EventBus 上的事件，而不知道是谁发送了事件。因此，模块可以在不依赖彼此的情况下通信。此外，替换消费者模块非常容易。只要新模块理解正在发送和接收的`Event`结构，其他模块就永远不会知道。

* * *

### 通过 4 个步骤开始解耦模块

解耦模块帮助我们编写清晰的代码，一次只关注一件事。使用 EventBus 库，您可以从真实事件总线系统中获益，并轻松编写解耦模块。

#### 注册事件主题

在您的 app init 上，或者在交付我们需要注册主题的活动之前的任何时候。例如，当我们收到一笔付款时，我们将创建`checkout_completed`事件。

```
EventBus.register_topic(:checkout_completed)
EventBus.register_topic(:checkout_failed) 
```

Enter fullscreen mode Exit fullscreen mode

#### 传递事件

模块提供了通知助手来传递事件，而不需要修改你当前的代码。

```
defmodule Order do
  ...
  use EventBus.EventSource
  ...
  def checkout(params) do
    event_params = %{topic: :checkout_completed, error_topic: :checkout_failed}
    EventSource.notify(event_params) do
      ... # process the payment as usual in here and if errors then return {:error, _} tuple
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

#### 为消费者订阅话题

假设我们有几个等待成功和失败的操作。让我们为消费者订阅事件主题。

```
EventBus.subscribe({StockUpdateService, ["^checkout_completed$", "^new_stock_arrived$", ...]})
EventBus.subscribe({CargoService, ["^checkout_completed$, "^cargo_dispatched$", "^new_stock_arrived$", ...]})
EventBus.subscribe({EmailService, ["^user_registered$", "^checkout_completed$", ...]})
EventBus.subscribe({EventBus.Logger, [".*"]}) 
```

Enter fullscreen mode Exit fullscreen mode

#### 消费事件

EventBus 消费者只是模块，因此它们可以通过任何类型的消费者策略来实现，包括 gen stage([event _ bus _ postgres](https://github.com/otobus/event_bus_postgres)作为示例)、池、动态监管器、全局消费者、spawn 等。因此，根据您的用例，使用最合适的策略来实现您的消费者。(但是任何一种阻击策略都不推荐！)

```
defmodule CargoService do
  use GenServer
  ...
  def process({topic, id}) do
    GenServer.cast(__MODULE__, event_shadow)
  end

  def handle_cast({topic, id}, state) do
    payment_data = EventBus.fetch_event_data({topic, id})
    # do sth with payment_data
    ...
    # mark event as completed for this consumer
    EventBus.mark_as_completed({CargoService, topic, id})
    {:noreply, state}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

下面是另一个需要所有事件结构并使用产卵进行消费的消费者:

```
defmodule StockUpdateService do
  ...
  def process({topic, id}) do
    spawn(fn ->     
      event = EventBus.fetch_event({topic, id})
      # do sth with event.data and/or any other event attributes
      ...
      # mark event as completed for this consumer
      EventBus.mark_as_completed({StockUpdateService, topic, id})
    end)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 关于 EventBus 库

EventBus 库是一个可跟踪、可扩展、快速、内存友好的纯 Elixir 实现，不需要任何外部依赖。

#### 可追溯

EventBus 库带有很好的可选属性来提供可追溯性。当消费者收到主题和 event_id 时，它可以用`fetch_event/1`函数获取结构。如果你使用 EventBus。EventSource helper，为您自动设置可选字段。下面是一个事件模型的结构:

```
%EventBus.Model.Event{
  id: String.t | integer(), # required
  transaction_id: String.t | integer(), # optional
  topic: atom(), # required
  data: any() # required,
  initialized_at: integer(), # optional
  occurred_at: integer(), # optional
  source: String.t(), # optional
  ttl: integer() # optional
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 可扩展

EventBus 库允许使用 regex 模式订阅事件，这允许消费者自动订阅新主题。有了这个特性，它允许与通用消费者异步扩展系统，如 [event_bus_logger](https://github.com/otobus/event_bus_logger) 、[通用 Postgresql 事件存储库](https://github.com/otobus/event_bus_postgres)、 [event_bus_metrics UI](https://github.com/otobus/event_bus/wiki/EventBus-Metrics-and-UI) 等等。

[![](../Images/87ed332304e50e542f37964f8d1fa96b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bhevEvgZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9mldxd6atsm3hj1qeocm.png)

#### 快速通过设计

EventBus 库有效地使用内置内存存储来获得并发读写的好处。它不会阻塞大多数读取操作，并允许并发读取来获取事件数据。

它应用排队理论来处理输入。并且几乎所有的实现数据访问都具有 O(1)复杂度。

#### 记忆友好

EventBus 库只向消费者提供主题和 event_id，并将原始事件数据保存在主题的事件表中。因此，只有当消费者能够处理事件时，他们才会获取事件数据。

* * *

#### 分发？

尽管创建这个库的主要目的是用于内部模块通信，但是跨节点传递事件是可能的。其中一种传递方式是像往常一样发送消息到连接的其他节点 Elixir/Erlang 方式:

```
for node <- Node.list() do
  Node.spawn(node, EventBus, :register_topic, [:checkout_completed])
  Node.spawn(node, EventBus, :notify, [%{EventBus.Model.Event{..}}])
end 
```

Enter fullscreen mode Exit fullscreen mode

* * *

注意:EventBus 库不属于 Apache Kafka、RabbitMQ 或 Redis Streams 之类的分布式解决方案。

媒体上的原创故事:[https://Medium . com/elixirlabs/decoupled-modules-with-elixir-eventbus-a709b 1479411](https://medium.com/elixirlabs/decoupled-modules-with-elixir-eventbus-a709b1479411)

EventBus 库的源代码:[https://github.com/otobus/event_bus](https://github.com/otobus/event_bus)