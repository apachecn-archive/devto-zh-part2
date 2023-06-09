# Ruby 中的过程与调用

> 原文：<https://dev.to/jgaskins/procs-vs-callables-in-ruby-1ff1>

这篇文章最初发表在[jgaskins.org](http://jgaskins.org/blog/2018/09/04/procs-vs-callables-in-ruby)上。

我最近一直在玩 RabbitMQ，将它与我们目前在工作中使用的 SNS+SQS 作为我们的消息总线进行比较。它的一个优点是，使用`bunny` gem，您可以通过传递一个告诉如何处理消息的块来订阅队列中的消息:

```
queue.bind(exchange).subscribe do |delivery, metadata, message|
  do_things_with(message, metadata)
end 
```

Enter fullscreen mode Exit fullscreen mode

这让我陷入了“这需要多高的性能？”的困惑这样我就可以知道它是否应该在自己的进程中运行。从那时起，我开始仔细观察，并检查我们如何才能最大限度地提高性能。

我想了解 gem 的性能，特别是因为消息队列的消费者应该很快并且开销最小，所以我打开代码，发现当调用那个块时，它用 splat args 调用[，然后通过 splat 相同的 args 调用那个块。](https://github.com/ruby-amqp/bunny/blob/2f5b35530c6415b9f0e061586b09755ba0ae1e74/lib/bunny/consumer.rb#L55-L57)

*注意*:这不是对`bunny`宝石、splat args 或任何东西的批评。这只是对获取一个块并在以后调用该块的模式的性能特征的探索，以及该模式的一些变体。这些都是 Ruby 中常见的约定，我认为理解它们的性能是很有用的。

我想知道的第一件事是，调用 procs 与调用 [PORO](http://blog.jayfields.com/2007/10/ruby-poro.html) 的`call`方法——即`call`能力对象——的性能成本是多少。

## 假设和假说

我有一种感觉，procs 会更慢。我没有任何东西可以作为这个假设的基础，但是 Ruby 实现围绕着向对象发送消息的思想进行了很多优化，procs 不是一般的对象——它们基本上是绑定到一些字节码的 Ruby。我不知道这些绑定有多重，但是考虑到你可以从它们那里得到各种各样的内省(包括局部变量)，我假设它们会相当重。所以我假设了很多。

不太像是假设，更像是假设的是 splat-args 会比显式参数慢。Splat 参数必须分配和填充一个数组，所以它们有性能成本。尽管如此，我并不完全确定，所以这充其量只是一个假设。

在没有基准的情况下猜测性能是浪费时间，所以我[写了一些](https://gist.github.com/jgaskins/06d50b7723090c60e64b5af436fb6436)，包括用 splat args 调用两者。结果我的猜测非常接近(点击链接查看基准代码):

```
Comparison:
     callable no arg: 10095848.2 i/s
   callable with arg:  9777103.9 i/s - same-ish: difference falls within error
     callable 3 args:  9460308.0 i/s - same-ish: difference falls within error
callable splat args (0):  6773190.5 i/s - 1.49x  slower
         proc no arg:  6747397.4 i/s - 1.50x  slower
       proc with arg:  6663572.5 i/s - 1.52x  slower
         proc 3 args:  6454715.5 i/s - 1.56x  slower
callable splat args (1):  5099903.4 i/s - 1.98x  slower
 proc splat args (0):  5028088.6 i/s - 2.01x  slower
callable splat args (3):  4880320.0 i/s - 2.07x  slower
 proc splat args (1):  4091623.1 i/s - 2.47x  slower
 proc splat args (3):  4005997.8 i/s - 2.52x  slower 
```

Enter fullscreen mode Exit fullscreen mode

这令人失望，原因有二:

1.  证明自己是正确的对你没什么帮助；证明自己是错的会教会你很多。充其量，我证明了一堆受过适度教育的假设是正确的。
2.  在 Ruby 中，捕获并随后调用块是如此常见的一种做法，以至于我想知道我们因此损失了多少性能

好的一面是，为了找到这个答案，我已经钻了足够多的兔子洞。如果我错了，我会更深入了解原因。

## 做什么？

如果说“永远不要因为性能而捕获块”，那就太傻了。在 Ruby 中捕获块可能会慢一点，但这是一个很有表现力的概念，性能上的差异不太可能对你的应用程序产生那么大的影响——我仍然每秒钟得到 670 万次调用。如果您需要以每秒几百万次的速度调用一个捕获的块，那么这篇文章可能会对您有所帮助。否则，这很大程度上是一个学术练习，也没关系。

如果你想在允许块捕获的同时优化性能，你可以通过调用一个可调用的*或*块:
来实现这两个目的

```
class ThingThatHasEvents
  def on event_name, handler=nil, &block
    @events[event_name] << (handler || block)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

您将希望在那里进行检查，以确保您接收到一个或另一个，但是为传递任何一个提供启示将为您提供在接收块的同时仍然接受可调用对象的更快路径的表达性 API。在典型的“事件处理程序”风格中，事件是通过对每个处理程序的调用发出的，我们可以看到这比快了[45%。](https://gist.github.com/jgaskins/e493f7f87f05e9c96cfcb289d0c0fa12)

不幸的是，基准测试表明，一组异构的事件处理程序(一些作为块传递，一些作为可调用的 POROs 传递)实际上比 procs-only 要慢，但只慢了 10%左右——比 procs 和 callables 分别慢得多。

## 总是基准

在这一点上，我可能是对的，但是没有基准的性能声明总是扯淡。始终进行基准测试。

即使你以前做过类似的事情。即使你以前在不同的应用程序中做过完全相同的事情。
即使你已经在不同的 Ruby 虚拟机上的相同应用程序中做了完全相同的事情。

我可能会在`bunny` gem 中放一个 PR，看看我们是否可以移除 splat-args，并允许使用非`Proc` PORO 进行订阅。同时，当前的实现为我们的需求提供了足够的性能。