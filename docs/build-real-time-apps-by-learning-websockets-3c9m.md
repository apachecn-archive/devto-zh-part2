# 通过学习 WebSockets 构建实时应用

> 原文：<https://dev.to/iwilsonq/build-real-time-apps-by-learning-websockets-3c9m>

作为一名正在学习的 web 开发人员，一旦您学会了查询外部 API 以在您的 UI 中显示数据，一个全新的世界就展现在您面前。

在面试各个公司的 UI 开发人员职位时，我会说这样的话，“只要给我端点，我会处理剩下的；)".

他们所有的其他问题，比如“你如何处理逆境？”或者“你如何处理与利益相关者的纠纷？”变得没有意义。

通过 HTTP 请求，我可以获取数据、发布数据和删除数据。我什么都做——不管你知不知道，我绝对适合你的团队。

这在我脑海的回音室里听起来很棒。我清楚地记得感觉自己像一个冠军开发者，因为我知道如何向 REST APIs 发送请求。

然后，他们让我做了一个聊天应用程序。

# 问题与投票

如果你像我一样，当你成为一名网络开发人员时，你不明白聊天应用程序是如何工作的。您可能会想，嘿，我会每半秒左右发送一个 GET 请求来检索最近的聊天消息。

在 JavaScript 中，这可能看起来有点像这样:

```
let clearIntervalId
let messages = []

function pollChatMessages() {
  const clearIntervalId = setInterval(() => {
    messages = await axios.get('https://api.example.com/threads/1/messages') // totally fake url
  }, 500)
} 
```

Enter fullscreen mode Exit fullscreen mode

这叫做轮询，它会工作一会儿。我的一个客户看不出区别，只要只有几个用户。

这个函数将在我们的 web 应用程序范围内的单例中的某个时间间隔执行。如果我们想终止投票，我们可以调用`clearInterval(clearIntervalId)`。

如果我们和 10 个人聊天，我们将在浏览器上进行 10 次投票。同样，这 10 个人也会对他们聊天的每个人进行民意调查。

如果一些线程有数百条消息呢？对于一个简单的聊天应用程序来说，这是一大堆不必要的大请求。

这里的问题是，轮询的使用假设像我们的浏览器这样的客户端无法订阅服务器。我们可以通过一点网络做得更好。

# 网络基础知识

让我们从一些网络基础知识开始，什么是**插座**？

一个 **TCP 套接字**是一个*端点实例*，它由一个 IP 地址和一个端口的组合定义，在监听状态(一个服务器)或特定 TCP 连接(一个客户端，就像你的浏览器)的上下文中。

一个 **TCP 连接**由两个套接字的配对定义。

我们通常在浏览器 web 应用程序中使用三种主要的传输方式:

*   **XMLHTTPRequests** ，或者简称 HTTP。发送一个请求，得到一个响应。这些很常见。
*   **服务器发送的事件**，或 SSE。发送一个长期请求，并能够从服务器传输数据。非常适合实时数据流，尤其是当客户端不需要将消息发送回服务器时。
*   WebSockets ，唯一允许文本和二进制数据双向传输的传输方式。我们将深入探讨一下。

