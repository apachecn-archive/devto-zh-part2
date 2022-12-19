# 不要打电话给我们，我们会打电话给你

> 原文：<https://dev.to/asaf_g6/dont-call-us-we-will-call-you-3aeb>

## 动机

最近，越来越多的网站、服务器和工具开始采用 http2。大约在 2012 年开始的 SPDY 大约在 2015 年发展并被接受为 http2。

读了一些关于[http 2](https://medium.com/@whale_eat_squid/why-http-2-isnt-the-answer-35ba28ad8dc3)的好处后，我决定我必须试一试。基本上，我想建立一个非常简单的“事件驱动”的应用程序，它将使用 http2 的服务器推送。

这是我的大致想法:

*   用户获得一个 SPA ui，允许他们阅读“实时”消息
*   使用 http2 上的服务器推送来提供 ui
*   从某种队列中读取消息
*   消息一到，用户就能收到
*   消息来自某个来源(这不重要)
*   尽可能少地使用轮询

最终代码在[https://github.com/asafg6/MessagesAppPOC](https://github.com/asafg6/MessagesAppPOC)。

想知道我是怎么去的吗？继续读。

## 客户端更新

我想到的第一件事是，客户端如何从服务器接收更新。我的思路自然而然地转向了 websockets，它提供了全双工通信。Webscokets 已经存在了一段时间，并在流行的浏览器中得到支持，所以它们是一个不错的选择。然而，我想使用 http2，但 websockets 只是用它来打开初始连接，实际的流只是使用 tcp。经过一些研究，我偶然发现了马丁·查夫的这篇文章。本文比较了长轮询、websockets 和服务器发送的事件。我从未使用过服务器发送的事件，也从未从任何人那里得到过令人放心的“我们在生产中使用它”的声明。无论如何，这听起来是我想要的最好的解决方案。

下面是马丁写的关于服务器发送的事件:

> 它像预期的那样工作，起初看起来像魔术。我们得到了应用程序高效运行所需的一切。就像所有看起来好得难以置信的事情一样，我们有时会面临一些需要解决的问题。然而，它们实现起来并不复杂

我从阅读中得到的最重要的东西是:

1.  连接将通过 http2 进行多路复用
2.  断开连接的客户端将自动尝试重新连接

听起来很完美。当我选择 Go 作为服务器时，我只需要找到 SSE 的实现。

## 服务器在 Go 中发送事件

Go 为您提供了一个低级 api，您可以使用它来实现服务器发送的事件。可以投 **http。ResponseWriter** 在一个 **http。冲洗器**。你需要做的就是以正确的格式写 responseWriter，完成后调用 flusher。齐平()。

我想避免重新发明轮子，所以我开始寻找实现 SSE 服务器的 Go 包。快速谷歌搜索揭示了一些实现，例如 https://github.com/julienschmidt/sse 的和 https://github.com/alexandrevicenzi/go-sse 的(还有更多)。我查阅了资料来源，发现了很多我没想到会发现的东西。频道、地图、配置、goroutines 等。肯定比我需要的多得多，或者更准确地说，很多东西我根本不需要。我真的很想使用其中的一个包，它们的代码写得很整洁，看起来也很不错。然而，考虑到我发现的软件包上 github 星的数量很少，而花里胡哨的数量很多，我决定推出自己的。

你可以在[https://github.com/asafg6/sse_handler](https://github.com/asafg6/sse_handler)找到我的实现，大约 70 行代码，没有任何复杂之处。
基本上，它提供了一个包装“for 循环”函数的函数，您将使用该函数向客户端发送事件，并返回一个 **func(http。ResponseWriter，*http。Request)** 然后可以传递给 http . handle。
因此，经过一些编码和调试，我对我的解决方案感到满意。

## 服务器推送

多路传输很棒，但是服务器推送应该节省宝贵的毫秒。问题是，你如何提前知道客户会要求什么路径？非常简单的解决方案是在配置文件中保存一个路径列表，让服务器在启动时读取它。不要相信我的话，看看 [Nginx 1.13.9](https://www.nginx.com/blog/nginx-1-13-9-http2-server-push/) 。问题是...对于这个概念验证，我使用 [Create-React-App](https://github.com/facebook/create-react-app) 构建了一个小的 React 应用。每次你为生产构建 React 应用程序时，它都会生成带有哈希值的 js 文件(使用 webpack 插件)。顺便说一下，正则表达式支持可能会提供更好的解决方案？我决定尽可能保持简单。只需将 React 构建脚本生成的所有文件放在一个列表中，并在请求 index.html 时推送它们，假设客户端会在某个时候请求它们。
服务器推送的 go 界面是这样的:

**From[https://blog.golang.org/h2push](https://blog.golang.org/h2push)**

```
http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        if pusher, ok := w.(http.Pusher); ok {
            // Push is supported.
            if err := pusher.Push("/app.js", nil); err != nil {
                log.Printf("Failed to push: %v", err)
            }
        }
        // ...
    }) 
```

Enter fullscreen mode Exit fullscreen mode

下面是我的实现:

```
func serveIndex(w http.ResponseWriter, r *http.Request) {
    log.Println("serving index.html")
    pusher, ok := w.(http.Pusher)
    if ok {
        log.Println("Push is supported")
        for _, path := range pushPaths {
            if err := pusher.Push(path , nil); err != nil {
                log.Printf("Failed to push: %v", err)
            } else {
                log.Printf("Pushed %v", path)
            }
        }
    }
    http.ServeFile(w, r, client_dir + "/index.html")
}

func visit(path string, f os.FileInfo, err error) error {
    if !f.IsDir() && !strings.Contains(path, "index.html") {
        pushPaths = append(pushPaths, strings.TrimPrefix(path, client_dir))
    }
    return nil
}

func fillPushPaths() {
    pushPaths = make([]string, 0)
    err := filepath.Walk(client_dir, visit)
    if err != nil {
        log.Printf("filepath.Walk() returned %v\n", err)
    }
    log.Println(pushPaths)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 广播消息

在决定了基础设施之后，我必须找到一种方法在整个应用程序中高效地传播消息。goroutines 之间的标准通信方式是使用通道。经过一些研究，我发现我正在寻找的并发模式是“扇出”。在这个[堆栈溢出问题](https://stackoverflow.com/questions/16930251/go-one-producer-many-consumers)中，您可以看到一个“扇出”模式的示例。这种模式挺好看的，但是给每个消费者开一个通道好像是一种浪费。尤其是在所有消费者都需要获得相同价值的情况下。经过一番思考，我决定使用共享内存。我的想法是使用一个总是返回当前项目的链表，而不是使用通道。每个项目都有一个下一个函数，该函数将一直阻塞，直到下一个项目可用。围棋有针对此类情况的工具， **sync。Cond** 让你阻塞，直到 **cond.wait()** 有事发生。当有事发生时，你可以使用**广播**来唤醒所有等待的 goroutines。
下面是我一开始想出来的:

```
package messages

import (
    "sync"
)

type BroadcastChannel struct {
    channelItem *ChannelItem
    lock sync.Mutex
}

func (channel *BroadcastChannel) Listen() *ChannelItem {
    channel.lock.Lock()
    defer channel.lock.Unlock()
    return channel.channelItem
}

func (channel *BroadcastChannel) Publish(data interface{}) {
    channel.lock.Lock()
    defer channel.lock.Unlock()
    newItem := MakeNewChannelItem(data)
    channel.channelItem.setNext(newItem)
    channel.channelItem = newItem
}

func MakeNewBroadcastChannel() *BroadcastChannel {
    m := sync.Mutex{}
    nilItem := MakeNewChannelItem(nil)
    return &BroadcastChannel{channelItem: nilItem, lock: m}
}

type ChannelItem struct {
    next *ChannelItem
    cond *sync.Cond
    data interface{}
}

func (channelItem *ChannelItem) GetNextMessageOrWait() *ChannelItem {
    channelItem.cond.L.Lock()
    defer channelItem.cond.L.Unlock()
    for channelItem.next == nil {
        channelItem.cond.Wait()
    }
    return channelItem.next
}

func (channelItem *ChannelItem) setNext(next *ChannelItem) {
    channelItem.cond.L.Lock()
    defer channelItem.cond.L.Unlock()
    channelItem.next = next
    channelItem.cond.Broadcast()

}

func (channelItem *ChannelItem) GetData() interface{} {
    return channelItem.data
}

func MakeNewChannelItem(data interface{}) *ChannelItem {
    m := sync.Mutex{}
    cond := sync.NewCond(&m)
    return &ChannelItem{data:data, cond:cond}
} 
```

Enter fullscreen mode Exit fullscreen mode

这个实现的行为有点像一个通道，在有值之前一直阻塞。在更多的编码之后，我发现这个实现有一个很强的缺点——它不能被取消。在客户端断开连接的情况下，代码会一直阻塞，goroutine 会变成内存泄漏。没有办法从外部取消一个 goroutine，它必须自己“死”。Go 的 SSE 接口为您提供了一个通道，告诉您客户端何时断开连接，因此我自然希望有一种方法来使用它。
这是我的解决方案:

```
...

func (channelItem *ChannelItem) GetNextMessageOrWaitWithClose(close <- chan bool) *ChannelItem {

    waitChan := make( chan struct{})
    go func() {
        channelItem.cond.L.Lock()
        defer channelItem.cond.L.Unlock()
        channelItem.cond.Wait()
        waitChan <- struct {}{}
    } ()
    select {
    case _, _ = <- waitChan:
        return channelItem.next
    case _, ok := <- close:
        if ok {
            return nil
        }

    }
    return channelItem.next
}
... 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，最后我不得不使用我试图避免的东西...每个 goroutine 的通道。然而，这个通道是空的，所以它几乎不占用任何内存，所以它是较小的邪恶。如果 Go 的 select 块支持等待一个变量，我可以避免使用 **waitChan** ，但是不管怎样，它完成了工作。

## 最终还是要有人投票

PubSub 系统是广播消息的明显工具。我需要的是一个 PubSub，它可以让你从一个生产者发布到许多消费者，并且可以集群化。经过一番搜索，我发现 Redis PUBSUB 非常适合。Redis PUBSUB 是可伸缩的，可以选择从多个生产者向多个消费者发布消息。客户端使用长轮询，它仍在轮询..但我想最终还是要有人投票的。可能有其他工具我可以在这里使用，但由于这不是我的主要关注点，我只使用了第一个合适的工具。

## 客户端

正如我已经提到的，客户端是一个非常简单的 React 应用程序。它侦听来自服务器的事件，并显示传入的消息。

SSE 用 Javascript 实现非常简单，下面是我的 Events 类:

```
 class Events {

    constructor() {
        this.client = new EventSource('https://' + window.location.host + '/messages');
    }

    register(eventName, handler) {
        this.client.addEventListener(eventName, handler);
    }

    unRegister(eventName, handler) {
        this.client.removeEventListener(eventName, handler);
    }

}

export default Events; 
```

Enter fullscreen mode Exit fullscreen mode

和客户端代码:

```
 import React, { Component } from 'react';

class MessageList extends Component {

    constructor(props) {
        super(props);
        this.state = {messages: []}
    }

    componentWillMount() {
        const { events, channel } = this.props;
        events.register(channel, (e) => { 
            this.addMessage(JSON.parse(e.data)); 
        })
    }

    componentWillUnmount() {
        const { events, channel } = this.props;
        events.unRegister(channel, (e) => { this.addMessage(e) })

    }

    addMessage(e) {
        let messages = this.state.messages.slice();
        messages.push(e);
        this.setState({ messages });
    }

    render() {
        const { messages } = this.state; 
        return (
            <div className="message-box" style={{backgroundColor: this.props.color}}>
            { messages.map((message) => {
                return (
                    <div className="msg" key={message.id.toString()} >
                        <p>{message.data}</p>
                    </div>
                );
            }) }
            </div>
        );
    }
}

export default MessageList; 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

所有部分共同构成了以下流程:

客户流量:

1.  客户端连接到应用程序，所有资源都使用 pusher 推送
2.  Eventsource 连接到 SSE 处理程序
3.  组件注册事件并等待数据
4.  事件进来，数据显示在屏幕上

服务器流程:

1.  消息被推送到 Redis 发布订阅
2.  Redis 客户端获取消息并将其推送到广播频道
3.  SSE 层从 BroadcastChannel 获取消息，并向客户端发送事件

我希望你喜欢，当然，请随意评论或分享。

感谢阅读。