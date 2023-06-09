# Traceroute 是如何工作的

> 原文：<https://dev.to/nicolasmesa/how-does-traceroute-work-8i9>

交叉发布自我的个人博客[https://blog.nicolasmesa.co](https://blog.nicolasmesa.co)。

在这篇文章中，我们来看看`traceroute`命令是如何工作的。`traceroute`是一个实用程序命令，它打印数据包到达另一台主机的路由(或跳数)。我们从一个`traceroute`的例子开始。然后我们回顾一下幕后发生的事情。最后，我们再次运行`traceroute`命令，同时用`tcpdump`嗅探流量。

### Traceroute 示例

让我们看一个例子:

```
nmesa@desktop-nicolas:~$ ping -c 1 google.com
PING google.com (172.217.14.206) 56(84) bytes of data.
64 bytes from sea30s01-in-f14.1e100.net. (172.217.14.206): icmp_seq=1 ttl=56 time=8.02 ms

--- google.com ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 8.028/8.028/8.028/0.000 ms nmesa@desktop-nicolas:~$ traceroute -q 1 google.com
traceroute to google.com (172.217.14.206), 64 hops max
  1 192.168.0.1 0.247ms
  2 67.218.102.107 11.667ms
  3 174.127.182.56 10.964ms
  4 174.127.141.190 12.403ms
  5 72.14.198.216 11.208ms
  6 74.125.243.177 13.321ms
  7 209.85.254.237 11.483ms
  8 172.217.14.206 12.060m 
```

首先，我们向`google.com`发出一个`ping`来找出它的 IP 地址(`172.217.14.206`)。然后，我们运行一个`traceroute`命令给`google.com`(`-q 1`是让`traceroute`只发送一个包而不是 3 个)。在这个例子中，我们看到数据包必须经过 8 跳才能到达`google.com`的主机。

### IP 报头

那么，这在引擎盖下是如何工作的？为了理解这一点，首先，我们需要谈谈互联网协议(IP)。IP 负责将数据包从源主机传送到目的主机。每个 IP 数据包都有一个 IP 报头，路由器用它来做出路由决定。 [IP 报头](https://en.wikipedia.org/wiki/IPv4#Header)包含大量信息，但我们将重点关注 3 个字段:

1.  **源 IP (src)** :发送数据包的主机的 IP 地址(在此讨论中我们将忽略 NAT)。在我们的例子中，是`192.168.0.250`。
2.  **目的 IP (dst)** :目标主机的 IP 地址。在我们的例子中，它是`google.com`的 IP 地址:`172.217.14.206`。
3.  **生存时间字段(TTL)** :顾名思义，该字段不包含时间。它具有数据包到达目的地之前应该经过的最大跳数。如果达到最大跳数，数据包将被丢弃，并向发送方发送一条错误消息。该字段有助于防止数据包永远处于循环中的路由循环。

当路由器收到 IP 数据包时，它会从 TTL 字段中减去 1，以确定是否应该转发该数据包。如果新的 TTL 值大于 0，路由器将使用更新后的 TTL 值转发数据包。如果新的 TTL 等于 0，路由器会丢弃数据包，并向源发送 ICMP 错误消息，让它知道数据包的*时间超过了传输中的*(`TTL`不足以让数据包到达目的地)。

### Traceroute 简介

`traceroute`利用`TTL`字段和返回的`ICMP`错误消息来跟踪到目标主机的路由。首先，它用`TTL = 1`发送一个包(如果在 Linux 中通常是 UDP)。当这个包到达我们的路由器时，路由器递减`TTL`，并注意到`TTL`等于`0`。我们的路由器丢弃数据包并发回一条`ICMP`错误消息。`traceroute`使用`ICMP`包中的*源 IP 地址*作为第一跳的值。`traceroute`用`TTL = 2`发送另一个数据报。我们的路由器收到数据包，递减`TTL`并转发数据包，因为`TTL`大于`0`。第二跳接收分组，递减`TTL`并注意到它等于`0`。路由器丢弃数据包并发回一条`ICMP`错误消息，由`traceroute`用来获取第二跳的值。`traceroute`将`TTL`递增 1，直到它实际到达服务器。服务器通常会响应一个 ICMP 错误，指出`UDP`端口不可达。至此，`traceroute`完成。

### 捕获跟踪路由流量

让我们用`tcpdump`来捕捉这次交流。首先，我们打开一个新的终端窗口并执行下面的`tcpdump`命令:

```
nmesa@desktop-nicolas:~$ sudo tcpdump -vv -nn -i eth0 "(udp and ip[8] < 10) or icmp"
tcpdump: listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes 
```

让我们回顾一下论点:

*   `-vv`:使输出冗长。我们需要这样才能看到数据包的`TTL`值。
*   `-nn`:我们不希望`tcpdump`将 IP 地址解析为主机名，或者将端口解析为服务名。
*   `-i eth0`:监听的界面。
*   `"(udp and ip[8] < 10) or icmp"`:过滤以显示`TTL`值小于 10 的 UDP 数据包(根据需要调整该值)或 ICMP 数据包(查看从路由器返回的错误)。
    *   `ip[8]`表示 [IP 头](https://en.wikipedia.org/wiki/IPv4#Header)的第 8 个字节(从 0 开始)。

在另一个终端窗口中，我们运行`traceroute`命令:

```
nmesa@desktop-nicolas:~$ traceroute -q 1 google.com
traceroute to google.com (172.217.14.206), 64 hops max
  1 192.168.0.1 0.247ms
  2 67.218.102.107 11.667ms
  3 174.127.182.56 10.964ms
  4 174.127.141.190 12.403ms
  5 72.14.198.216 11.208ms
  6 74.125.243.177 13.321ms
  7 209.85.254.237 11.483ms
  8 172.217.14.206 12.060m 
```

如果我们返回到`tcpdump`窗口，我们会看到如下内容:

```
nmesa@desktop-nicolas:~$ sudo tcpdump -vv -nn -i eth0 "(udp and ip[8] < 10) or icmp"
tcpdump: listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
21:48:11.140870 IP (tos 0x0, ttl 1, id 34332, offset 0, flags [DF], proto UDP (17), length 37)
 192.168.0.250.50475 > 172.217.14.206.33434: [bad udp cksum 0x7d6c -> 0x0386!] UDP, length 9
21:48:11.141090 IP (tos 0xc0, ttl 64, id 59363, offset 0, flags [none], proto ICMP (1), length 65)
 192.168.0.1 > 192.168.0.250: ICMP time exceeded in-transit, length 45
 IP (tos 0x0, ttl 1, id 34332, offset 0, flags [DF], proto UDP (17), length 37) 192.168.0.250.50475 > 172.217.14.206.33434: [udp sum ok] UDP, length 9
21:48:11.141236 IP (tos 0x0, ttl 2, id 34333, offset 0, flags [DF], proto UDP (17), length 37)
 192.168.0.250.50475 > 172.217.14.206.33435: [bad udp cksum 0x7d6c -> 0x0385!] UDP, length 9
21:48:11.152912 IP (tos 0x0, ttl 254, id 0, offset 0, flags [none], proto ICMP (1), length 56)
 67.218.102.107 > 192.168.0.250: ICMP time exceeded in-transit, length 36
 IP (tos 0x0, ttl 1, id 34333, offset 0, flags [DF], proto UDP (17), length 37) 192.168.0.250.50475 > 172.217.14.206.33435: UDP, length 9
21:48:11.152943 IP (tos 0x0, ttl 3, id 34335, offset 0, flags [DF], proto UDP (17), length 37)
 192.168.0.250.50475 > 172.217.14.206.33436: [bad udp cksum 0x7d6c -> 0x0384!] UDP, length 9
21:48:11.163898 IP (tos 0x0, ttl 253, id 26888, offset 0, flags [none], proto ICMP (1), length 96)
 174.127.182.56 > 192.168.0.250: ICMP time exceeded in-transit, length 76
 IP (tos 0x0, ttl 1, id 34335, offset 0, flags [DF], proto UDP (17), length 37) 192.168.0.250.50475 > 172.217.14.206.33436: [udp sum ok] UDP, length 9
21:48:11.163934 IP (tos 0x0, ttl 4, id 34336, offset 0, flags [DF], proto UDP (17), length 37)
 192.168.0.250.50475 > 172.217.14.206.33437: [bad udp cksum 0x7d6c -> 0x0383!] UDP, length 9
21:48:11.176324 IP (tos 0x0, ttl 252, id 59054, offset 0, flags [none], proto ICMP (1), length 96)
 174.127.141.190 > 192.168.0.250: ICMP time exceeded in-transit, length 76
 IP (tos 0x0, ttl 1, id 34336, offset 0, flags [DF], proto UDP (17), length 37) 192.168.0.250.50475 > 172.217.14.206.33437: [udp sum ok] UDP, length 9
21:48:11.176368 IP (tos 0x0, ttl 5, id 34339, offset 0, flags [DF], proto UDP (17), length 37)
 192.168.0.250.50475 > 172.217.14.206.33438: [bad udp cksum 0x7d6c -> 0x0382!] UDP, length 9
21:48:11.187557 IP (tos 0x0, ttl 251, id 0, offset 0, flags [none], proto ICMP (1), length 56)
 72.14.198.216 > 192.168.0.250: ICMP time exceeded in-transit, length 36
 IP (tos 0x0, ttl 1, id 34339, offset 0, flags [DF], proto UDP (17), length 37) 192.168.0.250.50475 > 172.217.14.206.33438: UDP, length 9
21:48:11.187622 IP (tos 0x0, ttl 6, id 34340, offset 0, flags [DF], proto UDP (17), length 37)
 192.168.0.250.50475 > 172.217.14.206.33439: [bad udp cksum 0x7d6c -> 0x0381!] UDP, length 9
21:48:11.200912 IP (tos 0x0, ttl 249, id 45260, offset 0, flags [none], proto ICMP (1), length 96)
 74.125.243.177 > 192.168.0.250: ICMP time exceeded in-transit, length 76
 IP (tos 0x80, ttl 1, id 34340, offset 0, flags [DF], proto UDP (17), length 37) 192.168.0.250.50475 > 172.217.14.206.33439: [udp sum ok] UDP, length 9
21:48:11.201012 IP (tos 0x0, ttl 7, id 34341, offset 0, flags [DF], proto UDP (17), length 37)
 192.168.0.250.50475 > 172.217.14.206.33440: [bad udp cksum 0x7d6c -> 0x0380!] UDP, length 9
21:48:11.212452 IP (tos 0x0, ttl 248, id 36434, offset 0, flags [none], proto ICMP (1), length 96)
 209.85.254.237 > 192.168.0.250: ICMP time exceeded in-transit, length 76
 IP (tos 0x80, ttl 1, id 34341, offset 0, flags [DF], proto UDP (17), length 37) 192.168.0.250.50475 > 172.217.14.206.33440: [udp sum ok] UDP, length 9
21:48:11.212596 IP (tos 0x0, ttl 8, id 34342, offset 0, flags [DF], proto UDP (17), length 37)
 192.168.0.250.50475 > 172.217.14.206.33441: [bad udp cksum 0x7d6c -> 0x037f!] UDP, length 9
21:48:11.224616 IP (tos 0x0, ttl 56, id 0, offset 0, flags [none], proto ICMP (1), length 56)
 172.217.14.206 > 192.168.0.250: ICMP 172.217.14.206 udp port 33441 unreachable, length 36
 IP (tos 0x80, ttl 1, id 34342, offset 0, flags [DF], proto UDP (17), length 37) 192.168.0.250.50475 > 172.217.14.206.33441: UDP, length 9 
```

### 跟踪路由流量深度挖掘

我们来分析一下。`traceroute`命令中的第 3 行显示第一跳是`192.168.0.1`(我的路由器)。请看下面一行:

```
21:48:11.140870 IP (tos 0x0, ttl 1, id 34332, offset 0, flags [DF], proto UDP (17), length 37)
    192.168.0.250.50475 > 172.217.14.206.33434: [bad udp cksum 0x7d6c -> 0x0386!] UDP, length 9
21:48:11.141090 IP (tos 0xc0, ttl 64, id 59363, offset 0, flags [none], proto ICMP (1), length 65)
    192.168.0.1 > 192.168.0.250: ICMP time exceeded in-transit, length 45
        IP (tos 0x0, ttl 1, id 34332, offset 0, flags [DF], proto UDP (17), length 37)
    192.168.0.250.50475 > 172.217.14.206.33434: [udp sum ok] UDP, length 9 
```

注意`ttl 1`值。我们还可以看到带着源 IP `192.168.0.1`返回的`time exceeded in-transit`消息。这相当于第一跳！

`traceroute`命令中的第二跳是`67.218.102.107`(第 4 行)。这也对应于以下行:

```
21:48:11.141236 IP (tos 0x0, ttl 2, id 34333, offset 0, flags [DF], proto UDP (17), length 37)
    192.168.0.250.50475 > 172.217.14.206.33435: [bad udp cksum 0x7d6c -> 0x0385!] UDP, length 9
21:48:11.152912 IP (tos 0x0, ttl 254, id 0, offset 0, flags [none], proto ICMP (1), length 56)
    67.218.102.107 > 192.168.0.250: ICMP time exceeded in-transit, length 36
        IP (tos 0x0, ttl 1, id 34333, offset 0, flags [DF], proto UDP (17), length 37)
    192.168.0.250.50475 > 172.217.14.206.33435: UDP, length 9 
```

这里我们看到了`ttl 2`值。注意，源 IP ( `192.168.0.250`)和目的 IP ( `172.217.14.206`)没有变(目标端口每条消息加 1，但这不影响我们的讨论)。ICMP 错误消息的源 IP `67.218.102.107`也与我们在 traceroute 命令中看到的一致！让我们一路走到底！

`traceroute`命令中的最后一跳是`172.217.14.206`，`google.com`的 IP 地址之一。让我们检查一下这一跳的`tcpdump`输出。

```
21:48:11.212596 IP (tos 0x0, ttl 8, id 34342, offset 0, flags [DF], proto UDP (17), length 37)
    192.168.0.250.50475 > 172.217.14.206.33441: [bad udp cksum 0x7d6c -> 0x037f!] UDP, length 9
21:48:11.224616 IP (tos 0x0, ttl 56, id 0, offset 0, flags [none], proto ICMP (1), length 56)
    172.217.14.206 > 192.168.0.250: ICMP 172.217.14.206 udp port 33441 unreachable, length 36
        IP (tos 0x80, ttl 1, id 34342, offset 0, flags [DF], proto UDP (17), length 37)
    192.168.0.250.50475 > 172.217.14.206.33441: UDP, length 9 
```

我们现在看到一个`ttl 8`值。注意，这里没有一个`time exceeded in-transit` ICMP 错误消息。相反，服务器用一条 ICMP 消息回应说`udp port 33441 unreachable`。此错误来自主机，因为它是唯一知道该端口是否打开的主机。这个消息告诉`traceroute`它发送的最后一个包到达了主机，并且在途中没有更多的跳。

## 结论

我们介绍了`traceroute`是如何工作的。我们看到了该命令如何使用 TTL 字段来控制数据包在被丢弃之前经过的跳数，并向我们发回一条错误消息。我们还看到 ICMP 错误消息的源 IP 地址也是丢弃该数据包的跳的 IP 地址。一些路由器被配置为无声地丢弃数据包，并且不发送 ICMP 错误消息(这在`traceroute`输出中显示为`*`条目)。

我们没有谈到的一件事是 IP 报头校验和。校验和用于验证 IP 报头(而不是 IP 正文)的内容。该校验和在每一跳都被验证，并且在递减`TTL`值后需要重新计算。

## 链接

*   [原帖](https://blog.nicolasmesa.co/posts/2018/09/how-does-traceroute-work/)
*   [Traceroute 手册页](https://linux.die.net/man/8/traceroute)
*   [互联网协议(IP)](https://en.wikipedia.org/wiki/Internet_Protocol)
*   [IP 报头](https://en.wikipedia.org/wiki/IPv4#Header)
*   [生存时间(TTL)](https://en.wikipedia.org/wiki/Time_to_live)