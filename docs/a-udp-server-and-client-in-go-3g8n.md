# Go 中的 UDP 服务器和客户端

> 原文：<https://dev.to/cirowrc/a-udp-server-and-client-in-go-3g8n>

嘿，

虽然在 Golang 中常见到 TCP 服务器的实现，但在 UDP 中却不常见。

除了 UDP 和 TCP 之间的许多差异之外，使用 Go 感觉它们非常相似，除了每个协议细节中出现的一些小细节。

如果你觉得一些 Golang UDP 知识很有价值，一定要坚持到底。

此外，本文还讨论了 Golang 使用的系统调用时 TCP 和 UDP 之间的基本差异，以及对这些系统调用被调用时内核所做的一些分析。

敬请期待！

### 概述

作为这篇博文的目标，最终的实现应该看起来像一个“回显通道”，无论客户端向服务器写入什么，服务器都会回显。

```
 .---> HELLO!! -->-.
        |                 |
client -*                 *--> server --.
   ^                                    |
   |                                    |
   *---<----- HELLO!! ---------<--------* 
```

Enter fullscreen mode Exit fullscreen mode

由于 UDP 是一种不保证可靠传输的协议，所以可能是服务器接收到消息，也可能是客户端接收到来自服务器的回应。

流程**可能**成功完成(或者不成功)。

```
 .---> HELLO!! -->-.
        |                 |
client -*                 *--> server --.
                                        |
                                        |
            whoops, lost! -----<--------* 
```

Enter fullscreen mode Exit fullscreen mode

不是面向连接的，客户端不会真的“建立连接”，就像在 TCP 中一样；每当消息到达服务器时，它不会“向连接写回响应”，它只会将消息定向到写入它的地址。

记住这一点，流程应该是这样的:

```
TIME    DESCRIPTION

t0      client and server exist

          client                         server
          10.0.0.1                       10.0.0.2

t1      client sends a message to the server

          client      ------------>      server
          10.0.0.1         msg           10.0.0.2
                       (from:10.0.0.1)
                       (to:  10.0.0.2)

t2      server receives the message, then it takes
        the address of the sender and then prepares
        another message with the same contents and
        then writes it back to the client

          client      <------------      server
          10.0.0.1         msg2          10.0.0.2
                       (from:10.0.0.1)
                       (to:  10.0.0.2)

t3      client receives the message

          client                         server
          10.0.0.1                       10.0.0.2

           thxx!! :D :D

ps.: ports omitted for brevity 
```

Enter fullscreen mode Exit fullscreen mode

也就是说，让我们看看这个故事如何在 Go 中展开。

