# c10k 客户端的特性

> 原文：<https://dev.to/dzeban/peculiarities-of-c10k-client-1ain>

有一个众所周知的问题叫做 [c10k](https://en.wikipedia.org/wiki/C10k_problem) 。它的本质是在一台服务器上处理 10000 个并发客户机。1999 年，丹·凯格尔在[提出了这个问题，当时它促使业界重新思考网络服务器处理连接的方式。面对即将到来的 web 规模，为每个客户端分配一个线程的先进解决方案开始出现漏洞。Nginx 的诞生就是为了解决这个问题，它采用了全新的](http://www.kegel.com/c10k.html) [epoll](https://en.wikipedia.org/wiki/Epoll) 系统调用(在 Linux 中)提供的事件驱动 I/O 模型。

时代不同了，现在我们可以拥有一个非常强大的服务器，它拥有 10G 网络、32 个内核和 256 GiB RAM，可以轻松处理如此多的客户端，因此 c10k 即使使用线程 I/O 也不是太大的问题。但是，无论如何，我想检查各种解决方案(如线程和非阻塞异步 I/O)将如何处理它，所以我开始在我的 [c10k repo](https://github.com/dzeban/c10k) 中编写一些愚蠢的服务器，然后我陷入了困境，因为我需要一些工具来测试我的实现。

基本上，我需要一个 **c10k 客户端**。我实际上写了两个——一个在 Go 中，另一个用 C 语言和 *libuv* 一起写。我还打算用 Python 3 写一个带有 *asyncio* 的。

当我给每个客户写信时，我发现了两个特点——如何让它变得糟糕，如何让它变得缓慢。

## 如何让它变坏

我说的变坏是指变得真正 c10k——创建大量到服务器的连接，从而使其资源饱和。

### Go 客户端

我从 Go 中的客户端开始，很快就遇到了第一个路障。当我用简单的`"net/http"`请求发出 10 个并发 HTTP 请求时，只有 2 个 TCP 连接

```
$ lsof -p $(pgrep go-client) -n -P
COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME
go-client 11959 avd cwd DIR 253,0 4096 1183846 /home/avd/go/src/github.com/dzeban/c10k
go-client 11959 avd rtd DIR 253,0 4096 2 /
go-client 11959 avd txt REG 253,0 6240125 1186984 /home/avd/go/src/github.com/dzeban/c10k/go-client
go-client 11959 avd mem REG 253,0 2066456 3151328 /usr/lib64/libc-2.26.so
go-client 11959 avd mem REG 253,0 149360 3152802 /usr/lib64/libpthread-2.26.so
go-client 11959 avd mem REG 253,0 178464 3151302 /usr/lib64/ld-2.26.so
go-client 11959 avd 0u CHR 136,0 0t0 3 /dev/pts/0
go-client 11959 avd 1u CHR 136,0 0t0 3 /dev/pts/0
go-client 11959 avd 2u CHR 136,0 0t0 3 /dev/pts/0
go-client 11959 avd 4u a_inode 0,13 0 12735 [eventpoll]
go-client 11959 avd 8u IPv4 68232 0t0 TCP 127.0.0.1:55224->127.0.0.1:80 (ESTABLISHED)
go-client 11959 avd 10u IPv4 68235 0t0 TCP 127.0.0.1:55230->127.0.0.1:80 (ESTABLISHED) 
```

Enter fullscreen mode Exit fullscreen mode

`ss`同

```
$ ss -tnp dst 127.0.0.1:80
State Recv-Q Send-Q Local Address:Port Peer Address:Port
ESTAB 0 0 127.0.0.1:55224 127.0.0.1:80 users:(("go-client",pid=11959,fd=8))
ESTAB 0 0 127.0.0.1:55230 127.0.0.1:80 users:(("go-client",pid=11959,fd=10)) 
```

Enter fullscreen mode Exit fullscreen mode

原因很简单——HTTP 1.1 为客户端使用了带 TCP keepalive 的持久连接，以避免每次 HTTP 请求时 TCP 握手的开销。Go 的`"net/http"`完全实现了这个逻辑——它通过少量的 TCP 连接复用多个请求。可以通过 [`Transport`](https://golang.org/pkg/net/http/#Transport) 进行调谐。

但我不需要调音，我需要避免。我们可以通过`net.Dial`显式地创建 TCP 连接，然后通过这个连接发送一个请求来避免它。在这里，这个函数在一个专用的 goroutine 中同时运行。

```
func request(addr string, delay int, wg *sync.WaitGroup) {
    conn, err := net.Dial("tcp", addr)
    if err != nil {
        log.Fatal("dial error ", err)
    }

    req, err := http.NewRequest("GET", "/index.html", nil)
    if err != nil {
        log.Fatal("failed to create http request")
    }

    req.Host = "localhost"

    err = req.Write(conn)
    if err != nil {
        log.Fatal("failed to send http request")
    }

    _, err = bufio.NewReader(conn).ReadString('\n')
    if err != nil {
        log.Fatal("read error ", err)
    }

    wg.Done()
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们检查它的工作情况

```
$ lsof -p $(pgrep go-client) -n -P
COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME
go-client 12231 avd cwd DIR 253,0 4096 1183846 /home/avd/go/src/github.com/dzeban/c10k
go-client 12231 avd rtd DIR 253,0 4096 2 /
go-client 12231 avd txt REG 253,0 6167884 1186984 /home/avd/go/src/github.com/dzeban/c10k/go-client
go-client 12231 avd mem REG 253,0 2066456 3151328 /usr/lib64/libc-2.26.so
go-client 12231 avd mem REG 253,0 149360 3152802 /usr/lib64/libpthread-2.26.so
go-client 12231 avd mem REG 253,0 178464 3151302 /usr/lib64/ld-2.26.so
go-client 12231 avd 0u CHR 136,0 0t0 3 /dev/pts/0
go-client 12231 avd 1u CHR 136,0 0t0 3 /dev/pts/0
go-client 12231 avd 2u CHR 136,0 0t0 3 /dev/pts/0
go-client 12231 avd 3u IPv4 71768 0t0 TCP 127.0.0.1:55256->127.0.0.1:80 (ESTABLISHED)
go-client 12231 avd 4u a_inode 0,13 0 12735 [eventpoll]
go-client 12231 avd 5u IPv4 73753 0t0 TCP 127.0.0.1:55258->127.0.0.1:80 (ESTABLISHED)
go-client 12231 avd 6u IPv4 71769 0t0 TCP 127.0.0.1:55266->127.0.0.1:80 (ESTABLISHED)
go-client 12231 avd 7u IPv4 71770 0t0 TCP 127.0.0.1:55264->127.0.0.1:80 (ESTABLISHED)
go-client 12231 avd 8u IPv4 73754 0t0 TCP 127.0.0.1:55260->127.0.0.1:80 (ESTABLISHED)
go-client 12231 avd 9u IPv4 71771 0t0 TCP 127.0.0.1:55262->127.0.0.1:80 (ESTABLISHED)
go-client 12231 avd 10u IPv4 71774 0t0 TCP 127.0.0.1:55268->127.0.0.1:80 (ESTABLISHED)
go-client 12231 avd 11u IPv4 73755 0t0 TCP 127.0.0.1:55270->127.0.0.1:80 (ESTABLISHED)
go-client 12231 avd 12u IPv4 71775 0t0 TCP 127.0.0.1:55272->127.0.0.1:80 (ESTABLISHED)
go-client 12231 avd 13u IPv4 73758 0t0 TCP 127.0.0.1:55274->127.0.0.1:80 (ESTABLISHED)

$ ss -tnp dst 127.0.0.1:80
State Recv-Q Send-Q Local Address:Port Peer Address:Port
ESTAB 0 0 127.0.0.1:55260 127.0.0.1:80 users:(("go-client",pid=12231,fd=8))
ESTAB 0 0 127.0.0.1:55262 127.0.0.1:80 users:(("go-client",pid=12231,fd=9))
ESTAB 0 0 127.0.0.1:55270 127.0.0.1:80 users:(("go-client",pid=12231,fd=11))
ESTAB 0 0 127.0.0.1:55266 127.0.0.1:80 users:(("go-client",pid=12231,fd=6))
ESTAB 0 0 127.0.0.1:55256 127.0.0.1:80 users:(("go-client",pid=12231,fd=3))
ESTAB 0 0 127.0.0.1:55272 127.0.0.1:80 users:(("go-client",pid=12231,fd=12))
ESTAB 0 0 127.0.0.1:55258 127.0.0.1:80 users:(("go-client",pid=12231,fd=5))
ESTAB 0 0 127.0.0.1:55268 127.0.0.1:80 users:(("go-client",pid=12231,fd=10))
ESTAB 0 0 127.0.0.1:55264 127.0.0.1:80 users:(("go-client",pid=12231,fd=7))
ESTAB 0 0 127.0.0.1:55274 127.0.0.1:80 users:(("go-client",pid=12231,fd=13)) 
```

Enter fullscreen mode Exit fullscreen mode

### C 客户端

我还决定在 libuv 的基础上构建一个 C 客户端，以方便事件循环。

在我的 C 客户端中，没有 HTTP 库，所以我们从一开始就建立 TCP 连接。它通过为每个请求创建一个连接来很好地工作，所以它没有 Go 客户端的问题(更像特性:-)。但是当它读完 response 时，它会停顿，直到很长的超时才把控制返回给事件循环。

下面是看起来卡住的响应读数回调:

```
static void on_read(uv_stream_t* stream, ssize_t nread, const uv_buf_t* buf)
{
    if (nread > 0) {
        printf("%s", buf->base);
    } else if (nread == UV_EOF) {
        log("close stream");
        uv_connect_t *conn = uv_handle_get_data((uv_handle_t *)stream);
        uv_close((uv_handle_t *)stream, free_close_cb);
        free(conn);
    } else {
        return_uv_err(nread);
    }

    free(buf->base);
} 
```

Enter fullscreen mode Exit fullscreen mode

看起来我们被困在这里，要等一段(相当长的)时间，直到我们最终得到 EOF。

这个*“相当长的时间”*实际上是 nginx 中设置的 HTTP keepalive 超时[，默认为 75 秒](https://nginx.ru/en/docs/http/ngx_http_core_module.html#keepalive_timeout)。

我们可以在客户端通过属于 HTTP 1.1 的 [`Connection`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Connection) 和 [`Keep-Alive`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Keep-Alive) HTTP 头来控制它。

这是唯一合理的解决方案，因为在 libuv 端，我没有办法关闭对流——我没有收到 EOF，因为它只在连接实际关闭时发送。

我的客户端创建一个连接，发送一个请求，nginx 回复，然后 nginx 保持连接，因为它在等待后续的请求。对 libuv 的修改向我展示了这一点，这也是我喜欢用 C 语言做东西的原因——你必须深入挖掘，真正理解事物是如何工作的。

因此，为了解决这个挂起的请求，我刚刚设置了`Connection: close`头，以便对来自同一个客户端的每个请求强制执行新的连接，并禁用 HTTP keepalive。作为替代，我可以坚持使用 HTTP 1.0，因为它没有保持活动。

现在，它正在创建大量连接，让我们让它保持这些连接一个客户端指定的延迟，以显示一个缓慢的客户端。

## 如何让它慢下来

我需要让它慢下来，因为我希望我的服务器花一些时间处理请求，同时避免在服务器代码中设置睡眠。

最初，我想让客户端的读取变慢，即一次读取一个字节或者延迟读取服务器响应。有趣的是，这些解决方案都不起作用。

我通过使用 [`$request_time`](https://nginx.ru/en/docs/http/ngx_http_core_module.html#var_request_time) 变量查看访问日志，用 nginx 测试了我的客户端。不用说，我所有的请求都在 0.000 秒内得到了满足。无论我插入了什么延迟，nginx 似乎都忽略了它。

我开始通过调整请求-响应管道的各个部分来找出原因，比如连接数、响应大小等。

最后，我只能在 nginx 处理非常大的文件(比如 30 MB)时才能看到我的延迟，也就是点击的时候。

这种延迟忽略行为的全部原因是套接字缓冲区。套接字缓冲区是套接字的缓冲区，换句话说，它是 Linux 内核出于性能原因缓冲网络请求和响应的一块内存——通过网络发送大块数据，缓解客户端速度慢的问题，以及 TCP 重传等其他事情。套接字缓冲区就像页面缓存，所有的网络 I/O(页面缓存是磁盘 I/O)都是通过它进行的，除非被明确跳过。

所以在我的例子中，当 nginx 收到一个请求时，由 send/write syscall 编写的响应仅仅存储在套接字缓冲区中，但是从 nginx 的角度来看，它已经完成了。只有当响应大到套接字缓冲区容纳不下时，nginx 才会在 syscall 中被阻塞，并一直等到客户端延迟结束，套接字缓冲区被读取并被释放以用于下一部分数据。

您可以在`/proc/sys/net/ipv4/tcp_rmem`和`/proc/sys/net/ipv4/tcp_wmem`中检查和检测套接字缓冲区的大小。

所以在弄清楚这一点之后，我在建立连接之后和发送请求之前插入了 delay。

这样，服务器将保持客户端连接(耶，c10k！)用于客户指定的延迟。

## 重述

所以最后，我有 2 个 c10k 客户端——一个用 Go 编写[,另一个用 C 语言用 libuv](https://github.com/dzeban/c10k/blob/master/go-client.go) 编写[。Python 3 客户机正在路上。](https://github.com/dzeban/c10k/blob/master/libuv-client.c)

所有这些客户端都连接到 HTTP 服务器，等待指定的延迟，然后发送带有`Connection: close`头的 GET 请求。

这使得 HTTP 服务器为每个请求保持一个专用连接，并花费一些时间等待模拟 I/O。

我的 c10k 客户就是这样工作的。