# 了解 TCP 中的套接字和端口

> 原文：<https://dev.to/onmyway133/understanding-socket-and-port-in-tcp-58m>

[![](img/719b1d2926db860b523b83992974be1e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PZKWUQNl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AFBtXpAXB-9QMV8NK.jpg)

当深入研究 TCP 世界时，我得到了许多术语，我不知道任何误解。但是在 SO 上那些极客的帮助下，这些问题被揭开了神秘面纱。现在是总结和与他人分享:D 的时候了

## 什么定义了唯一连接？

有 5 个元素可以识别连接。他们称之为五元组

1.  协议。这经常被省略，因为我们知道我们在谈论 TCP，剩下 4。

2.  源 IP 地址。

3.  源端口。

4.  目标 P 地址。

5.  目标港口。

## TCP 是否在一个端口监听，在另一个端口通话？

不。这是一个常见的误解。TCP 在一个端口上侦听，并在同一个端口上通话。如果客户端与服务器建立多个 TCP 连接。客户端操作系统必须生成不同的随机源端口，以便服务器可以将它们视为唯一的连接

## 理论上，一台服务器可以处理的最大并发 TCP 连接数是多少？

一个监听端口可以同时接受多个连接。有一个经常被引用的“64K”限制，但这是针对每个客户端每个服务器端口的，需要澄清。

如果一个客户端与同一目标上的同一端口有多个连接，那么其中三个字段将是相同的，只有 source_port 有所不同，以区分不同的连接。端口是 16 位数字，因此任何给定的客户端到任何给定的主机端口的最大连接数是 64K。

但是，多个客户端可以各自拥有到某个服务器端口的高达 64K 的连接，如果服务器有多个端口或者其中一个是多宿的，那么您可以进一步增加这个数量

所以真正的限制是文件描述符。每个单独的套接字连接都有一个文件描述符，所以限制实际上是系统配置允许的文件描述符的数量和要处理的资源。最大限值通常超过 300K，但可通过 sysctl 等工具进行配置

## 并发连接请求 vs 并发打开连接

当客户端希望与服务器建立 TCP 连接时，该请求将在服务器的 backlog 队列中排队。这个积压队列很小(大约 5–10)，并且这个大小限制了并发连接请求的数量。然而，服务器很快从队列中挑选连接请求并接受它。被接受的连接请求被称为开放连接。并发打开连接的数量受到为文件描述符分配的服务器资源的限制。

## 为什么 TCP 握手成功后连接被拒绝？

很正常。当服务器收到来自客户端的连接请求(通过接收 SYN)时，它将使用 SYN，ACK 进行响应，从而导致成功的 TCP 握手。但是这个请求仍然在积压队列中。

但是，如果应用程序进程超过了它可以使用的最大文件描述符的限制，那么当服务器调用 accept 时，它会意识到没有可用于分配给套接字的文件描述符，并使 accept 调用和 TCP 连接失败，从而向另一端发送 FIN

## 什么是主动插座和被动插座？

插座有两种主要类型。主动套接字通过开放的数据连接连接到远程主动套接字。被动套接字不连接，而是等待传入连接，一旦建立连接，传入连接将产生新的主动套接字

每个端口可以绑定一个被动套接字，等待连接，也可以绑定多个主动套接字，每个套接字对应端口上的一个开放连接。这就好像工厂工人在等待新消息的到来(他表示被动套接字)，当一个消息从新的发送者到达时，他通过让其他人(主动套接字)主动读取数据包并在必要时向发送者做出响应，来与他们建立一个响应(连接)。这允许工厂工人自由接收新的包

## 何去何从

这里有更多的链接来帮助你进一步探索

*   [端口和插座有什么区别？](http://stackoverflow.com/questions/152457/what-is-the-difference-between-a-port-and-a-socket)

*   [无源和有源插座](http://stackoverflow.com/questions/4696812/passive-and-active-sockets)

*   [为什么 TCP 服务器在接受连接后会立即发送 FIN？](http://stackoverflow.com/questions/3870260/why-will-a-tcp-server-send-a-fin-immediately-after-accepting-a-connection)

*   [现代 Linux 机器理论上可以拥有的开放 TCP 连接的最大数量是多少](http://stackoverflow.com/questions/2332741/what-is-the-theoretical-maximum-number-of-open-tcp-connections-that-a-modern-lin)

*   [socket 监听 backlog 参数，如何确定这个值？](http://stackoverflow.com/questions/114874/socket-listen-backlog-parameter-how-to-determine-this-value)

*   [关于套接字监听和积压的问题](http://stackoverflow.com/questions/4253454/question-about-listening-and-backlog-for-sockets)

*   在 TCP 连接被接受之前拒绝它？

*   [生成百万 tcp 连接的最佳方式](http://stackoverflow.com/questions/6590181/best-way-to-generate-million-tcp-connection)

*   [浏览器中最大并行 http 连接数？](http://stackoverflow.com/questions/985431/max-parallel-http-connections-in-a-browser)

*   [如何保留一百万个同时 TCP 连接？](http://stackoverflow.com/questions/2831434/how-to-retain-one-million-simultaneous-tcp-connections)

*   [TCP RST 数据包详情](http://stackoverflow.com/questions/7735618/tcp-rst-packet-details)

*   一个连接中有多少个元组？

*   [TCP 是否使用另一个端口发送数据？](http://stackoverflow.com/questions/15761776/does-tcp-use-another-port-for-sending-data)

原帖[https://medium . com/fantageek/understanding-socket-and-port-in-TCP-2213 DC 2e 9 b 0 c](https://medium.com/fantageek/understanding-socket-and-port-in-tcp-2213dc2e9b0c)