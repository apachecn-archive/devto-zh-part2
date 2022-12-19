# 如何在 Go 中测试 TCP/UDP 连接-第 1 部分

> 原文：<https://dev.to/williamhgough/how-to-test-tcpudp-connections-in-go---part-1-3bga>

这篇文章最初发表在我的博客上- [如何在 Go 中测试 TCP/UDP 连接-第一部分](https://devtheweb.io/blog/2018/07/testing-net-connections-part-one//)

## 简介

在最近的一项工作任务中，我不得不通过基于包和流的协议(如 TCP 和 UDP)以 REST 风格的方式公开一个键值存储(通常通过 REST API 访问)。这对我来说是一个令人兴奋的挑战，因为我相对来说是一个专业编写 Go 的新手，我想确保一些事情:

1.  我正在编写的软件的整体完整性和可靠性
2.  清除器代码
3.  快速开发周期
4.  回归检测

对于如何与暴露的接口进行交互，我没有给出具体的要求，但是我知道编写测试将是以最有效和高效的方式解决新挑战的最佳方式。

本教程将展示我在 Golang 中以简单而有效的方式测试 TCP/UDP 连接的方法。逐步说明将提供指导，以验证您的网络连接产生所需的输出，特别是如果您是新手或测试(像我一样)。

为了这篇文章，我不会重新实现原来的项目，但是，我将创建一些类似的东西来演示这个问题，以及我发现的解决这个问题的明智而快速的方法。

## 设置

首先，让我们创建一个新的工作目录，并创建 2 个新的 Go 文件。我通常在`/go/src/github.com/me`里面工作，但这完全取决于你:

```
cd $GOPATH/src/github.com/<you>
mkdir net-testing && cd $_
touch net.go net_test.go 
```

Enter fullscreen mode Exit fullscreen mode

启动你最喜欢的编辑器(我用的是 VS 代码；它对自动格式化和保存时自动测试有很好的支持，让我们开始构建我们的应用程序。首先，在 net_test.go 中，我们需要引导应用程序在测试运行时启动。

> 名词（noun 的缩写）b:运行一个网络服务器，不管它的 TCP/UDP/HTTP，都会阻塞主 Goroutine，直到应用程序关闭或者服务器出错。

```
package net

import (
    "log"
)

var srv Server

func init() {
    // Start the new server.
    srv, err := NewServer("tcp", ":1123")
    if err != nil {
        log.Println("error starting TCP server")
        return
    }

    // Run the server in Goroutine to stop tests from blocking
    // test execution.
    go func() {
        srv.Run()
    }()
} 
```

Enter fullscreen mode Exit fullscreen mode

上面展示了我们如何声明一个 Server 类型的全局变量，我们还没有创建它，然后使用`init`函数在一个新的 Goroutine 中引导我们的服务器。接下来，我们将编写一个测试来验证服务器是否已经启动。

```
// Be sure to update your imports
import (
    "log"
    "net"
    "testing"
)

// Below init function
func TestNETServer_Run(t *testing.T) {
    // Simply check that the server is up and can
    // accept connections.
    conn, err := net.Dial("tcp", ":1123")
    if err != nil {
        t.Error("could not connect to server: ", err)
    }
    defer conn.Close()
} 
```

Enter fullscreen mode Exit fullscreen mode

现在运行测试将不可避免地导致构建失败，但是不要担心这个。既然我们已经准备好测试来检查我们的服务器运行，让我们开始服务器实现。在`net.go`中，添加以下代码:

```
package net

import (
    "errors"
    "net"
    "strings"
)

// Server defines the minimum contract our
// TCP and UDP server implementations must satisfy.
type Server interface {
    Run() error
    Close() error
}

// NewServer creates a new Server using given protocol
// and addr.
func NewServer(protocol, addr string) (Server, error) {
    switch strings.ToLower(protocol) {
    case "tcp":
        return &TCPServer{
            addr: addr,
        }, nil
    case "udp":
    }
    return nil, errors.New("Invalid protocol given")
}

// TCPServer holds the structure of our TCP
// implementation.
type TCPServer struct {
    addr   string
    server net.Listener
}

// Run starts the TCP Server.
func (t *TCPServer) Run() (err error) {
    t.server, err = net.Listen("tcp", t.addr)
    if err != nil {
        return
    }
    for {
        conn, err := t.server.Accept()
        if err != nil {
            err = errors.New("could not accept connection")
            break
        }
        if conn == nil {
            err = errors.New("could not create connection")
            break
        }
        conn.Close()
    }
    return
}

// Close shuts down the TCP Server
func (t *TCPServer) Close() (err error) {
    return t.server.Close()
} 
```

Enter fullscreen mode Exit fullscreen mode

无论你是一个围棋新手还是一个熟练的围棋程序员，请耐心听我解释。首先，我们声明一个名为`Server`的新接口类型，这样我们可以确保所有客户端使用相同的 API，因为在本系列的下一部分中，我们将构建一个`UDPServer`类型。过早地给你的应用程序添加接口通常会导致过度工程化，永远要考虑 *YAGNI* ...“你不会需要它的”。接下来，我们编写一个构建器函数，它将根据所选的协议返回一个服务器实现。最后，我们创建了隐式满足服务器接口的 TCPServer 类型，因为我们已经定义了`Run`和`Close`方法。目前，我们所取得的就是通过了我们定义的测试。常言道...红色，绿色，*重构*。

记住这一点，让我们编写一个测试来处理服务器的输出。这次我们将利用表格测试，我们将在 for 循环中运行一系列测试标准，这意味着我们可以有效地测试不同的结果，如下:

```
func TestNETServer_Request(t *testing.T) {
    tt := []struct {
        test    string
        payload []byte
        want    []byte
    }{
        {
            "Sending a simple request returns result",
            []byte("hello world\n"),
            []byte("Request received: hello world")
        },
        {
            "Sending another simple request works",
            []byte("goodbye world\n"),
            []byte("Request received: goodbye world")
        },
    }

    for _, tc := range tt {
        t.Run(tc.test, func(t *testing.T) {
            conn, err := net.Dial("tcp", ":1123")
            if err != nil {
                t.Error("could not connect to TCP server: ", err)
            }
            defer conn.Close()

            if _, err := conn.Write(tc.payload); err != nil {
                t.Error("could not write payload to TCP server:", err)
            }

            out := make([]byte, 1024)
            if _, err := conn.Read(out); err == nil {
                if bytes.Compare(out, tc.want) == 0 {
                    t.Error("response did match expected output")
                }
            } else {
                t.Error("could not read from connection")
            }
        })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的测试向我们的服务器发送一个`payload`(字节片),然后尝试从连接中读取数据，看看响应是什么。通过利用`bytes`包，我们可以测试带有`bytes.Index`的子串的索引，或者简单地测试带有`bytes.Contains`的子串。如果我们想要测试预期响应和实际响应之间的直接匹配，我们可以使用上面概述的方法，使用`bytes.Compare(a, b)`，其中 0 返回值表示`a == b`。

就目前的应用程序而言，测试将会失败，所以现在我们需要添加代码来以惯用的方式处理输入:

```
func (t *TCPServer) handleConnections() (err error) {
    for {
        conn, err := t.server.Accept()
        if err != nil || conn == nil {
            err = errors.New("could not accept connection")
            break
        }

        go t.handleConnection(conn)
    }
    return
}

func (t *TCPServer) handleConnection(conn net.Conn) {
    defer conn.Close()

    rw := bufio.NewReadWriter(bufio.NewReader(conn), bufio.NewWriter(conn))
    for {
        req, err := rw.ReadString('\n')
        if err != nil {
            rw.WriteString("failed to read input")
            rw.Flush()
            return
        }

        rw.WriteString(fmt.Sprintf("Request received: %s", req))
        rw.Flush()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

那么，我们在这里做什么？嗯，我们首先在 TCPServer 上定义一个新的方法 receiver，它将持续监听和接受任何到服务器的连接请求。如果这样做有任何问题，它会中断循环并返回错误。如果一切按计划进行，连接被传递给第二个方法，`handleConnection`，在这里我们不断地读取消息并响应客户端。使用`go`关键字允许我们在自己的 Goroutine 中处理每个连接。最重要的是，让我们不要忘记更新我们的`Run`方法来使用新的`handleConnections`。在`Run`内部，用`return t.handleConnections()`替换`conn.Close()`行，因为 handleConnections 也返回一个错误，我们可以使用方法调用作为返回值。

如果你现在用`go test -v -cover`运行测试。您应该会看到下面的输出:

```
=== RUN   TestNETServer_Running
-------- PASS: TestNETServer_Running (0.00s)
=== RUN   TestNETServer_Request
=== RUN   TestNETServer_Request/Sending_a_simple_request_returns_result
=== RUN   TestNETServer_Request/Sending_another_simple_request_works
-------- PASS: TestNETServer_Request (0.00s)
    --- PASS: TestNETServer_Request/Sending_a_simple_request_returns_result (0.00s)
    --- PASS: TestNETServer_Request/Sending_another_simple_request_works (0.00s)
PASS
coverage: 68.6% of statements 
```

Enter fullscreen mode Exit fullscreen mode

太棒了，我们有一个工作的 TCP 服务器！我们可以确信将返回我们的输出，不管实现有多简单。如果你是网络编程或测试的新手，希望你能从这篇教程中得到一些启发。正如我前面提到的，在本系列的下一期中，我将考虑添加一个`UDPServer`并更新测试以有效地覆盖两种网络协议。感谢阅读，如果你喜欢的内容，请考虑分享！

[![image](img/09df05446019ff1c9487d2cd292d20a0.png)T2】](https://i.giphy.com/media/XreQmk7ETCak0/giphy.gif)

这个例子的源代码可以在我的 GitHub 上找到:[github.com/williamhgough/devtheweb-source](https://github.com/williamhgough/devtheweb-source/tree/master/2018/July/net-testing)