> 如果你真的想深入了解一下，一定要考虑这些书:
> 
> *   [计算机网络:自上而下的方法](https://amzn.to/2DEiNOG)
> *   [Unix 网络编程，第 1 卷:套接字网络 API(第 3 版)](https://amzn.to/2zuuMu1)
> *   [Linux 编程接口](https://amzn.to/2QWyXp9)
> 
> 第一种方法是从网络堆栈的最高层(应用层)开始，然后深入到最底层，解释协议的细节，如果您需要一个关于网络概念的极好的复习，而不需要深入到实现细节，请查看这一篇！
> 
> 另外两个是关于 Linux 和 Unix 的——如果你更关注实现的话，这是非常值得的。
> 
> 好好读书！

### 使用 Go 发送 UDP 数据包

从整个实现开始(充满注释)，我们可以开始描绘它，一点一点地理解，直到我们可以理解幕后发生的每一个交互。

```
// client wraps the whole functionality of a UDP client that sends
// a message and waits for a response coming back from the server
// that it initially targetted.
func client(ctx context.Context, address string, reader io.Reader) (err error) {
    // Resolve the UDP address so that we can make use of DialUDP
    // with an actual IP and port instead of a name (in case a
    // hostname is specified).
    raddr, err := net.ResolveUDPAddr("udp", address)
    if err != nil {
        return
    }

    // Although we're not in a connection-oriented transport,
    // the act of `dialing` is analogous to the act of performing
    // a `connect(2)` syscall for a socket of type SOCK_DGRAM:
    // - it forces the underlying socket to only read and write
    // to and from a specific remote address.
    conn, err := net.DialUDP("udp", nil, raddr)
    if err != nil {
        return
    }

    // Closes the underlying file descriptor associated with the,
    // socket so that it no longer refers to any file.
    defer conn.Close()

    doneChan := make(chan error, 1)

    go func() {
        // It is possible that this action blocks, although this
        // should only occur in very resource-intensive situations:
        // - when you've filled up the socket buffer and the OS
        // can't dequeue the queue fast enough.
        n, err := io.Copy(conn, reader)
        if err != nil {
            doneChan <- err
            return
        }

        fmt.Printf("packet-written: bytes=%d\n", n)

        buffer := make([]byte, maxBufferSize)

        // Set a deadline for the ReadOperation so that we don't
        // wait forever for a server that might not respond on
        // a resonable amount of time.
        deadline := time.Now().Add(*timeout)
        err = conn.SetReadDeadline(deadline)
        if err != nil {
            doneChan <- err
            return
        }

        nRead, addr, err := conn.ReadFrom(buffer)
        if err != nil {
            doneChan <- err
            return
        }

        fmt.Printf("packet-received: bytes=%d from=%s\n",
            nRead, addr.String())

        doneChan <- nil
    }()

    select {
    case <-ctx.Done():
        fmt.Println("cancelled")
        err = ctx.Err()
    case err = <-doneChan:
    }

    return
} 
```

Enter fullscreen mode Exit fullscreen mode

有了客户端代码，我们现在可以描述它，探索它的每一个细微差别。

### 地址解析

在我们开始创建一个套接字并向服务器发送信息之前，首先发生的是一个名称解析，它将给定的名称(比如，`google.com`)转换成一组 IP 地址(比如，`8.8.8.8`)。

我们在代码中的做法是调用 [net。ResolveUDPAddr](https://golang.org/pkg/net/#ResolveUDPAddr) ，在 Unix 环境中，通过下面的栈一直执行 DNS 解析:

```
(in a given goroutine ...)
    >> 0 0x00000000004e5dc5 in net.(*Resolver).goLookupIPCNAMEOrder
           at /usr/local/go/src/net/dnsclient_unix.go:553
    >> 1 0x00000000004fbe69 in net.(*Resolver).lookupIP
           at /usr/local/go/src/net/lookup_unix.go:101
        2 0x0000000000514948 in net.(*Resolver).lookupIP-fm
           at /usr/local/go/src/net/lookup.go:207
        3 0x000000000050faca in net.glob..func1
           at /usr/local/go/src/net/hook.go:19
    >> 4 0x000000000051156c in net.(*Resolver).LookupIPAddr.func1
           at /usr/local/go/src/net/lookup.go:221
        5 0x00000000004d4f7c in internal/singleflight.(*Group).doCall
           at /usr/local/go/src/internal/singleflight/singleflight.go:95
        6 0x000000000045d9c1 in runtime.goexit
           at /usr/local/go/src/runtime/asm_amd64.s:1333

(in another goroutine...)
0 0x0000000000431a74 in runtime.gopark
   at /usr/local/go/src/runtime/proc.go:303
1 0x00000000004416dd in runtime.selectgo
   at /usr/local/go/src/runtime/select.go:313
2 0x00000000004fa3f6 in net.(*Resolver).LookupIPAddr <<
   at /usr/local/go/src/net/lookup.go:227
3 0x00000000004f6ae9 in net.(*Resolver).internetAddrList <<
   at /usr/local/go/src/net/ipsock.go:279
4 0x000000000050807d in net.ResolveUDPAddr <<
   at /usr/local/go/src/net/udpsock.go:82
5 0x000000000051e63b in main.main
   at ./resolve.go:14
6 0x0000000000431695 in runtime.main
   at /usr/local/go/src/runtime/proc.go:201
7 0x000000000045d9c1 in runtime.goexit
   at /usr/local/go/src/runtime/asm_amd64.s:1333 
```

Enter fullscreen mode Exit fullscreen mode

如果我没弄错的话，整个过程是这样的:

1.  它检查我们是否给出了一个已经存在的 IP 地址或主机名；如果是主机名，那么
2.  根据系统指定的查找顺序，使用本地解析程序查找主机；然后，
3.  最终执行一个实际的 DNS 请求，请求该域的记录；然后，
4.  如果所有这些都成功了，一个 IP 地址列表被检索出来，然后根据一个 [RFC](https://tools.ietf.org/html/rfc6724) 进行分类；这给了我们列表中最高优先级的 IP。

按照上面的堆栈跟踪，您应该能够自己看到“神奇”发生的源代码(这是一件有趣的事情！).

选择 IP 地址后，我们可以继续了。

注意。:这个过程对于 TCP 来说是**而不是**不同的。

> 《计算机网络:自上而下的方法》这本书有很大一部分是关于 DNS 的。
> 
> 我**真的**推荐你通过它了解更多。
> 
> 我还写了一篇博文，讲述了如何使用 Go 从零开始编写能够解析`A`记录的东西:使用 Go 从零开始编写 DNS 消息。

### TCP 拨号 vs UDP 拨号

不是使用 TCP 常用的常规 [`Dial`](https://golang.org/pkg/net/#Dial) ，对于我们的 UDP 客户端，使用了不同的方法: [`DialUDP`](https://golang.org/pkg/net/#DialUDP) 。

这样做的原因是，我们可以强制执行所传递的地址类型，以及接收专门的连接:用“具体类型” [`UDPConn`](https://golang.org/pkg/net/#UDPConn) 来代替通用的 [`Conn`](https://golang.org/pkg/net/#Conn) 接口。

虽然`Dial`和`DialUDP`听起来可能一样(甚至当谈到与内核对话时使用的系统调用时)，但它们最终在网络堆栈实现方面有很大不同。

例如，我们可以检查两种方法是否都使用了 [`connect(2)`](http://man7.org/linux/man-pages/man2/connect.2.html) :

*   传输控制协议（Transmission Control Protocol）

```
// TCP - performs an actual `connect` under the hood,
// trying to establish an actual connection with the
// other side.
net.Dial("tcp", "1.1.1.1:53")

// strace -f -e trace=network ./main
// [pid 4891] socket(
        AF_INET, 
 -----> SOCK_STREAM|SOCK_CLOEXEC|SOCK_NONBLOCK, 
        IPPROTO_IP) = 3
// [pid 4891] connect(3, {sa_family=AF_INET, sin_port=htons(53), sin_addr=inet_addr("1.1.1.1")}, 16) = -1 EINPROGRESS (Operation now in progress)
... 
```

Enter fullscreen mode Exit fullscreen mode

*   用户数据报协议(User Datagram Protocol)

```
// UDP - calls `connect` just like TCP, but given that
// the arguments are different (it's not SOCK_STREAM),
// the semantics differ - it constrains the socket 
// regarding to whom it might communicate with.
net.Dial("udp", "1.1.1.1:53")

// strace -f -e trace=network ./main
// [pid 5517] socket(
        AF_INET, 
 -----> SOCK_DGRAM|SOCK_CLOEXEC|SOCK_NONBLOCK, 
        IPPROTO_IP) = 3
// [pid 5517] connect(3, {sa_family=AF_INET, sin_port=htons(53), sin_addr=inet_addr("1.1.1.1")}, 16) = 0
... 
```

Enter fullscreen mode Exit fullscreen mode

虽然它们几乎相同，但是从文档中我们可以看到它们在语义上的不同，这取决于我们配置通过发生在`connect(2)`之前的`socket(2)`调用创建的`socket`的方式:

> 如果 socket sockfd 的类型是 **SOCK_DGRAM** ，那么 addr 就是默认情况下数据报发送到的地址，也是接收数据报的唯一地址。
> 
> 如果套接字的类型为 **SOCK_STREAM** 或 SOCK_SEQ‐PACKET，则该调用尝试连接到绑定到 addr 指定地址的套接字。

我们应该能够验证 TCP 传输中的`Dial`方法会执行真正连接到另一端的行为吗？当然可以！

```
./tools/funccount -p $(pidof main) 'tcp_*'
Tracing 316 functions for "tcp_*"... Hit Ctrl-C to end.
^C
FUNC COUNT
tcp_small_queue_check.isra.28 1
tcp_current_mss 1
tcp_schedule_loss_probe 1
tcp_mss_to_mtu 1
tcp_write_queue_purge 1
tcp_write_xmit 1
tcp_select_initial_window 1
tcp_fastopen_defer_connect 1
tcp_mtup_init 1
tcp_v4_connect 1
tcp_v4_init_sock 1
tcp_rearm_rto.part.61 1
tcp_close 1
tcp_connect 1
tcp_send_fin 1
tcp_rearm_rto 1
tcp_tso_segs 1
tcp_event_new_data_sent 1
tcp_check_oom 1
tcp_clear_retrans 1
tcp_init_xmit_timers 1
tcp_init_sock 1
tcp_initialize_rcv_mss 1
tcp_assign_congestion_control 1
tcp_sync_mss 1
tcp_init_tso_segs 1
tcp_stream_memory_free 1
tcp_setsockopt 1
tcp_chrono_stop 2
tcp_rbtree_insert 2
tcp_set_state 2
tcp_established_options 2
tcp_transmit_skb 2
tcp_v4_send_check 2
tcp_rate_skb_sent 2
tcp_options_write 2
tcp_poll 2
tcp_release_cb 4
tcp_v4_md5_lookup 4
tcp_md5_do_lookup 4 
```

Enter fullscreen mode Exit fullscreen mode

但是在 UDP 的情况下，理论上，它只是负责标记套接字，以便对指定的地址进行读写。

通过与 TCP 相同的过程(从查看 syscall 接口开始)，我们可以追踪到`DialUDP`和`Dial`使用的底层内核方法，看看它们有什么不同:

```
./tools/funccount -p $(pidof main) 'udp_*'
Tracing 57 functions for "udp_*"... Hit Ctrl-C to end.
^C
FUNC COUNT
udp_v4_rehash 1
udp_poll 1
udp_v4_get_port 1
udp_lib_close 1
udp_lib_lport_inuse 1
udp_init_sock 1
udp_lib_unhash 1
udp_lib_rehash 1
udp_lib_get_port 1
udp_destroy_sock 1 
```

Enter fullscreen mode Exit fullscreen mode

少得多。

如果我们更进一步，尝试探究在这些调用中的每一个调用中发生了什么，我们可以注意到`connect(2)`在 TCP 的情况下如何真正地传输数据(例如，建立执行握手):

```
PID TID COMM FUNC
6747 6749 main tcp_transmit_skb
        tcp_transmit_skb+0x1
        tcp_v4_connect+0x3f5
        __inet_stream_connect+0x238
        inet_stream_connect+0x3b
        SYSC_connect+0x9e
        sys_connect+0xe
        do_syscall_64+0x73
        entry_SYSCALL_64_after_hwframe+0x3d 
```

Enter fullscreen mode Exit fullscreen mode

而在 UDP 的情况下，不传输任何东西，只执行一些设置:

```
PID TID COMM FUNC
6815 6817 main ip4_datagram_connect
        ip4_datagram_connect+0x1 [kernel]
        SYSC_connect+0x9e [kernel]
        sys_connect+0xe [kernel]
        do_syscall_64+0x73 [kernel]
        entry_SYSCALL_64_after_hwframe+0x3d [kernel] 
```

Enter fullscreen mode Exit fullscreen mode

如果你还不相信这两者真的不同(TCP 发送数据包来建立连接，而 UDP 没有)，我们可以在网络堆栈中设置一些触发器来告诉我们数据包何时流动:

```
# By creating a rule that will only match
# packets destined at `1.1.1.1` and that
# match a specific protocol, we're able
# to see what happens at the time that
# `connect(2)` happens with a given protocol
# or another.
iptables \
        --table filter \
        --insert OUTPUT \
        --jump LOG \
        --protocol udp \
        --destination 1.1.1.1 \
        --log-prefix="[UDP] "

iptables \
        --table filter \
        --insert OUTPUT \
        --jump LOG \
        --protocol tcp \
        --destination 1.1.1.1 \
        --log-prefix="[TCP] " 
```

Enter fullscreen mode Exit fullscreen mode

现在，对一个 TCP 目标和`DialUDP`目标运行`Dial`，并比较它们之间的差异。

您应该只看到`[TCP]`日志:

```
[46260.105662] [TCP] IN= OUT=enp0s3 DST=1.1.1.1 SYN URGP=0
[46260.120454] [TCP] IN= OUT=enp0s3 DST=1.1.1.1 ACK URGP=0
[46260.120718] [TCP] IN= OUT=enp0s3 DST=1.1.1.1 ACK FIN URGP=0
[46260.150452] [TCP] IN= OUT=enp0s3 DST=1.1.1.1 ACK URGP=0 
```

Enter fullscreen mode Exit fullscreen mode

> 如果你不熟悉`dmesg`的内部运作，可以看看我的另一篇博文- [dmesg under the hood](https://ops.tips/blog/dmesg-under-the-hood) 。
> 
> 顺便说一下，[Linux 编程接口](https://amzn.to/2QWyXp9)是一本了解更多关于套接字和其他相关主题的好书！

### 写入一个 UDP“连接”

随着我们的 UDP 套接字被正确地创建并配置为特定的地址，我们现在按时通过“写入”路径——当我们实际上获取一些数据并写入从`net.DialUDP`接收的`UDPConn`对象时。

一个只向给定的 UDP 服务器发送少量数据的示例程序如下:

```
// Perform the address resolution and also
// specialize the socket to only be able
// to read and write to and from such
// resolved address.
conn, err := net.Dial("udp", *addr)
if err != nil {
        panic(err)
}
defer conn.Close()

// Call the `Write()` method of the implementor
// of the `io.Writer` interface.
n, err = fmt.Fprintf(conn, "something") 
```

Enter fullscreen mode Exit fullscreen mode

假设由`Dial`返回的`conn`实现了`io.Writer`接口，我们可以使用类似于`fmt.Fprintf`(它将一个`io.Writer`作为它的第一个参数)的东西，让它用我们传递给它的消息调用`Write()`。

> 如果你还不清楚接口和其他 Golang 概念，一定要去看看 Kernighan 的书:[Go 编程语言](https://amzn.to/2R7XdVe)。
> 
> 是的，来自与丹尼斯·里奇一起编写 C 语言《T2》的人。

在底层， [`UDPConn`](https://golang.org/pkg/net/#UDPConn) 通过组合 [`conn`](https://github.com/golang/go/blob/a0e7f12771c2e84e626dcf5e30da5d62a3b1adf6/src/net/net.go#L164-L166) 实现了来自 [`io.Writer`](https://golang.org/pkg/io/#Writer) 接口的`Write()`方法，该结构实现了关于写入和读取给定文件描述符的最基本方法:

```
type conn struct {
    fd *netFD
}

// Write implements the Conn Write method.
func (c *conn) Write(b []byte) (int, error) {
    if !c.ok() {
        return 0, syscall.EINVAL
    }
    n, err := c.fd.Write(b)
    if err != nil {
        err = &OpError{Op: "write", Net: c.fd.net, Source: c.fd.laddr, Addr: c.fd.raddr, Err: err}
    }
    return n, err
}

// UDPConn is the implementation of the Conn 
// and PacketConn interfaces for UDP network 
// connections.
type UDPConn struct {
    conn
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，知道了最后`fmt.Fprintf(conn, "something")`在文件描述符(UDP 套接字)的`write(2)`中结束，我们可以进一步研究，看看内核路径如何寻找这样的`write(2)`调用:

```
PID TID COMM FUNC
14502 14502 write.out ip_send_skb
        ip_send_skb+0x1 
        udp_sendmsg+0x3b5 
        inet_sendmsg+0x2e 
        sock_sendmsg+0x3e 
        sock_write_iter+0x8c 
        new_sync_write+0xe7 
        __vfs_write+0x29 
        vfs_write+0xb1 
        sys_write+0x55 
        do_syscall_64+0x73 
        entry_SYSCALL_64_after_hwframe+0x3d 
```

Enter fullscreen mode Exit fullscreen mode

此时，数据包应该已经到达通信通道的另一端。

### 在客户端接收来自 UDP 的“连接”

从`UDPConn`接收的行为可以被视为与“写路径”几乎相同，只是此时提供了一个缓冲区(以便它可以填充到达的内容)，并且我们并不真正知道我们必须等待内容到达多长时间。

例如，我们可以用下面的代码路径从一个已知的地址读取:

```
buf := make([]byte, *bufSize)
_, err = conn.Read(buf) 
```

Enter fullscreen mode Exit fullscreen mode

这将变成一个幕后的`read(2)`系统调用，然后经过`vfs`并从一个套接字
变成一个`read`

```
22313 22313 read __skb_recv_udp
        __skb_recv_udp+0x1 
        inet_recvmsg+0x51 
        sock_recvmsg+0x43 
        sock_read_iter+0x90 
        new_sync_read+0xe4 
        __vfs_read+0x29 
        vfs_read+0x8e 
        sys_read+0x55 
        do_syscall_64+0x73 
        entry_SYSCALL_64_after_hwframe+0x3d 
```

Enter fullscreen mode Exit fullscreen mode

重要的是要记住，当从套接字读取时，这将是一个阻塞操作。

鉴于消息可能永远不会从这样的套接字返回，我们可能会永远等待下去。

为了避免这种情况，我们可以设定一个阅读期限，如果我们等得太久，就会把整个事情都扼杀掉

```
buf := make([]byte, *bufSize)

// Sets the read deadline for now + 15seconds.
// If you plan to read from the same connection again,
// make sure you expand the deadline before reading
// it.
conn.SetReadDeadline(time.Now().Add(15 * time.Second))
_, err = conn.Read(buf) 
```

Enter fullscreen mode Exit fullscreen mode

现在，万一对方回答时间太长:

```
read udp 10.0.2.15:41745->1.1.1.1:53: i/o timeout 
```

Enter fullscreen mode Exit fullscreen mode

### 接收来自服务器中 UDP 的“连接”

虽然这对`client`(我们知道我们在读谁的信息)来说很好，但对服务器来说却不是。

原因是在服务器端，我们不知道从谁那里读取数据(地址未知)。

与 TCP 服务器的情况不同，我们有`accept(2)`返回给服务器实现者服务器可以写入的连接，在 UDP 的情况下，没有所谓的“写入连接”。只有一个“写给谁”，可以通过检查到达的数据包来检索。

```
WITH READ

  "Hmmm, let me write something to
   my buddy at 1.1.1.1:53"

   client --.
            |
            | client: n, err := udpConn.Write(buf)
            | server: n, err := udpConn.Read(buf)
            |
            *---> server
                  "Oh, somebody wrote me something!
                   I'd like to write back to him/her,
                   but, what's his/her address?

                   I don't have a connection... I need
                   an address to write to! I can't to
                   a thing now!"

WITH READFROM

   client --.
            |
            | client: n, err := udpConn.Write(buf)
            | server: n, address, err := udpConn.Read(buf)
            |
            *---> server
                  "Oh, looking at the packet, I can
                   see that my friend Jane wrote to me,
                   I can see that from `address`!

                   Let me answer her back!" 
```

Enter fullscreen mode Exit fullscreen mode

为此，在服务器上，我们需要专门的连接: [`UDPConn`](https://golang.org/pkg/net/#UDPConn) 。

这种专门的连接能够为我们提供`ReadFrom`，一种不仅仅是从文件描述符中读取内容并将内容添加到缓冲区的方法，它还检查数据包的报头，并向我们提供关于谁发送了数据包的信息。

它的用法是这样的:

```
buffer := make([]byte, 1024)

// Given a buffer that is meant to hold the
// contents from the messages arriving at the
// socket that `udpConn` wraps, it blocks until
// messages arrive. 
//
// For each message arriving, `ReadFrom` unwraps
// the message, getting information about the
// sender from the protocol headers and then
// fills the buffer with the data.
n, addr, err := udpConn.ReadFrom(buffer)
if err != nil {
        panic(err)
} 
```

Enter fullscreen mode Exit fullscreen mode

试图理解事情如何在幕后运作的一个有趣的方法是查看[计划 9](https://en.wikipedia.org/wiki/Plan_9_from_Bell_Labs) 实施( [`net/udpsock_plan9.go`](https://github.com/golang/go/blob/a0e7f12771c2e84e626dcf5e30da5d62a3b1adf6/src/net/udpsock_plan9.go#L14-L28) )。

下面是它的样子(加上我自己的评论):

```
func (c *UDPConn) readFrom(b []byte) (n int, addr *UDPAddr, err error) {
        // creates a buffer a little bit bigger than
        // the one we provided (to account for the header of
        // the UDP headers)
    buf := make([]byte, udpHeaderSize+len(b))

        // reads from the underlying file descriptor (this might
        // block).
    m, err := c.fd.Read(buf)
    if err != nil {
        return 0, nil, err
    }
    if m < udpHeaderSize {
        return 0, nil, errors.New("short read reading UDP header")
    }

        // strips out the parts that were not readen
    buf = buf[:m]

        // interprets the UDP header
    h, buf := unmarshalUDPHeader(buf)

        // copies the data back to our supplied buffer
        // so that we only receive the data, not the header.
    n = copy(b, buf)
    return n, &UDPAddr{IP: h.raddr, Port: int(h.rport)}, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

自然，在 Linux 下，`readFrom`不会走这条路。它使用`recvfrom`来完成整个“UDP 头解释”,但是想法是一样的(除了`plan9`是在用户空间完成的)。

为了验证在 Linux 下我们使用`recvfrom`的事实，我们跟踪`UDPConn.ReadFrom`(你可以使用[`delve`](https://github.com/derekparker/delve)):

```
0 0x00000000004805b8 in syscall.recvfrom
   at /usr/local/go/src/syscall/zsyscall_linux_amd64.go:1641
1 0x000000000047e84f in syscall.Recvfrom
   at /usr/local/go/src/syscall/syscall_unix.go:262
2 0x0000000000494281 in internal/poll.(*FD).ReadFrom
   at /usr/local/go/src/internal/poll/fd_unix.go:215
3 0x00000000004f5f4e in net.(*netFD).readFrom
   at /usr/local/go/src/net/fd_unix.go:208
4 0x0000000000516ab1 in net.(*UDPConn).readFrom
   at /usr/local/go/src/net/udpsock_posix.go:47
5 0x00000000005150a4 in net.(*UDPConn).ReadFrom
   at /usr/local/go/src/net/udpsock.go:121
6 0x0000000000526bbf in main.server.func1
   at ./main.go:65
7 0x000000000045e1d1 in runtime.goexit
   at /usr/local/go/src/runtime/asm_amd64.s:1333 
```

Enter fullscreen mode Exit fullscreen mode

在内核层面，我们也可以检查一下涉及到哪些方法:

```
24167 24167 go-sample-udp __skb_recv_udp
        __skb_recv_udp+0x1 
        inet_recvmsg+0x51 
        sock_recvmsg+0x43 
        SYSC_recvfrom+0xe4 
        sys_recvfrom+0xe 
        do_syscall_64+0x73 
        entry_SYSCALL_64_after_hwframe+0x3d 
```

Enter fullscreen mode Exit fullscreen mode

### 一个 UDP 服务器在 Go

现在，转到服务器端的实现，下面是代码看起来的样子(大量注释):

```
// maxBufferSize specifies the size of the buffers that
// are used to temporarily hold data from the UDP packets
// that we receive.
const maxBufferSize = 1024

// server wraps all the UDP echo server functionality.
// ps.: the server is capable of answering to a single
// client at a time.
func server(ctx context.Context, address string) (err error) {
    // ListenPacket provides us a wrapper around ListenUDP so that
    // we don't need to call `net.ResolveUDPAddr` and then subsequentially
    // perform a `ListenUDP` with the UDP address.
    //
    // The returned value (PacketConn) is pretty much the same as the one
    // from ListenUDP (UDPConn) - the only difference is that `Packet*`
    // methods and interfaces are more broad, also covering `ip`.
    pc, err := net.ListenPacket("udp", address)
    if err != nil {
        return
    }

    // `Close`ing the packet "connection" means cleaning the data structures
    // allocated for holding information about the listening socket.
    defer pc.Close()

    doneChan := make(chan error, 1)
    buffer := make([]byte, maxBufferSize)

    // Given that waiting for packets to arrive is blocking by nature and we want
    // to be able of canceling such action if desired, we do that in a separate
    // go routine.
    go func() {
        for {
            // By reading from the connection into the buffer, we block until there's
            // new content in the socket that we're listening for new packets.
            //
            // Whenever new packets arrive, `buffer` gets filled and we can continue
            // the execution.
            //
            // note.: `buffer` is not being reset between runs.
            // It's expected that only `n` reads are read from it whenever
            // inspecting its contents.
            n, addr, err := pc.ReadFrom(buffer)
            if err != nil {
                doneChan <- err
                return
            }

            fmt.Printf("packet-received: bytes=%d from=%s\n",
                n, addr.String())

            // Setting a deadline for the `write` operation allows us to not block
            // for longer than a specific timeout.
            //
            // In the case of a write operation, that'd mean waiting for the send
            // queue to be freed enough so that we are able to proceed.
            deadline := time.Now().Add(*timeout)
            err = pc.SetWriteDeadline(deadline)
            if err != nil {
                doneChan <- err
                return
            }

            // Write the packet's contents back to the client.
            n, err = pc.WriteTo(buffer[:n], addr)
            if err != nil {
                doneChan <- err
                return
            }

            fmt.Printf("packet-written: bytes=%d to=%s\n", n, addr.String())
        }
    }()

    select {
    case <-ctx.Done():
        fmt.Println("cancelled")
        err = ctx.Err()
    case err = <-doneChan:
    }

    return
} 
```

Enter fullscreen mode Exit fullscreen mode

您可能已经注意到了，它与客户端并没有什么不同！原因在于，由于没有实际的连接(像 TCP 中那样)，客户端和服务器最终都要经过相同的路径:准备一个套接字进行读写，然后检查数据包的内容，并一遍又一遍地做同样的事情。

### 关闭思绪

进行这种探索是非常棒的，检查 go 源代码(顺便说一下，写得非常好)以及内核中的幕后发生了什么。

我想我终于有了一个很棒的工作流程，当涉及到用 [Delve](https://github.com/derekparker/delve) 调试和用 [bcc](https://github.com/iovisor/bcc) 验证内核函数时，也许我会很快写下来——让我知道这是否有趣！

如果你有任何问题，请告诉我！我是 Twitter 上的 [@cirowrc](https://twitter.com/cirowrc) ，我很乐意收到您的反馈。

祝你愉快！