# 叫做 EventEmitter 的美丽东西

> 原文：<https://dev.to/tunaxor/the-beautiful-thing-called-eventemitter-23ei>

事件发射器是在代码中移动部分之间进行异步通信的好方法。
事件发射器在一个*稀释的*简化的*功能字典中，有一些助手(通常:开，关，发射)*

所以一个非常简单和天真的实现可能是这样的

```
// we'll omit error handling and complex stuff for simplicity
const EventEmitter = {
  events: {}, // dictionary with our events
  on(event, listener) { // add event listeners
    if (!this.events[event]) { this.events[event] = { listeners: [] } }
    this.events[event].listeners.push(listener);
  },
  off(event) { // remove listeners
    delete this.events[event]
  },
  emit(name, ...payload) { // trigger events
    for (const listener of this.events[name].listeners) {
      listener.apply(this, payload)
    }
  }
};

EventEmitter.on('dog', () => console.log('dog'));
EventEmitter.on('dog', (name, color, race) => console.log('dog', name, color, race));

EventEmitter.emit('dog');
// dog
// dog undefined undefined undefined

EventEmitter.emit('dog', 'Fig', 'brown', 'chihuahua');
// dog
// dog Fig brown chihuahua

EventEmitter.off('dog')

// EventEmitter.emit('dog');
// TypeError: Cannot read property 'listeners' of undefined 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果你以前用过这个发射器，也许你会想*真的吗？就这么简单？*一般来说，是的，但也许你想调整性能和伸缩性、错误管理等。

然而，如果你不想重新发明轮子，你可以只使用节点的事件发射器的实现，我很肯定它已经很棒了，因为节点的流实现了那个接口。

实现时代码非常相似:

```
const EventEmitter = require('events');

const ee = new EventEmitter();

ee.on('dog', () => console.log('dog'));
ee.on('dog', (name, color, race) => console.log('dog', name, color, race));

ee.emit('dog');
// dog
// dog undefined undefined undefined

ee.emit('dog', 'Fig', 'brown', 'chihuahua');
// dog
// dog Fig brown chihuahua 
```

Enter fullscreen mode Exit fullscreen mode

因此，在这一点上，你可能想知道为什么你应该使用它？毕竟，我们有异步代码的工具，比如承诺或回调，你会认为这是一个公平的论点。

在我看来，常见的情况是，当你需要对环境中发生的某些事件做出反应时，例如，浏览器的点击，你对一个你永远不知道它何时会发生的点击做出反应，而承诺或回调更可能以一种更程序化的方式调用，例如，在你做了一些事情之后，继续做这个异步任务，并在完成后调用我来继续做我要做的事情。

换句话说，举另一个类似承诺的例子

> 嘿，马克，朱迪思打电话来，她想稍后在自助餐厅见你，我告诉她你会打电话来确认(设定承诺)
> //花几分钟做其他事情
> 嘿，朱迪思，马克，我在路上了(履行承诺)

现在让我们试着做一个发射器的例子

> 嘿，史蒂夫，你需要守着这个入口，每次有人经过，按下这个按钮来增加计数器，好吗？(注册事件[上])
> 因为我在别的地方有理由和事情要做。
> 确定标记！
> 【发射】人 3 分钟后来
> **点击**
> 【发射】人 1 小时后来
> **点击**

我希望说清楚一点
(emit)人来了之后
**点击**

是的，那也可能发生:P

# 扩展一个事件发射器

事件发射器易于在节点
中扩展

```
class MyEmitter extends EventEmitter {

} 
```

Enter fullscreen mode Exit fullscreen mode

嘣，你已经可以在上用*使用 MyEmitter，*发射*，以及你可以在[节点文档](https://nodejs.org/docs/latest-v8.x/api/events.html)中找到的其他酷功能*

再来做一个例子

```
 class MyEmitter extends EventEmitter {

  constructor(avilableTickets = 31) {
    super()
    this.ticketCount = avilableTickets
  }

  *dispenseTicket() {
    while (this.ticketCount > 0) {
      // check each 10 tickets
      if (this.ticketCount % 10 === 0) {
        // call something somewhere to act at the count number
        this.emit('spent-10-tickets', this.ticketCount)
      } else if (this.ticketCount < 10) {
        this.emit('warn:low-tickets', this.ticketCount)
      }
      yield --this.ticketCount;
    }
    this.emit('spent-all-tickets')
  }
}

const myee = new MyEmitter();

myee
  .on('spent-10-tickets', count => console.log(count))
  .on('warn:low-tickets', count => console.warn(`Warning! ticket count is low:${count}`));

const ticketDispenser = myee.dispenseTicket();
const interval = setInterval(() => ticketDispenser.next(), 500);

myee
  .on('spent-all-tickets', () => {
    console.log('no more tickets')
    clearInterval(interval)
  }); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以在代码中其他不直接调用发射器的地方使用这个自动售票机(在本例中由 set interval 模拟)

我们更感兴趣的是知道我们的票数是多少，并据此做出反应。

在 node 的例子中，你可以在流对象中找到发射器，所以如果你创建一个写/读流，你经常在读/写事件和结束时使用监听器。

现在，我更多地使用发射器来注册系统事件，如未处理的承诺错误，或者跟踪流的进程并将其打印到控制台。对于自定义 CLI 工具，您的用法可能会有所不同，WebSocket 通信可能是一个很好的用例，因为 WebSockets 旨在成为一个实时通信解决方案，这些交互很可能会随机发生。

一个复杂的用例曾经迫使我混合生成器和节点流。
基本上，我需要转换来自 mongo 集合的数据，将超过数十万条记录插入到一个新的集合中，这必须每周运行一次，而且必须对集合中的每条记录都这样做。

解决方案必须在 node 中，我的尝试是使用一个生成器来拉取 n 数量的记录(这取决于服务器的处理能力，100、600 个，随便你怎么说)记录(就像自动售票机)，然后这些记录进入一个转换节点流(在对象模式下),完成所有的转换工作， 一旦完成了那批记录，它就只使用 上的 ***和正确位置上的 ***发射*** 来提取另一批记录，以此类推，因此处理总是在受控的批次中进行，并且数据流从不阻塞。***

我意识到它有一个混合的解决方案，但是如果没有事件发射器，我(以我的经验)永远也做不到。

但是请注意，使用太多的侦听器会导致性能问题，发射器确实很强大，但是如果您使用太多，那么您将会遇到很多性能问题。

还要记住，任何地方的代码都可以调用这些发射器，所以要小心不要运行意大利面条式的代码以及隐藏在代码中的监听器，尽量做到简洁，并很好地本地化所有的调用

那么你的 EventEmitter 用例是什么？你不喜欢他们吗？
请分享你的想法！

你可以在这里找到这段代码的副本
[https://repl.it/@AngelMunoz/Naive-Event-Emmiter](https://repl.it/@AngelMunoz/Naive-Event-Emmiter)

如果您对事件发射器的轻量级实现感兴趣，请看一下这个要点！
[https://gist.github.com/mudge/5830382](https://gist.github.com/mudge/5830382)

(发出)人来了之后
**点击**