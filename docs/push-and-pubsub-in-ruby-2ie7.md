# Ruby 中的推送和发布/订阅

> 原文：<https://dev.to/appsignal/push-and-pubsub-in-ruby-2ie7>

使用像 [Action Cable](https://guides.rubyonrails.org/action_cable_overview.html) 这样的库，在 Rails 中构建实时特性变得容易多了。在这一集 [AppSignal Academy](https://blog.appsignal.com/category/academy.html) 中，我们将深入实时更新，并尝试构建一个最小的 WebSocket 服务器，看看它是如何工作的。

我们将构建一个应用程序，**推送**数据，并通过 **WebSocket** 使用 **Pub/Sub** 。在我们开始编写代码之前，让我们先花点时间了解一下这三个概念的含义:

*   *推送是指将数据推送给接收方，而不是让接收方轮询该数据。必须有实时更新，如股票价格，聊天应用程序，或操作控制台。*

**   ***Pub/Sub*** 或 publish and subscribe 是上世纪 90 年代 TIBCO 在华尔街流行的一种推送数据的交互模型。接收者订阅一个主题，并等待发布者将数据推送到该主题。通常包括通配符模式匹配来将发布的消息匹配到侦听器，尽管一些更简单的实现只在主题中使用命名通道而不是通配符。我在 TIBCO 起步较早，所以我喜欢通配符模式匹配的灵活性。

    *   ***WebSocket*** 是一种交换数据的协议——通常是在 web 浏览器和应用程序之间。HTTP 连接被升级为 WebSocket 连接，然后数据可以在两个端点之间双向发送。WebSockets 可以将数据从应用程序推送到浏览器。它还提供了除 POST 或 PUT 之外的另一种机制，用于将浏览器中 JavaScript 代码的数据发送回应用程序。很不错，是吧？* 

 *## 引擎盖下

让我们看一个 WebSocket 服务器如何工作的例子。在浏览器中，客户端尝试使用 JavaScript 代码与服务器建立 WebSocket 连接。

```
var sock = new WebSocket("ws://" + document.URL.split('/')[2] + '/upgrade'); 
```

Enter fullscreen mode Exit fullscreen mode

服务器接收到一个 HTTP 请求，该请求带有一个指示已请求升级的指示符。通常，服务器让应用程序决定是否升级。它如何做到这一点取决于提供给应用程序的 API。一个支持 Rack 的服务器提供了一个劫持套接字的选项，让开发者处理所有的协议细节，或者根据一个[提议的 PR](https://github.com/rack/rack/pull/1272) ，一个升级响应就足够了。

升级是服务器和客户端之间的一系列交换。所有的浏览器和一些服务器 gem 都隐藏了这些细节。一旦建立了连接，就可以按照 WebSocket 协议交换消息。

引擎盖下的魔法处理编码、解码和消息交换协议。消息是二进制、固定宽度的结构，带有尾部有效负载，使用 SHA1 加密。WebSocket 协议包括几种消息类型和交换，比如 ping/pong 心跳以及打开和关闭消息交换。这就是服务器不使用连接劫持方法的神奇之处。

## 潜入

我们将使用一个时钟线程的例子，该线程开始向所有监听客户端发布当前时间。我们将使用 [Agoo](https://github.com/ohler55/agoo) 来构建我们的服务器，因为它速度快，并将复杂性保持在最低水平。

我们将使用一些 JavaScript 作为客户端，在 HTML 页面上显示当前时间。在创建一个新的`WebSocket`之后，设置一个`onopen`回调来改变 HTML 元素的状态。`onmessage`回调更新了`message` HTML 元素。在处理异步调用(如发布和订阅交换)时，回调是一种常见的设计模式。

```
<!-- websocket.html -->
<html>
  <body>
    <p id="status"> ... </p>
    <p id="message"> ... waiting ... </p>

    <script type="text/javascript">
      var sock = new WebSocket("ws://" + document.URL.split('/')[2] + '/upgrade');
      sock.onopen = function() {
          document.getElementById("status").textContent = "connected";
      }
      sock.onmessage = function(msg) {
          document.getElementById("message").textContent = msg.data;
      }
    </script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

客户端完成后，让我们实现服务器，这是一个使用 Rack API 的 Ruby 应用程序。`Clock`类本身将是`/upgrade`路径上所有 HTTP 请求的处理程序。如果请求是升级，我们简单地返回成功，HTTP 状态代码为 200，否则，我们返回 404，表示没有找到页面。`#call`方法中唯一的另一步是分配 WebSocket 处理程序。

```
class Clock
  def self.call(env)
    unless env['rack.upgrade?'].nil?
      env['rack.upgrade'] = Clock
      [ 200, { }, [ ] ]
    else
      [ 404, { }, [ ] ]
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

API 是基于回调的。我们对服务器唯一关心的回调是`#on_open`回调，它使我们能够创建对“time”主题的订阅。消息通过由主题或话题标识的通道进行交换。建立 web 套接字连接时调用`#on_open`。

```
class Clock
  # ...

  def self.on_open(client)
    client.subscribe('time')
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们用一个每秒后发布时间的线程开始发布。对`Agoo.publish`的调用发送一条关于“时间”主题的消息，然后所有订阅者都收到这条消息。服务器跟踪订阅和连接，并将消息传递给更新 HTML 元素的 JavaScript 客户机。

```
require 'agoo'

Thread.new {
  loop do
    now = Time.now
    Agoo.publish('time', "%02d:%02d:%02d" % [now.hour, now.min, now.sec])
    sleep(1)
  end
} 
```

Enter fullscreen mode Exit fullscreen mode

唯一需要的其他代码是初始化和启动服务器的代码。对`Agoo::Server.handle(:GET, '/upgrade', Clock)`的调用告诉服务器监听`/upgrade` URL 路径上的 HTTP GET 请求，并将这些请求传递给`Clock`类。这允许路由发生在 Ruby 之外，以提高性能和灵活性。

```
Agoo::Server.init(6464, '.', thread_count: 0)
Agoo::Server.handle(:GET, '/upgrade', Clock)
Agoo::Server.start 
```

Enter fullscreen mode Exit fullscreen mode

我们快到了。使用以下命令运行服务器。

```
$ ruby pubsub.rb 
```

Enter fullscreen mode Exit fullscreen mode

应该会出现如下所示的日志条目，表明服务器正在运行并侦听端口 6464。

```
I 2018/08/14 19:49:45.170618000 INFO: Agoo 2.5.0 with pid 40366 is listening on http://:6464. 
```

Enter fullscreen mode Exit fullscreen mode

## 看它是否工作的时间

我们打开[http://localhost:6464/web socket . html](http://localhost:6464/websocket.html)。当连接建立时，在初始闪烁之后，应该显示连接状态和时间。随着时钟的滴答声，时间每秒钟都会增加。

```
connected

19:50:12 
```

Enter fullscreen mode Exit fullscreen mode

祝贺您创建了发布和订阅 web 应用程序；-)

在今天的节目中，我们学习了如何使用 WebSocket。服务器端事件(SSE)提供了另一种选择，我们已经在[完整源代码示例](https://github.com/ohler55/agoo/blob/master/example/push/pubsub.rb)中包含了 SSE。如果你想了解更多，看看我们使用的 [Agoo](https://github.com/ohler55/agoo) 服务器或者[碘](https://github.com/boazsegev/iodine) WebSocket 服务器。

我们很想知道您对这篇文章的看法，或者您是否有任何问题。我们总是在寻找可以研究和解释的话题，所以如果你想了解 Ruby 的神奇之处，请不要犹豫，留下你的评论。

*本文由客座作者[彼得·奥勒](http://www.ohler.com)撰写。Peter 创建了相当多的高性能代码，并不时地撰写相关文章。他制作了 Agoo gem，这是一个非常酷的高性能 HTTP 服务器。**