# 在 NATS Go 客户端使用自定义拨号器

> 原文：<https://dev.to/wallyqs/using-custom-dialers-in-the-nats-go-client-1h7a>

默认情况下，来自 [NATS](https://nats.io) 的 [Go 客户端](https://github.com/nats-io/go-nats)支持重新连接逻辑，因此每当它与服务器断开连接时，都会在放弃和关闭连接之前重试多次。

```
nc, err := nats.Connect("demo.nats.io", nats.MaxReconnects(10))
// ... 
```

Enter fullscreen mode Exit fullscreen mode

除了限制[重新连接尝试的最大次数](https://godoc.org/github.com/nats-io/go-nats#MaxReconnects)、
之外，客户端还有许多旋钮来定制它应该如何重新连接、
，例如[在连接尝试之间后退](https://godoc.org/github.com/nats-io/go-nats#ReconnectWait)，或者是否
[完全禁用重新连接](https://godoc.org/github.com/nats-io/go-nats#NoReconnect)而不启用例如[缓冲](https://godoc.org/github.com/nats-io/go-nats#ReconnectBufSize)
。

此外，它还支持事件回调，以便在客户端[断开](https://godoc.org/github.com/nats-io/go-nats#DisconnectHandler)、[重新连接](https://godoc.org/github.com/nats-io/go-nats#ReconnectHandler)或[放弃并关闭与服务器的
连接](https://godoc.org/github.com/nats-io/go-nats#ClosedHandler)时能够跟踪
。

```
nc, err := nats.Connect("demo.nats.io",
        nats.ReconnectWait(2 * time.Second),
        nats.ReconnectHandler(func(c *nats.Conn) {
            log.Println("Reconnected to", c.ConnectedUrl())
        }),
        nats.DisconnectHandler(func(c *nats.Conn) {
            log.Println("Disconnected from NATS")
        }),
        nats.ClosedHandler(func(c *nats.Conn) {
            log.Println("Closed connection to NATS")
        }),
)
// ... 
```

Enter fullscreen mode Exit fullscreen mode

另一方面，客户端完成的初始
连接尝试没有那么多旋钮。同样，如果最初连接到
一个 NATS 服务器失败，那么`nats.Connect`将返回一个错误并且
中止。

```
 nc, err = nats.Connect("127.0.0.1:4223")
    if err != nil {
        // nats: no servers available for connection
        log.Fatal(err)
    } 
```

Enter fullscreen mode Exit fullscreen mode

作为客户端的一部分，可以定义一个[自定义拨号器](https://godoc.org/github.com/nats-io/go-nats#CustomDialer)，用
替换[默认拨号器](https://golang.org/pkg/net/#Dial)，定制如何
拨号，而不必对库做进一步的内部修改，而不用暴露许多选项来管理如何拨号到 NATS
。

```
type customDialer struct {
}

func (cd *customDialer) Dial(network, address string) (net.Conn, error) {
    // ...
    return conn, nil
}

func main() {
    cd := &customDialer{}
    opts := []nats.Option{
        nats.SetCustomDialer(cd),
        // ...
    }
    nc, err = nats.Connect("demo.nats.io", opts...)
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

Go 接口的这种使用使事情变得非常灵活，因为现在
客户端可以添加自定义逻辑作为拨号器实现的一部分，并且
一切都将透明地工作。

例如，假设您想让客户端使用
`context`包来使用`DialContext`，并且能够在一个截止日期内取消
到 NATS 的连接，那么
可以如下定义一个`Dialer`实现:

```
package main

import (
    "context"
    "log"
    "net"
    "time"

    "github.com/nats-io/go-nats"
)

type customDialer struct {
    ctx             context.Context
    nc              *nats.Conn
    connectTimeout  time.Duration
    connectTimeWait time.Duration
}

func (cd *customDialer) Dial(network, address string) (net.Conn, error) {
    ctx, cancel := context.WithTimeout(cd.ctx, cd.connectTimeout)
    defer cancel()

    for {
        log.Println("Attempting to connect to", address)
        if ctx.Err() != nil {
            return nil, ctx.Err()
        }

        select {
        case <-ctx.Done():
            return nil, ctx.Err()
        default:
            d := &net.Dialer{}
            if conn, err := d.DialContext(ctx, network, address); err == nil {
                log.Println("Connected to NATS successfully")
                return conn, nil
            } else {
                time.Sleep(cd.connectTimeWait)
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用上面的拨号器实现，NATS 客户端将重试
次连接到 NATS 服务器，直到上下文不再
有效:

```
func main() {
    // Parent context cancels connecting/reconnecting altogether.
    ctx, cancel := context.WithCancel(context.Background())
    defer cancel()

    var err error
    var nc *nats.Conn
    cd := &customDialer{
        ctx:             ctx,
        nc:              nc,
        connectTimeout:  5 * time.Second,
        connectTimeWait: 1 * time.Second,
    }
    opts := []nats.Option{
        nats.SetCustomDialer(cd),
        nats.ReconnectWait(2 * time.Second),
        nats.ReconnectHandler(func(c *nats.Conn) {
            log.Println("Reconnected to", c.ConnectedUrl())
        }),
        nats.DisconnectHandler(func(c *nats.Conn) {
            log.Println("Disconnected from NATS")
        }),
        nats.ClosedHandler(func(c *nats.Conn) {
            log.Println("NATS connection is closed.")
        }),
        nats.MaxReconnects(2),
    }
    go func() {
        nc, err = nats.Connect("127.0.0.1:4222", opts...)
    }()

    c := make(chan os.Signal, 1)
    signal.Notify(c, syscall.SIGINT)
    go func() {
        for {
            select {
            case sig := <-c:
                switch sig {
                case syscall.SIGINT:
                    log.Println("Shutting down...")
                    cancel()
                    return
                }
            }
        }
    }()

WaitForEstablishedConnection:
    for {
        if err != nil {
            log.Fatal(err)
        }
        select {
        case <-ctx.Done():
            break WaitForEstablishedConnection
        default:
        }
        if nc == nil || !nc.IsConnected() {
            log.Println("Connection not ready yet...")
            time.Sleep(500 * time.Millisecond)
            continue
        }
        break WaitForEstablishedConnection
    }
    if ctx.Err() != nil {
        log.Fatal(ctx.Err())
    }

    // Run until receiving signal
    for range time.NewTicker(1 * time.Second).C {
        select {
        case <-ctx.Done():
            // Disconnect and flush pending messages
            if err := nc.Drain(); err != nil {
                log.Println(err)
            }
            log.Println("Closed connection to NATS")
            return
        default:
        }

        if nc.IsClosed() {
            break
        }
        if err := nc.Publish("hello", []byte("world")); err != nil {
            log.Println(err)
            continue
        }
        log.Println("Published message")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中使用 [DialContext](https://golang.org/pkg/net/#Dialer.DialContext) ，也为
能够挂钩到标准
库的一些跟踪特性打开了大门，例如检查连接尝试何时发生或者
TCP 连接何时建立。

在下面的例子中，使用`httptrace.ClientTrace`包
是为了通过`context`和`DialContext`激活一些内置的跟踪工具，这些工具
是标准库的一部分。

```
type customDialer struct {
    ctx             context.Context
    nc              *nats.Conn
    connectTimeout  time.Duration
    connectTimeWait time.Duration
}

func (cd *customDialer) Dial(network, address string) (net.Conn, error) {
    d := &net.Dialer{}
    trace := &httptrace.ClientTrace{
        ConnectStart: func(network, addr string) {
            log.Println("Connecting to server at", addr)
        },
        ConnectDone: func(network, addr string, err error) {
            if err == nil {
                log.Println("Established TCP connection")
            }
        },
    }
    ctx := httptrace.WithClientTrace(cd.ctx, trace)
    ctx, cancel := context.WithTimeout(ctx, cd.connectTimeout)
    defer cancel()
    for {
        if ctx.Err() != nil {
            return nil, ctx.Err()
        }

        select {
        case <-ctx.Done():
            return nil, ctx.Err()
        default:
            if conn, err := d.DialContext(ctx, network, address); err == nil {
                log.Println("Connected to NATS successfully")
                return conn, nil
            } else {
                time.Sleep(cd.connectTimeWait)
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

结果:

```
go run custom-connect.go
2018/09/05 09:02:17 Connection not ready yet...
2018/09/05 09:02:17 Attempting to connect to server at 127.0.0.1:4222
2018/09/05 09:02:17 Established TCP connection
2018/09/05 09:02:17 Connected to NATS successfully
2018/09/05 09:02:19 Published message
2018/09/05 09:02:20 Published message
2018/09/05 09:02:21 Published message
2018/09/05 09:02:22 Published message
2018/09/05 09:02:23 Published message
^C2018/09/05 09:02:23 Shutting down... 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里找到完整的例子。有兴趣了解更多信息吗？加入 [NATS 松弛频道](https://join.slack.com/t/natsio/shared_invite/enQtMzE2NDkxNDI2NTE1LTc5ZDEzYTkwYWZkYWQ5YjY1MzBjMWZmYzA5OGQxMzlkMGQzMjYxNGM3MWYxMjNiYmNjNzIwMTVjMWE2ZDgxZGM)，或者你也可以在我的书[实用 NATS](https://www.apress.com/us/book/9781484235690) 上找到更多高级 NATS 用法的例子。