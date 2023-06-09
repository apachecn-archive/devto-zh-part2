# 编写服务对象的注意事项

> 原文：<https://dev.to/katafrakt/on-service-objects-1fjl>

服务对象可能是重构 Ruby 应用程序的一种最流行的技术。然而，它们有一点:它们有(太多)多种写法。因为不同的人使用服务对象的方式不同，所以他们也容易变得混乱。

自从一年半前加入 Boostcom 以来，我写了几十封求救信。这主要是因为我们并不真正使用 Rails，所以它们更加自然。我们从未在编写服务时建立严格的规则来遵循，然而，它们看起来都很相似。看起来我们编写它们的方式对我们有用，所以我想分享一些关于我认为服务对象应该是什么样子的提示。

以下是要点列表，稍后会有更详细的解释:

*   只有一个公共方法
*   显式使用`.new`
*   仅在构造函数中使用实例变量
*   不要过度使用私有方法(保持扁平)
*   不要害怕局部变量
*   在重循环中重用服务实例
*   奖励:返回值对象
*   奖励等级硬:使用单子
*   打破规则

## 只有一个公共方法

既然类通常应该遵循单一责任原则，我们也应该将它应用于服务对象。一个负责意味着只有一个公共方法——以及一种使用服务的方式。

我通常简称为`call`。这有几个好处:

*   可以使用 Ruby 神奇的快捷语法`service.(arg)`。这可能不是一个致命的特性，但有时拥有它也是件好事
*   如果方法的名字是`call`，你需要给服务一个合适的名字，这很好
*   “调用”服务似乎很自然

我知道其他人在这个问题上有完全不同的观点，他们说你应该**永远不要**调用你的方法`call`。然而，这并不重要，只要只有一种方法。

## 明确地使用`.new`

我知道很多人喜欢这样写一个快捷方式:

```
class MyService
  def self.call(*args)
    new(*args)
  end

  def call(args)
    # do something
  end
end 
```

我承认:写`MyService.call(1,2,3)`而不是更长且看似多余的`MyService.new.call(1,2,3)`有一定的吸引力。它当然更短，而且通常不会重用服务对象实例。我实际上不认为这是最好的方法。

另一边是这个反对意见。我必须说我有时使用它，但是我也认为这不是完美的。为什么？通过将数据传递给初始化器并分配给一个实例变量，您稍后会尝试用不同的方法(和相同的数据)重用该服务。这打破了以前关于只有一个公共方法的规则。比如:

```
service = MyService.new(data)
service.remove_dulicates!
filtered = service.filter_invalid
service.send_valid_to_api(filtered) 
```

我不敢说这本身是不好的*但是它允许你的服务对象不受控制地增长。如果你有自律来避免这种情况，这可能是你的方式。但我认为还有其他可能性值得探索。*

在编写服务对象的过程中，您可能会发现依赖注入。你会想要重写这样的服务:

```
# before
class MyService
  def call(data)
    filtered = FilteringService.new.call(data)
    ApiSender.new.call(filtered) # where are my Elixir pipes? :(
  end
end

# after
class MyService
  def initialize(filterer: FilteringService.new, api_sender: ApiSender.new)
    @filterer = filtering
    @api_sender = api_sender
  end

  def call(data)
    filtered = filterer.call(data)
    api_sender.call(filtered) # where are my Elixir pipes? :(
  end

  private
  attr_reader :filterer, :api_sender
end 
```

为什么？例如测试`MyService`而不需要存根它所依赖的两个服务。但那是一个完全不同的故事...无论如何，通过在开始时不使用构造函数，很容易在以后将依赖注入代码放在那里。这就是为什么我认为如果你没有任何其他强烈的意见，那么`MyService.new.call`是一条可以走的路。

## 只在构造函数中使用实例变量

如上例所示，实例变量只在构造函数中使用。在我看来，这是应该的。如果你在其他地方使用它们，用户`attr_reader`和**会不惜一切代价避免改变这些变量**。

可能会有一个问题，这些 getters 是否应该是私有的。我认为他们应该这样做，但这没什么大不了的。