这是我从[高性能浏览器网络](https://hpbn.co)偷来的图表，它说明了这些传输之间的通信流。如果你真的想提高你的网络应用程序的性能，这是一本很好的书。

[![ws](img/8401ae522627b792eed3f35d36aee7cb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ApzTu2NN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/47j90glrwxk8dv2perdp.png)

在大多数处理外部数据的教程中，您将处理最左边范例的 HTTP 请求。实际上，所有这些流都是由 HTTP 请求发起的，如蓝色箭头所示。

我不常在 SSE 上看到文章或教程，但是在单向数据流听起来很有趣的情况下， [MDN 有一个很好的参考资料](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events)。

第三个流是我们最感兴趣的——它使我们能够通过一个单一的长期连接与服务器通信。

# 进入 WebSockets

正如 Mozilla 开发者文档所描述的，

> WebSockets 是一种先进的技术，它使得在用户的浏览器和服务器之间打开交互式通信会话成为可能。有了这个 API，您可以向服务器发送消息并接收事件驱动的响应，而不必轮询服务器的回复。

太棒了，我们不需要做任何投票！但是它是如何工作的呢？

客户端和服务器之间的 WebSocket 连接的生命周期是这样的。假设我们的服务器托管在端口 8080 上的`https://api.example.com`,我们的客户端是某个人的浏览器。

1.  客户机向`api.example.com:8080`发送一个 GET 请求，带有几个头，表示它想与服务器建立一个 WebSocket 连接。其中一个称为`Sec-WebSocket-Key`，用于保护客户端和服务器之间的连接。服务器从这个名为`Sec-WebSocket-Accept`的键中得到一个响应头，这表明服务器确实支持 WebSockets，并且它不会试图将请求作为普通的 HTTP 请求来处理。
2.  服务器响应代码 101 -交换协议，表明握手完成，客户端/服务器可以开始交换通过 XOR 加密的消息[。抛开数据屏蔽的细节，我们现在可以通过这个连接发布和订阅文本或二进制消息。](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_servers#Exchanging_Data_Frames)
3.  在成功握手后的任何时候，客户端或服务器都可以发送 ping 命令，以检查另一方是否仍处于连接状态。如果 ping 的接收方没有发回一个 pong，则可能已经断开连接。

在 JavaScript 中，我们可以像这样连接到 WebSocket 服务器:

```
const thread = document.getElementById('chat-thread-1')

const conn = new WebSocket('ws://api.example.com/threads/1')

conn.onclose = function(event) {
  console.log('Connection closed')
}

conn.onmessage = function(event) {
  console.log('Message received.')
  const message = document.createElement('p')
  message.textContent = event.data
  thread.append(message)
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以调用内置的 WebSocket 构造函数来创建一个连接，此时我们可以设置事件处理程序来决定收到消息时会发生什么。

我们还可以发送消息，如果我们有一个客户端可以输入文本与其他用户聊天的`input`元素会怎么样？那肯定会很有用。

```
function sendMessage() {
  const input = document.getElementById('chat-thread-1-input')
  conn.send(input.value)
  input.value = ''
} 
```

Enter fullscreen mode Exit fullscreen mode

更好的是，如果我们想用消息类型和时间戳(可能是 JSON 的形式)来交流更复杂的数据，会怎么样呢？

```
function sendMessage() {
  const input = document.getElementById('chat-thread-1-input')

  const message = {
    type: 'message',
    text: input.value,
    date: Date.now(),
  }

  conn.send(JSON.stringify(message))
  input.value = ''
} 
```

Enter fullscreen mode Exit fullscreen mode

通过一点 DOM 操作，我们离真正的聊天应用程序不远了。聊天很简洁，但是我们还能用这项技术做什么呢？

# web sockets 的其他应用

对于这个聊天示例，WebSockets 的应用是显而易见的:发送和接收文本消息。

如果你经常去 [dev.to](https://dev.to) ，那么你可能已经注意到他们正在进行一个竞赛，叫做[用 Pusher](https://dev.to/devteam/first-ever-dev-contest-build-a-realtime-app-with-pusher-4nhp) 构建一个实时应用。

Pusher API 建立在 WebSockets 的基础上。它的一些使用案例包括:

*   实时更新位置，比如旅行和送货
*   实时图表
*   通过浏览器、手机或物联网进行协作
*   网络游戏

就我个人而言，我正在考虑建立一个云 markdown 编辑器，它将帮助我在一个地方编辑和分享帖子。我会把我的减价内容存储在服务器上，最好能在我的 Mac 或手机上查看/编辑。

如果我想与某人分享一篇文章进行编辑，我会给他们一个链接，我就能实时看到他们的光标和注释，类似于谷歌文档的工作方式。

当然，对我来说，部分乐趣在于自己实现服务器。我是否会使用 Pusher 这样的服务将取决于我的生产力/实现的痛苦。

如果这也引起了你的兴趣，你可能会发现这些链接很有用:

*   [在 MDN 上编写 WebSocket 服务器](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_servers)
*   [如何使用 React、Redux、Redux-Saga 和 Web Sockets 构建聊天应用](https://medium.freecodecamp.org/how-to-build-a-chat-application-using-react-redux-redux-saga-and-web-sockets-47423e4bc21a)
*   [用 Go 和 WebSockets 构建一个实时聊天服务器](https://scotch.io/bar-talk/build-a-realtime-chat-server-with-go-and-websockets)

好奇更多帖子或者妙语连珠？在[媒体](https://medium.com/@iwilsonq)、 [Github](https://github.com/iwilsonq) 和 [Twitter](https://twitter.com/iwilsonq) 上关注我吧！