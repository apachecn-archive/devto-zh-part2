# 如何在 Go 中测试 TCP/UDP 连接-第 2 部分

> 原文：<https://dev.to/williamhgough/how-to-test-tcpudp-connections-in-go---part-2-2960>

这篇文章最初发布在我的博客上: [devtheweb.io](https://devtheweb.io)
在继续之前，请确保您已经阅读了本系列的第一部分- [如何在 Go 中测试 TCP/UDP 连接-第一部分](https://dev.to/williamhgough/how-to-test-tcpudp-connections-in-go---part-1-3bga)

## 简介

在这个迷你系列的上一期中，我们看了一下在 Golang 中测试 [TCP](https://dev.to/williamhgough/how-to-test-tcpudp-connections-in-go---part-1-3bga) 连接，以及我们如何验证预期的输出。
在第 2 部分中，我们将通过添加 UDP 功能来让应用程序更进一步。在使用 UDP 或其他基于流的协议时，有几件事情需要注意，我将在下面详细说明:

1.  UDP 连接使用`net.PacketConn`接口，而不是`net.Listener`
2.  golang 中的 UDP 客户端连接只是一个`net.UDPAddr`而不是`net.Conn`，这意味着我们不能`conn.Close()`
3.  由于`net.PacketConn`没有实现`io.writer`，我们不能简单地写一个连接的客户端。

作为一个几乎没有任何语言网络编程经验的人，我在测试 UPD 连接时遇到了不少“陷阱”。我首先创建了一个我认为每个协议都能满足的接口——我错了！现在，我已经有了更多的经验，利用了 std lib 上一些精彩的文档，并从我的错误中吸取了教训，我将演示我如何解决满足相同 API 的问题，以及如何最好地与 UDP 服务通信，希望您不要像我一样遇到同样的“问题”。

## 更新测试

在真正的测试驱动方式中，在我们做任何事情之前，我们需要做一件事——更新第 1 部分的测试。让我们更新测试以反映 UDP 的期望增加。

```
var tcp, udp Server

func init() {
    // Start the new server
    tcp, err := NewServer("tcp", ":1123")
    if err != nil {
        log.Println("error starting TCP server")
        return
    }

    udp, err := NewServer("udp", ":6250")
    if err != nil {
        log.Println("error starting UDP server")
        return
    }

    // Run the servers in goroutines to stop blocking
    go func() {
        tcp.Run()
    }()
    go func() {
        udp.Run()
    }()
}

func TestNETServer_Running(t *testing.T) {
    // Simply check that the server is up and can
    // accept connections.
    servers := []struct {
        protocol string
        addr     string
    }{
        {"tcp", ":1123"},
        {"udp", ":6250"},
    }
    for _, serv := range servers {
        conn, err := net.Dial(serv.protocol, serv.addr)
        if err != nil {
            t.Error("could not connect to server: ", err)
        }
        defer conn.Close()
    }
}

func TestNETServer_Request(t *testing.T) {
    servers := []struct {
        protocol string
        addr     string
    }{
        {"tcp", ":1123"},
        {"udp", ":6250"},
    }

    tt := []struct {
        test    string
        payload []byte
        want    []byte
    }{
        {"Sending a simple request returns result", []byte("hello world\n"), []byte("Request received: hello world")},
        {"Sending another simple request works", []byte("goodbye world\n"), []byte("Request received: goodbye world")},
    }

    for _, serv := range servers {
        for _, tc := range tt {
            t.Run(tc.test, func(t *testing.T) {
                conn, err := net.Dial(serv.protocol, serv.addr)
                if err != nil {
                    t.Error("could not connect to server: ", err)
                }
                defer conn.Close()

                if _, err := conn.Write(tc.payload); err != nil {
                    t.Error("could not write payload to server:", err)
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
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里所做的只是引导新的 UDPServer，然后向每个测试添加一部分服务器。这允许我们使用唯一的协议和网络地址对每种类型的服务器连接进行测试。如果您现在运行`go test -v`，您应该会看到测试失败或出错，但是现在不要担心，我们会修复它。

接下来，在`net.go`的底部，我们将编写创建新类型`UDPServer`和实现我们在[第 1 部分](https://dev.to/williamhgough/how-to-test-tcpudp-connections-in-go---part-1-3bga) :
中定义的`Server`接口所需的最少代码

```
// UDPServer holds the necessary structure for our
// UDP server.
type UDPServer struct {
    addr   string
    server *net.UDPConn
}

// Run starts the UDP server.
func (u *UDPServer) Run() (err error) { return nil }

// Close ensures that the UDPServer is shut down gracefully.
func (u *UDPServer) Close() error { return nil } 
```

Enter fullscreen mode Exit fullscreen mode

测试仍然会失败，但是，我们现在有了构建 UDPServer 的基础。下一步是实现上面的两个方法，就像这样:

```
// Run starts the UDP server.
func (u *UDPServer) Run() (err error) {
    laddr, err := net.ResolveUDPAddr("udp", u.addr)
    if err != nil {
        return errors.New("could not resolve UDP addr")
    }

    u.server, err = net.ListenUDP("udp", laddr)
    if err != nil {
        return errors.New("could not listen on UDP")
    }

    for {
        buf := make([]byte, 2048)
        n, conn, err := u.server.ReadFromUDP(buf)
        if err != nil {
            return errors.New("could not read from UDP")
        }
        if conn == nil {
            continue
        }

        u.server.WriteToUDP([]byte(fmt.Sprintf("Request recieved: %s", buf[:n])), conn)
    }
}

// Close ensures that the UDPServer is shut down gracefully.
func (u *UDPServer) Close() error {
    return u.server.Close()
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！我们的测试现在应该都通过了，我们有一个工作的 UDP 连接*注意到这里的不同了吗？首先，在开始监听连接和启动服务器之前，我们必须解析 UDP 地址。然后我们开始接受来自服务器的请求。这与 UDP 服务器略有不同。使用`net.Listener`，我们可以只使用`.Accept()`单个连接，但是使用 UDP 连接，我们从服务器连接读取请求，并将每个请求写入缓冲区，然后我们可以使用缓冲区解析命令等。`ReadFromUDP`返回三个变量:*

 *1.  一个整数，表示写入的字节数
2.  远程连接的 UDP 地址
3.  遇到任何错误

我们可以使用第一个函数只解析写入的字节数，如上面使用`buf[:n]`的例子所示。将缓冲区的大小设置为`2048`字节允许我们监听更大的请求。同样重要的是要注意，我们必须使用服务器写入连接的 UDP 地址，而不是写入连接。由于`net.UDPConn`没有实现`io.Writer`或`io.Reader`，我们不能通过使用`bufio`包来使用与 [TCP](https://dev.to/williamhgough/how-to-test-tcpudp-connections-in-go---part-1-3bga) 相同的方法。虽然我发现这种方法是成功的，但我很想听听你对如何克服这个问题的建议，因为肯定会有更干净的方法来解决这个问题。

好了，我们已经实现了我们想要的功能，但是我们可以抽象出一些功能吗？是的，我们可以。使用与我们对使用`handleConnections`方法的 [TCP](https://dev.to/williamhgough/how-to-test-tcpudp-connections-in-go---part-1-3bga) 相同的方法，我们可以减少 Run 方法的责任，并确保两个服务器具有相同的内部 API。这对该包的任何其他开发者都有好处，因为它提供了一种与不同网络协议工作的一致方式。我们来补充一下:

```
func (u *UDPServer) Run() (err error) {
    laddr, err := net.ResolveUDPAddr("udp", u.addr)
    if err != nil {
        return errors.New("could not resolve UDP addr")
    }

    u.server, err = net.ListenUDP("udp", laddr)
    if err != nil {
        return errors.New("could not listen on UDP")
    }

    return u.handleConnections()
}

func (u *UDPServer) handleConnections() error {
    var err error
    for {
        buf := make([]byte, 2048)
        n, conn, err := u.server.ReadFromUDP(buf)
        if err != nil {
            log.Println(err)
            break
        }
        if conn == nil {
            continue
        }

        go u.handleConnection(conn, buf[:n])
    }
    return err
}

func (u *UDPServer) handleConnection(addr *net.UDPAddr, cmd []byte) {
    u.server.WriteToUDP([]byte(fmt.Sprintf("Request recieved: %s", cmd)), addr)
} 
```

Enter fullscreen mode Exit fullscreen mode

瞧啊。我们已经将原来的 Run 方法分解成两个函数，这两个函数将获得相同的结果，并使测试通过，但是以一种更干净的方式。

## 结论

这就是这个系列乡亲们！我希望在这两篇文章中，我已经阐明了当你试图通过 [TCP](https://dev.to/williamhgough/how-to-test-tcpudp-connections-in-go---part-1-3bga) & UDP 提供一个经过测试的、可靠的、简单的服务时，你可能会面临的一些差异和问题。再次感谢阅读，请考虑分享或在 twitter 上联系我 [@whg_codes](https://twitter.com/whg_codes) 。

这个例子的源代码可以在我的 github 上找到:[github.com/williamhgough/devtheweb-source](https://github.com/williamhgough/devtheweb-source/tree/v1.01.2/2018/July/net-testing)*