还有一个提示:**使用[干式初始化器](http://dry-rb.org/gems/dry-initializer/)** ，它会替你完成这项工作。

```
require 'dry-initializer'

class MyService
  extend Dry::Initializer

  param :filterer, default: proc { FilteringService.new }
  param :api_sender, default: proc { ApiSender.new }

  def call(data)
    filtered = filterer.call(data)
    api_sender.call(filtered)
  end
end 
```

## 不要滥用私有方法

如果你的服务变得越来越复杂(有时不得不如此)，你将会添加越来越多的私有方法来保持事情的简短和 Rubocop 的快乐。随着时间的推移，代码将变得难以阅读。看这个例子:

```
class BadService
  def call(data)
    send_filtered_data_to_api(data)
    notify_subscribers(data)
  end

  private

  def notify_subscribers(data)
    SubscribersNotifier.new.call(data)
  end

  def send_filtered_data_to_api(data)
    api_sender.call(filtered(data))
  end

  def filtered(data)
    filterer.call(data)
  end

  def api_sender
    @api_sender ||= ApiSender.new
  end

  def filterer
    @filtering_service ||= FilteringService.new
  end 
```

你要跳多少次才能读懂这段代码？答案是:很多`call`->`send_filtered_data_to_api`->`api_sender`->`send_filtered_data_to_api`->-`filtered`->`filterer`->`filtered`->`send_filtered_data_to_api`->`call`->`notify_subscribers`->`call`。或者使用不同的符号:

```
call
  send_filtered_data_to_api
    api_sender
    filtered
      filterer
  notify_subscribers 
```

如您所见，我们这里有四个级别的呼叫。我想应该只有他们两个。这就是为什么我有时也称这个规则为“**保持平坦**”。

在我看来，一个完美的办法是**只使用`call`** 中的私有方法。这两个意思都有:除了`call`中的私有方法之外没有别的，只有`call`可以使用私有方法。通过这种方式，您可以将入口方法作为服务功能的一种高级描述。当然，私有方法的定义顺序应该和它们被调用的顺序一样，所以你在读取它们的时候不需要参考 call 是做什么的。

这样，即使是做很多事情的服务也可以保持简单易读:

```
class RequestHandler
  def call(params)
    valid_params = filter_valid_params(params)
    save_to_database(valid_params)
    notify_subscribers(valid_params)
    log_params(params)
    send_measures(params)
  end

  private

  # [...]
end 
```

旁注:如果你的私有方法开始做太多太长的事情，你可能需要引入另一个服务。

## 不要害怕局部变量

今年早些时候，我读过一个叫做 *[的东西，局部变量厌恶反模式](http://www.soulcutter.com/articles/local-variable-aversion-antipattern.html)* ，我开始在 Ruby 代码中注意到它。

基本上，**你不需要用一个单独的方法**来初始化一切。此外，没有必要记住所有的东西——通常你只使用一次。所以没有比这更好的了:

```
def send_to_api(data)
  api_sender.call(data)
end

def api_sender
  @api_sender ||= ApiSender.new
end 
```

比这个:

```
def send_to_api(data)
  sender = ApiSender.new
  sender.call(data)
end 
```

后者实际上更容易理解，并且不违反只在初始化器中设置实例变量的规则。

## 复用重循环中的服务实例

这可能是显而易见的，但当涉及到高级 Ruby 代码时，我们很少谈论性能，所以我敢大声说出来。

```
 # DON'T

large_array.each do |item|
  ItemProcessor.new.call(item)
end

# DO

processor = ItemProcessor.new
large_array.each do |item|
  processor.call(item)
end 
```

Ruby 对象相对便宜，但不是免费的。如果你能避免创建不必要的对象，在某些时候你会从中受益。

## 奖金:返回值对象

当你做一些比简化数据更复杂的事情时，返回服务对象是一个好习惯。在某个时候，你会想知道更多关于高级服务对象中发生的事情，你会开始创建这样的小怪物:

```
class MyHighLevelService
  def call(data)
    pg_result = save_to_postgresql(data)
    kafka_result = send_to_kafka(data)
    worker_ids = schedule_workers(data)
    [pg_result, kafka_result, worker_ids]
  end
end

# ...

pg_result, kafka_result, worker_ids = MyHighLevelService.new.call(data) 
```

值对象更适合它。

## 加值等级硬:使用单子

我不会说太多，因为这是我的清单上更多的一点开始做。但是有时候，干燥单子提供的东西很容易适合你的数据流。但是不要因为“单子很酷”就想把他们到处挤。正如彼得·索尔尼卡曾经说过的:

> 还有一件关于单子的事情——我完全推荐在有用的地方使用干式单子，比如用好的结果处理来组合操作等等。“太多”的事情是在任何地方、任何事情上一致地使用单子。
> 
> — Piotr Solnica (@\_solnic\_) [June 26, 2018](https://twitter.com/_solnic_/status/1011539445806456832?ref_src=twsrc%5Etfw)

## 最后但同样重要的是:如果有必要，打破规则

这只是一组建议。我觉得他们很好，很有凝聚力，但无论如何都是建议。甚至我也不总是跟着他们。有时有很好的理由不这样做。例如，当所有其他东西(例如来自您无法控制的外部 gems)都在使用`Blah.call(1)`时，也许您的服务也应该使用这个符号？否则你会发现自己每次都不知道该写些什么。

不要因为网上有人说他不这么做，就开始改变你代码中的一切。做有意识的决定，试着坚持，但允许自己有时偏离。毕竟，语境就是一切，而你的语境可能与我的截然不同。

* * *

This has been also posted [on my blog](http://katafrakt.me/2018/07/04/writing-service-objects/).

* * *