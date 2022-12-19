# Go:使用通道和 WebSockets 的异步实时广播

> 原文：<https://dev.to/danielkun/go-asynchronous-and-safe-real-time-broadcasting-using-channels-and-websockets-4g5d>

最近，我需要在服务器上执行一个长时间运行的命令，并将结果实时发送给多个客户端。我很自然地选择使用 WebSockets 作为传输层。但是当在 Go 中实现后端时，我在寻找一种解决方案，以线程安全的方式将`exec.Command`发出的结果分发给多个客户端。在 Go 中，[通道](https://gobyexample.com/channels)是异步和线程安全发送数据时的首选工具，但是通道只在两个端点之间通信。没有办法从多个客户端“挂钩”到一个频道。所以我需要一个不同的解决方案。在我的搜索中，我了解到你可以[通过](https://www.goin5minutes.com/blog/channel_over_channel/)频道发送频道，这使得频道成为一个比我已经拥有的更加多功能的工具。使用这个，我认为渠道的许多限制已经被解除。

有了这些知识，你就可以写这样的东西，它基本上是一个客户机/服务器基础设施:

```
serverChan := make(chan chan string, 1)
clientChan := make(chan string, 1)
serverChan <- clientChan 
```

Enter fullscreen mode Exit fullscreen mode

没错，我们把一个频道送进了一个频道！事实上，这并没有多大作用。我们需要接收`clientChan`并发回一些东西，以获得真正的客户机/服务器感觉:

```
go func(serverChan chan chan string) {
    client := <-serverChan
    client <- "Hello, World!"
}(serverChan)
fmt.Println(<-clientChan)
close(serverChan)
close(clientChan) 
```

Enter fullscreen mode Exit fullscreen mode

耶，我们成功了！我们通过一个通道发送一个通道，就是为了发回一个字符串。这本身可能已经是惊人的了(对吗？)，但它变得更好:我们可以使用它作为一个构建块来发送多个客户端到服务器，让服务器实时地向多个客户端“广播”内容。为此我们使用了`select`,所以它对 CPU 的使用非常少。

我们来看看服务器代码:

```
func server(serverChan chan chan string) {
    var clients []chan string
    for {
        select {
        case client, _ := <-serverChan:
            clients = append(clients, client)
            // Broadcast the number of clients to all clients:
            for _, c := range clients {
                c <- fmt.Sprintf("%d client(s) connected.", len(clients))
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

每当有新客户端连接时，服务器就不停地监听新客户端，将它们全部添加到一个列表中，并向所有客户端发送一个文本，说明客户端总数。

客户端代码可能是这样的:

```
func client(clientName string, clientChan chan string) {
    for {
        text, _ := <-clientChan
        fmt.Printf("%s: %s\n", clientName, text)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码将所有这些粘合在一起:

```
// Start the server:
serverChan:= make(chan chan string, 4)
go server(serverChan)

// Connect the clients:
client1Chan := make(chan string, 4)
client2Chan := make(chan string, 4)
serverChan <- client1Chan
serverChan <- client2Chan

// Notice that we have to start the clients in their own goroutine,// because we would have a deadlock otherwise:
go client("Client 1", client1Chan)
go client("Client 2", client2Chan)

// Just a dirty hack to wait for everything to finish up.
// A clean and safe approach would have been too much boilerplate code
// for this blog-post
time.Sleep(time.Second) 
```

Enter fullscreen mode Exit fullscreen mode

这将按照某种半随机的顺序输出:

```
Client 1: 1 client(s) connected.
Client 1: 2 client(s) connected.
Client 2: 2 client(s) connected. 
```

Enter fullscreen mode Exit fullscreen mode

瞧啊。理论上，这应该“无限地”扩展，就像计算机科学中的一切一样。:-)

现在我们可以把它连接到我们的 WebSocket 服务器上。我选择了`github.com/gorilla/websocket`作为我的 WebSocket 库。在这个例子中，服务器会以秒为单位计算它的正常运行时间，并将这个信息推送给所有客户端。我们可以选择新的客户机是获得自服务器启动以来发生的所有输出，还是只获得自客户机连接以来的新内容。在我的实现中，我选择现在只发送新的内容。

首先，让我们编写一个稍微修改的服务器来计算它的正常运行时间:

```
func uptimeServer(serverChan chan chan string) {
    var clients []chan string
    uptimeChan := make(chan int, 1)
    // This goroutine will count our uptime in the background, and write
    // updates to uptimeChan:
    go func (target chan int) {
        i := 0
        for {
            time.Sleep(time.Second)
            i++
            target <- i
        }
    }(uptimeChan)
    // And now we listen to new clients and new uptime messages:
    for {
        select {
        case client, _ := <-serverChan:
            clients = append(clients, client)
        case uptime, _ := <-uptimeChan:
            // Send the uptime to all connected clients:
            for _, c := range clients {
                c <- fmt.Sprintf("%d seconds uptime", uptime)
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这相当容易。然而，如果你仔细阅读，你会注意到客户端是通过`append(clients, client)`注册的，但从未被删除。这将导致孤立的客户端，这将导致服务器在试图写入这些客户端时崩溃。为了不使代码与样板文件混杂在一起，我们将暂时不做讨论。因此，请注意，当您关闭以下部分中的浏览器选项卡时，您的服务器将会崩溃。

接下来，我们围绕新的`uptimeServer` goroutine:
编写我们的 WebSocket 服务器

```
// This upgrader is needed for WebSocket connections later:
var upgrader = websocket.Upgrader {
    ReadBufferSize: 1024,
    WriteBufferSize: 1024,
    CheckOrigin: func(r * http.Request) bool {
        return true // Disable CORS for testing
    },
}

// Start the server and keep track of the channel that it receives
// new clients on:
serverChan := make(chan chan string, 4)
go uptimeServer(serverChan)

// Define a HTTP handler function for the /status endpoint, that can receive
// WebSocket-connections only... so note that browsing it with your browser will fail.
http.HandleFunc("/status", func(w http.ResponseWriter, r *http.Request) {
    // Upgrade this HTTP connection to a WS connection:
    ws, _ := upgrader.Upgrade(w, r, nil)
    // And register a client for this connection with the uptimeServer:
    client := make(chan string, 1)
    serverChan <- client
    // And now check for uptimes written to the client indefinitely.
    // Yes, we are lacking proper error and disconnect checking here, too:
    for {
        select {
        case text, _ := <-client:
            writer, _ := ws.NextWriter(websocket.TextMessage)
            writer.Write([]byte(text))
            writer.Close()
        }
    }
})
http.ListenAndServe(":8080", nil) 
```

Enter fullscreen mode Exit fullscreen mode

唷，那是一个更难解决的问题。正如您可以从行内注释中看到的，我们做了一些事情来设置 WebSocket 基础设施，启动我们的“服务器”来计算正常运行时间，然后定义一个 HTTP 处理函数来接收客户端请求，这又会导致客户端注册到我们的正常运行时间服务器，并随后将从正常运行时间服务器接收的所有文本写入 WebSocket 客户端。

你可以编译启动这个，用这个极简的 JavaScript 例子测试一下:
[https://jsfiddle.net/L31wy1gL/13/](https://jsfiddle.net/L31wy1gL/13/)
(BTW:这就是为什么我们在代码中禁用了 code 为了能够从 js fiddle/code pen/你的虚拟 index.html 等访问 WebSocket 服务器。)

[![Minimalistic JavaScript client](../Images/1c6b597d73bad9a8dc349966b9b2502e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h1gV2X4X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0f5jn0c23tmljadtqjwh.gif)

这就是我这篇博文的结尾。你已经看到了很多:

```
• Send channels into channels
• Use this to implement a "client"-registry in a goroutine
• Send broadcast messages to these clients
• Wrap all this into a WebSocket server 
```

Enter fullscreen mode Exit fullscreen mode

但是你还有很多事情要做:

```
• Implement proper error handling
• Implement proper handling for disconnecting clients
• Maybe turn this into a library that you can re-use across your project(s) 
```

Enter fullscreen mode Exit fullscreen mode

我希望你喜欢这篇文章，并感谢评论中的建议和反馈。