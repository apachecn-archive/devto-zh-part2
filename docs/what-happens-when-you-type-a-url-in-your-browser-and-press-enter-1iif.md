# 当您在浏览器中键入 Url 并按 Enter 键时会发生什么

> 原文：<https://dev.to/nicolasmesa/what-happens-when-you-type-a-url-in-your-browser-and-press-enter-1iif>

交叉发布自我的个人博客[https://blog.nicolasmesa.co](https://blog.nicolasmesa.co)。

**免责声明:**您好！这是我的第一篇博文！我非常高兴能与世界分享它，但我也应该提醒你，它有点长而且重复。希望随着我博客写得越来越多，我会成为一名更好的作家。希望你喜欢:)

## 简介

职业生涯中多次听到这个问题，想试着回答一下。我将深入探讨问题的网络部分，因为这是我觉得最有趣的部分。

网络中有很多事情发生，从浏览器向服务器发出 HTTP 请求，然后从服务器向浏览器返回 HTTP 响应。为了解释发生了什么，我将对网络设置和网络状态做一些假设。

## 假设

### 网络状态

*   我们的计算机通过以太网与网络相连。
*   我们的计算机已经有了一个 IP 地址(稍后会详细介绍 IP 地址是什么)。
*   我们的计算机位于 NAT 之后(这是许多家庭网络的情况)。
*   我们的 ARP 缓存是干净的。
*   我们的 DNS 缓存是干净的。
*   我们路由器的 ARP 缓存包含一个默认网关条目。
*   我们的网络是`192.168.0.0/24`。
*   我们将要使用的 URL 是`http://www.example.com`。
*   DNS 服务器的缓存中有一个针对`www.example.com`的条目。
*   我们将忽略 TCP 序列号。

### 配置

#### 我们的电脑(发出 HTTP 请求的电脑)

*   IP 地址:`192.168.0.10`。
*   默认网关:`192.168.0.1`。
*   DNS 服务器:`8.8.8.8`。
*   MAC 地址:`AA:AA:AA:AA:AA:AA`。
*   TCP 源端口:`9999`。
*   DNS 源端口:`3333`。

#### 我们的路由器/ NAT

*   内部 IP 地址:`192.168.0.1`。
*   外部 IP 地址:`51.0.0.20`。
*   外部 TCP 源端口:`15000`。
*   外部 DNS 源端口:`10000`。
*   外网掩码:`255.255.255.0`(或`/24`)。
*   内部 MAC 地址:`BB:BB:BB:BB:BB:BB`。
*   外部 MAC 地址:`CC:CC:CC:CC:CC:CC`。
*   默认网关:`51.0.0.1`。
*   默认网关的 MAC 地址:`DD:DD:DD:DD:DD:DD`。

#### 【www.example.com】T2 服务器

*   `www.example.com`的 IP 地址是`93.184.216.34`。

## 循序渐进

为了开始这个过程，我们在网络浏览器中键入 URL `http://www.example.com`,然后按回车键。这将指示我们的浏览器创建一个 HTTP 请求发送给`www.example.com`。HTTP 请求看起来像这样:

```
GET / HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8 
```

对`www.example.com`的 HTTP 请求。

我们的计算机现在将尝试连接到 TCP 端口`80`(默认 HTTP 端口)中的`www.example.com`。首先，它需要创建一个 TCP `SYN`段来启动 TCP 连接。`SYN`段包含建立连接的信息，包括随机源端口(`9999`来自我们的假设)、目的端口(`80`和标志(`SYN`)。

然后，这个 TCP 数据段被封装在一个包含源 IP ( `192.168.0.10`)和目的 IP 的 IP 数据包中。对于目的地 IP，我们的计算机进入它的 DNS 缓存，寻找包含`www.example.com`的条目。因为它的缓存是空的，所以 IP 包的创建需要等到我们得到`www.example.com`的 IP 地址。到目前为止，我们不完整的 IP 数据包看起来像这样:

```
IP[
    SourceIp=192.168.0.10,
    DestIp=????,
    TCP[
        SourcePort=9999,
        DestPort=80,
        Flags=[SYN]
    ]
] 
```

不完整的 SYN IP 数据包(注意`DestIP`字段中的`????`)。

为了获得`www.example.com`的 IP 地址，我们需要使用一个名为 DNS 的服务。DNS 代表域名系统，是负责将人类可读的名称(如`www.example.com`)翻译成 IP 地址的系统。我们的计算机创建了一个 DNS 请求，其中包括请求类型(`A`)和要解析的域名(`www.example.com`)。这个 DNS 请求然后被封装在一个 UDP 数据报中，该数据报的报头包含一个随机的源端口(`3333`来自我们的假设)和目的端口(`53`是 DNS 众所周知的端口)。然后，该数据报被放入一个 IP 数据包中，该数据包的报头包含源 IP 地址(`192.168.0.10`)和目的 IP 地址(`8.8.8.8`)。请注意，DNS 服务器是预先在我们的计算机中配置的，因此我们已经知道它的 IP 地址。

我们现在想发送这个 IP 数据包，但是我们的计算机不知道要发送到哪里。计算机查看其路由表，查看哪条路由与`8.8.8.8` IP 地址匹配。路由表看起来像这样:

```
Destination Netmask Gateway Interface
0.0.0.0 0.0.0.0 192.168.0.1 eth0
192.168.0.0 255.255.255.0 0.0.0.0 eth0 
```

我们计算机的路由表。

我们的计算机使用[最长前缀匹配](https://en.wikipedia.org/wiki/Longest_prefix_match)来选择将数据包发送给谁。在这种情况下，它选择第一条路由作为我们的默认网关(`192.168.0.1`)。这意味着 IP 数据包将被发送到我们的路由器，路由器将负责将其转发到下一跳(希望更靠近 DNS 服务器)。路径中的路由器将根据它们的路由表继续转发数据包，直到它到达 DNS 服务器(`8.8.8.8`)。每当我们的计算机需要发送一个 IP 数据包时，这个过程就会发生，在我们的例子中，它总是选择将数据包发送到我们的默认网关(`192.168.0.1`)。

既然 IP 数据包已经准备好，它就被封装在以太网帧中。以太网报头包含源 MAC 地址(`AA:AA:AA:AA:AA:AA`)和目的 MAC 地址(在本例中是路由器的 MAC 地址)。为了获得路由器的 MAC 地址，我们的计算机在它的 ARP 表中查找是否有条目将路由器的 IP 地址(`192.168.0.1`)转换成路由器的 MAC 地址。由于 ARP 缓存是空的，我们需要在发送 IP 数据包之前找出 MAC 地址。这就是我们不完整的以太网帧的样子:

```
Ethernet[
    SourceMac=AA:AA:AA:AA:AA:AA,
    DestMac=????,
    IP[
        SourceIp=192.168.0.10,
        DestIp=8.8.8.8,
        UDP[
            SourcePort=3333,
            DestPort=53,
            DNS[
                Query=A,
                Domain=www.example.com
            ]
        ]
    ]
] 
```

DNS 查询的以太网帧不完整(注意`DestMac`字段中的`????`)。

要将 IP 地址转换为 MAC 地址，需要使用 ARP 协议。ARP 代表地址解析协议。ARP 数据包包含一个问题，询问`who-has 192.168.0.1 tell 192.168.0.10`以及源 MAC 地址(`AA:AA:AA:AA:AA:AA`)、目的 MAC 地址(`FF:FF:FF:FF:FF:FF`，这是广播地址)、源 IP 地址(`192.168.0.10`)和目的 IP 地址(`192.168.0.1`，这是我们要询问的 IP 地址。然后，ARP 数据包被封装在包含源 MAC 地址的以太网帧中(`AA:AA:AA:AA:AA:AA`)，对于目的 MAC 地址，使用广播地址(`FF:FF:FF:FF:FF:FF`)。该帧通过我们的专用网络发送，看起来像这样:

```
Ethernet[
    SourceMac=AA:AA:AA:AA:AA:AA,
    DestMac=FF:FF:FF:FF:FF:FF,
    ARP[
        Type=Request,
        SourceIp=192.168.0.10,
        DestIp=192.168.0.1,
        SourceMac=AA:AA:AA:AA:AA:AA,
        DestMac=FF:FF:FF:FF:FF:FF
    ]
] 
```

ARP 包来找出我们路由器的 MAC 地址。

这一点有值得注意的地方。这是我们的计算机与网络的第一次互动！

由于使用了广播 MAC 地址(`FF:FF:FF:FF:FF:FF`)，该帧将到达我们专用网络上的每个节点。每个节点(除了我们的路由器)将最终丢弃数据包，因为目的 IP 地址(`192.168.0.1`)不是它们的 IP。

当我们的路由器收到这个数据包时，它会看到`192.168.0.10`正在请求它的 MAC 地址。它还可以在 ARP 数据包中看到我们计算机的源 MAC 地址。路由器将此信息保存在其 ARP 表中(`192.168.0.10 -> AA:AA:AA:AA:AA:AA`)，并创建一个 ARP 回复，称之为`192.168.0.1 is-at BB:BB:BB:BB:BB:BB`。然后，这个 ARP 回复被封装在一个以太网帧中，其中包含源 MAC 地址(`BB:BB:BB:BB:BB:BB`)和目的 MAC 地址(`AA:AA:AA:AA:AA:AA`)。以太网帧现在通过网络发送。这里需要注意的一点是，目的 MAC 地址不再是广播 MAC 地址，因此只有我们的计算机会处理它。从现在开始，我们的路由器将能够通过参考其 ARP 缓存，将我们计算机的 IP 地址(`192.168.0.10`)转换为其 MAC 地址(`AA:AA:AA:AA:AA:AA`)。包含此回复的以太网帧看起来像这样:

```
Ethernet[
    SourceMac=BB:BB:BB:BB:BB:BB,
    DestMac=AA:AA:AA:AA:AA:AA,
    ARP[
        Type=Reply,
        SourceIp=192.168.0.1,
        DestIp=192.168.0.10,
        SourceMac=BB:BB:BB:BB:BB:BB,
        DestMac=AA:AA:AA:AA:AA:AA
    ]
] 
```

ARP 回复，我们的路由器告诉我们的计算机它的 MAC 地址。

我们的计算机接收以太网帧，看到 ARP 回复，并将路由器的 MAC 地址保存在其 ARP 表中(`192.168.0.1 -> BB:BB:BB:BB:BB:BB`)。从现在开始，任何时候我们的计算机需要把我们路由器的 IP 地址(`192.168.0.1`)转换成它的 MAC 地址(`BB:BB:BB:BB:BB:BB`)，它只需要参考它的 ARP 缓存。我们在进步！我们现在有了路由器的 MAC 地址，我们需要通过 DNS 查询发送 IP 数据包以获得`www.example.com`的 IP 地址，我们需要发送 TCP `SYN`数据包，通过端口`80`建立 TCP 连接，我们需要通过端口`80`发送 HTTP 请求。

由于我们的计算机现在知道了路由器的 MAC 地址，它会填充包含我们的 DNS 查询的以太网帧的空白，并通过网络将其发送到(`BB:BB:BB:BB:BB:BB`)。每个需要发送到路由器的以太网帧都会发生这种情况。画面大概是这样的:

```
Ethernet[
    SourceMac=AA:AA:AA:AA:AA:AA,
    DestMac=BB:BB:BB:BB:BB:BB,
    IP[
        SourceIp=192.168.0.10,
        DestIp=8.8.8.8,
        UDP[
            SourcePort=3333,
            DestPort=53,
            DNS[
                Query=A,
                Domain=www.example.com
            ]
        ]
    ]
] 
```

DNS 查询的完整以太网帧。

需要注意的一点是`DestIp`字段不是路由器的 IP 地址；还是`8.8.8.8`。

路由器接收该帧，并查看 IP 报头中的目的 IP ( `8.8.8.8`)。它还使用[最长前缀匹配](https://en.wikipedia.org/wiki/Longest_prefix_match)来选择将该数据包转发到哪里，并最终选择其默认网关(`51.0.0.1`)。每当路由器需要将数据包转发到公共互联网中的一个 IP 地址，如`8.8.8.8`(以及后来的`93.184.216.34`)时，就会做出这个决定。

根据我们的假设，我们的路由器也是一个 NAT。这意味着我们的路由器需要在转发数据包之前对其进行转换。NAT 代表网络地址转换。它被广泛部署为让多个设备共享一个公共 IP 地址。我们的内部(或私有)网络使用私有 IP 地址(`192.168.0.1`和`192.168.0.10`)，但是公共互联网使用公共 IP 地址。当我们需要向公共互联网发送数据包时，NAT 会负责将私有 IP 地址替换为公共 IP 地址(`51.0.0.20`)。NAT 使用 5 个值来标识连接:协议(`UDP` / `TCP`)、源 IP、内部源端口、目的 IP 和外部源端口。随机选择外部源端口，以确保连接标识符是唯一的。基于我们的假设，路由器决定使用端口`10000`。NAT 表中的条目如下所示:

```
UDP 192.168.0.10 3333 -> 8.8.8.8 10000 
```

NAT 表。注意，目的端口(`53`)不用于识别连接。

我们的路由器使用 NAT 表中的信息将数据包的源 IP 地址(`192.168.0.10`)替换为公共 IP 地址(`51.0.0.20`)，将内部源端口(`3333`)替换为外部源端口(`10000`)。这使得该 IP 分组在公共互联网中可路由。我们的路由器为任何需要发送到公共互联网的数据包执行这种转换，包括 TCP/IP 数据包。我们将这个过程称为**内部到外部的翻译**。

我们的路由器将数据包封装在一个新的以太网帧中，其源 MAC 地址为`CC:CC:CC:CC:CC:CC`，目的 MAC 地址为`DD:DD:DD:DD:DD:DD`(我们假设该值已经被缓存，因此不需要 ARP)。我们的路由器转发到公共互联网的每个数据包都经过相同的过程，因此我们不会再提起这个问题。以太网帧最终看起来像这样:

```
Ethernet[
    SourceMac=CC:CC:CC:CC:CC:CC,
    DestMac=DD:DD:DD:DD:DD:DD,
    IP[
        SourceIp=51.0.0.20,
        DestIp=8.8.8.8,
        UDP[
            SourcePort=10000,
            DestPort=53,
            DNS[
                Query=A,
                Domain=www.example.com
            ]
        ]
    ]
] 
```

带有 DNS 查询的公共可路由数据包(注意`SourceIp`和`SourcePort`中的变化)。

以太网帧被发送到默认网关，默认网关很可能是我们的 ISP 拥有的路由器，由 ISP 将我们的数据包发送到`8.8.8.8`。

一旦我们的数据包到达`8.8.8.8`(谷歌的 DNS 服务器)，服务器就会查看查询(`A`为`www.example.com`)并搜索其缓存。我们假设缓存条目存在，所以它知道`www.example.com`映射到`93.184.216.34`。DNS 服务器创建一个新的包含此信息的 DNS 响应消息作为对查询的回答。响应被封装在具有源端口`53`和目的端口`10000`的 UDP 数据报中。然后，UDP 数据报被封装在一个 IP 包中，其中包含源 IP `8.8.8.8`和目的 IP `51.0.0.20`(我们的公共 IP)。数据包最终被封装在以太网帧中(我们将忽略这一部分)，并通过网络发送出去。IP 包最终看起来像这样:

```
IP[
    SourceIp=8.8.8.8,
    DestIp=51.0.0.20,
    UDP[
        SourcePort=53,
        DestPort=10000,
        DNS[
            Answer=93.184.216.34,
            Domain=www.example.com
        ]
    ]
] 
```

包含 DNS 回复的 IP 数据包。

数据包穿过网络，最终到达我们的路由器。我们的路由器需要在将数据包转发到我们的专用网络之前对其进行转换。路由器查看协议(`UDP`)、源 IP ( `8.8.8.8`)和目的端口(`10000`)，以在 NAT 表中找到具有这些值的条目。根据表中的值，路由器将目的 IP 转换为我们计算机的 IP ( `192.168.0.10`)并将目的端口转换为我们计算机的源端口(`3333`)。这使得 IP 数据包在我们的专用网络中可路由。对于来自公共互联网的任何信息包，包括 TCP/IP 信息包(只有其属性可在 NAT 表中找到的信息包被转换，其他信息包被丢弃)，都会发生相同的过程。我们将这个过程称为**外部到内部的转换**。此后，路由器将数据包封装在带有源 MAC `BB:BB:BB:BB:BB:BB`和目的 MAC `AA:AA:AA:AA:AA:AA`的以太网帧中，并通过专用网络发送(每次路由器将数据包转发到我们的计算机时都会发生这种情况，因此我们不会再提起它)。带有转换后的 IP 数据包的以太网帧看起来像这样:

```
Ethernet[
    SourceMac=BB:BB:BB:BB:BB:BB,
    DestMac=AA:AA:AA:AA:AA:AA,
    IP[
        SourceIp=8.8.8.8,
        DestIp=192.168.0.10,
        UDP[
            SourcePort=53,
            DestPort=3333,
            DNS[
                Answer=93.184.216.34,
                Domain=www.example.com
            ]
        ]
    ]
] 
```

包含 DNS 回复的私有可路由数据包(注意`DestIp`和`DestPort`中的变化)。

有了`www.example.com`的 IP 地址，我们的计算机在我们不完整的`SYN`包中填入目的 IP。IP 报头被封装在以太网帧中，并通过专用网络发送。以太网帧看起来像这样:

```
Ethernet[
    SourceMac=AA:AA:AA:AA:AA:AA,
    DestMac=BB:BB:BB:BB:BB:BB,
    IP[
        SourceIp=192.168.0.10,
        DestIp=93.184.216.34,
        TCP[
            SourcePort=9999,
            DestPort=80,
            Flags=[SYN]
        ]
    ]
] 
```

包含 TCP SYN 数据包的私有可路由数据包。

我们的路由器接收数据包并提取协议(`TCP`)、源 IP ( `192.168.0.10`)、源端口(`9999`)、目的地 IP ( `93.184.216.34`)，并生成一个随机数用作外部源端口(【来自假设】)。这些值标识 TCP 连接，并保存在路由器的 NAT 表中。我们的路由器可以参考 NAT 表来识别需要转换的数据包以及转换时使用的值。NAT 表中的条目如下所示:

```
TCP 192.168.0.10 9999 -> 93.184.216.34 15000 
```

我们的路由器使用我们的公共 IP 地址(`51.0.0.20`)替换源 IP，使用上一步生成的端口(`15000`)替换源端口。然后，它将转换后的 IP 数据包封装在以太网帧中，并通过网络发送出去。以太网帧看起来像这样:

```
Ethernet[
    SourceMac=CC:CC:CC:CC:CC:CC,
    DestMac=DD:DD:DD:DD:DD:DD,
    IP[
        SourceIp=51.0.0.20,
        DestIp=93.184.216.34,
        TCP[
            SourcePort=15000,
            DestPort=80,
            Flags=[SYN]
        ]
    ]
] 
```

可公开路由的 SYN 段(注意对`SourceIp`和`SourcePort`的更改)。

当数据包到达`93.184.216.34`(`www.example.com`的 IP 地址)时，服务器会查看 TCP 报头的目的端口(`80`)，并检查是否有应用程序正在侦听该端口。因为在那个端口上运行着一个 HTTP 服务器，所以服务器需要用一个`SYN` / `ACK`来回复。服务器创建一个`TCP`段，并将其源端口设置为`80`，目的端口设置为`15000`(刚收到的`SYN`数据包的源端口)，标志设置为`SYN` / `ACK`。然后，它将该数据段封装在一个 IP 数据包中，源 IP 为`93.184.216.34`，目的 IP 为`51.0.0.20`。最后，它将 IP 数据包封装在以太网帧中(我们将忽略这一部分)。然后数据包被发送到网络，看起来像这样:

```
IP[
    SourceIp=93.184.216.34,
    DestIp=51.0.0.20,
    TCP[
        SourcePort=80,
        DestPort=15000,
        Flags=[SYN, ACK]
    ]
] 
```

包含 TCP SYN / ACK 的 IP 数据包。

当数据包到达我们的路由器时，它会执行外部到内部的转换。转换后的数据包封装在以太网帧中，通过专用网络发送。以太网帧看起来像这样:

```
Ethernet[
    SourceMac=BB:BB:BB:BB:BB:BB,
    DestMac=AA:AA:AA:AA:AA:AA,
    IP[
        SourceIp=93.184.216.34,
        DestIp=192.168.0.10,
        TCP[
            SourcePort=80,
            DestPort=9999,
            Flags=[SYN, ACK]
        ]
    ]
] 
```

包含 TCP SYN / ACK 的私有可路由数据包。

我们的计算机查看目的端口(`9999`)和 TCP 标志(`SYN` / `ACK`)，并检查这个连接的状态。建立这个连接所缺少的唯一东西是向服务器发送一个`ACK`(`93.184.216.34`)。我们的计算机创建一个新的 TCP 段，将源端口`9999`、目的端口`80`和`ACK`作为其唯一的标志。然后，该数据段被封装在一个 IP 数据包中，其中包含源 IP `192.168.0.10`和目的 IP `93.184.216.34`。IP 数据包被封装在以太网帧中，并通过专用网络发送。以太网帧看起来像这样:

```
Ethernet[
    SourceMac=AA:AA:AA:AA:AA:AA,
    DestMac=BB:BB:BB:BB:BB:BB,
    IP[
        SourceIp=192.168.0.10,
        DestIp=93.184.216.34,
        TCP[
            SourcePort=9999,
            DestPort=80,
            Flags=[ACK]
        ]
    ]
] 
```

包含 ACK 的私有可路由数据包，用于完成三次握手。

我们的路由器接收此帧，并使用 NAT 表中的信息来执行内部到外部的转换。然后，它将转换后的 IP 数据包封装在以太网帧中，并通过公共网络发送出去。以太网帧看起来像这样:

```
Ethernet[
    SourceMac=CC:CC:CC:CC:CC:CC,
    DestMac=DD:DD:DD:DD:DD:DD,
    IP[
        SourceIp=50.0.0.20,
        DestIp=93.184.216.34,
        TCP[
            SourcePort=15000,
            DestPort=80,
            Flags=[ACK]
        ]
    ]
] 
```

包含 ACK 的公共可路由数据包，用于完成三次握手。

数据包在互联网上传输，直到到达`93.184.216.34`。服务器使用源端口(`15000`)和源 IP ( `50.0.0.20`)检查连接状态，并将连接标记为已建立。注意，服务器不会为它刚刚收到的`ACK`返回一个`ACK`。

我们的计算机现在准备发送实际的 HTTP 请求。它将 HTTP 请求封装在一个带有源端口`9999`、目的端口`80`的`TCP`段中，并将标志设置为`ACK` / `PUSH`。然后，这个 TCP 数据段被封装在一个带有源 IP `192.168.0.10`和目的 IP `93.184.216.34`的 IP 数据包中。然后，它将 IP 数据包封装在以太网帧中，并通过专用网络发送出去。画面大概是这样的:

```
Ethernet[
    SourceMac=AA:AA:AA:AA:AA:AA,
    DestMac=BB:BB:BB:BB:BB:BB,
    IP[
        SourceIp=192.168.0.10,
        DestIp=93.184.216.34,
        TCP[
            SourcePort=9999,
            DestPort=80,
            Flags=[ACK, PUSH],
            HTTP[
                GET / HTTP/1.1
                Host: www.example.com
                ...
            ]
        ]
    ]
] 
```

包含 HTTP 请求的私有可路由数据包。

我们的路由器接收帧，并对 IP 数据包进行内部到外部的转换。然后，它将转换后的数据包封装在以太网帧中，并通过公共网络发送出去。以太网帧看起来像这样:

```
Ethernet[
    SourceMac=CC:CC:CC:CC:CC:CC,
    DestMac=DD:DD:DD:DD:DD:DD,
    IP[
        SourceIp=50.0.0.20,
        DestIp=93.184.216.34,
        TCP[
            SourcePort=15000,
            DestPort=80,
            Flags=[ACK, PUSH],
            HTTP[
                GET / HTTP/1.1
                Host: www.example.com
                ...
            ]
        ]
    ]
] 
```

包含 HTTP 请求的公共可路由数据包。

数据包到达`93.184.216.3` ( `www.example.com`)，服务器使用源 IP ( `51.0.0.20`)和源端口(`15000`)验证连接存在并已建立。TCP 数据段中的`PUSH`标志指示服务器将 TCP 数据段的主体(HTTP 请求)发送给监听端口`80`的应用程序(HTTP 服务器)。HTTP 服务器处理 HTTP 请求并生成 HTTP 响应。来自`www.example.com`的 HTTP 响应看起来像这样:

```
HTTP/1.1 200 OK
Accept-Ranges: bytes
Cache-Control: max-age=604800
Content-Type: text/html
Date: Sat, 11 Aug 2018 20:45:46 GMT
Etag: "1541025663"
Expires: Sat, 18 Aug 2018 20:45:46 GMT
Last-Modified: Fri, 09 Aug 2013 23:54:35 GMT
Server: ECS (oxr/830D)
Vary: Accept-Encoding
X-Cache: HIT
Content-Length: 606

<!doctype html>
<html>
...
</html> 
```

来自`www.example.com`的示例 HTTP 响应(几乎截取了整个正文)。

**注意:**服务器应该已经发送了 ACK 来确认这个 TCP 数据段被接收。但是，这个 ACK 也可以和 HTTP 响应一起发送，所以为了避免重复，我们假设是这样的。

HTTP 响应被封装在一个 TCP 数据段中，该数据段包含源端口`80`、目的端口`15000`和标志`ACK` / `PUSH`。然后，TCP 数据段被封装在一个 IP 数据包中，该数据包具有源 IP `93.184.216.34`和目的 IP `50.0.0.20`。然后，数据包被封装在以太网帧中(我们也将忽略这一点)，并通过公共网络发送出去。IP 包看起来像这样:

```
IP[
    SourceIp=93.184.216.34,
    DestIp=50.0.0.20,
    TCP[
        SourcePort=80,
        DestPort=15000,
        Flags=[ACK, PUSH],
        HTTP[
            HTTP/1.1 200 OK
            Accept-Ranges: bytes
            ...
            <!doctype html>
            ...
        ]
    ]
] 
```

包含 HTTP 响应的 IP 数据包。

我们的路由器接收数据包并执行外部到内部的转换。之后，IP 数据包被包裹在以太网帧中，并通过专用网络发送。以太网帧看起来像这样:

```
Ethernet[
    SourceMac=BB:BB:BB:BB:BB:BB,
    DestMac=AA:AA:AA:AA:AA:AA,
    IP[
        SourceIp=93.184.216.34,
        DestIp=192.168.0.10,
        TCP[
            SourcePort=80,
            DestPort=9999,
            Flags=[ACK, PUSH],
            HTTP[
                HTTP/1.1 200 OK
                Accept-Ranges: bytes
                ...
                <!doctype html>
                ...
            ]
        ]
    ]
] 
```

带有 HTTP 响应的私有可路由数据包。

我们的计算机接收数据包并查找连接信息。`PUSH`标志指示我们的计算机向打开连接的应用程序(我们的 web 浏览器)发送 TCP 主体(HTTP 响应)。我们的 web 浏览器现在有了 HTTP 响应，可以用它来呈现 web 页面。然而，从网络的角度来看，我们还没有完成。服务器不知道我们收到了 TCP 数据段，因为我们还没有确认它(发回一个 ACK)。由于浏览器不打算向`www.example.com`发送更多的请求，它也会关闭连接。通过在同一个 TCP 段中发送`ACK`和`FIN`标志，这两个动作可以合并成一个动作(为了避免更多的重复，我们假设发生了这种情况)。

我们的计算机创建了一个新的 TCP 段，将源端口`9999`、目的端口`80`和`FIN` / `ACK`作为其标志。它将该数据段放入一个 IP 数据包中，该数据包的源 IP 为`192.168.0.10`，目的 IP 为`93.184.216.34`。IP 数据包用以太网帧包装，通过网络发送。以太网帧看起来像这样:

```
Ethernet[
    SourceMac=AA:AA:AA:AA:AA:AA,
    DestMac=BB:BB:BB:BB:BB:BB,
    IP[
        SourceIp=192.168.0.10,
        DestIp=93.184.216.34,
        TCP[
            SourcePort=9999,
            DestPort=80,
            Flags=[ACK, FIN]
        ]
    ]
] 
```

包含 HTTP 响应 ACK 和关闭 TCP 连接 FIN 的私有可路由数据包。

路由器获取此帧并执行内部到外部的转换。然后，它将 IP 数据包封装在以太网帧中，并将该帧发送到公共网络。画面大概是这样的:

```
Ethernet[
    SourceMac=CC:CC:CC:CC:CC:CC,
    DestMac=DD:DD:DD:DD:DD:DD,
    IP[
        SourceIp=50.0.0.20,
        DestIp=93.184.216.34,
        TCP[
            SourcePort=15000,
            DestPort=80,
            Flags=[ACK, FIN]
        ]
    ]
] 
```

带有 HTTP 响应 ACK 和关闭 TCP 连接 FIN 的公共可路由数据包。

当服务器收到这个数据包时，它现在知道 HTTP 响应已经收到。从标志中，它还可以知道我们的计算机想要关闭连接。服务器创建一个新的 TCP 报文段，其源端口为`80`，目的端口为`15000`，标志为`FIN` / `ACK`。它将这个 TCP 数据段封装在一个 IP 数据包中，该数据包带有源 IP `93.184.216.34`和目的 IP `50.0.0.20`。IP 数据包通过网络发送。IP 包看起来像这样:

```
IP[
    SourceIp=93.184.216.34,
    DestIp=50.0.0.20,
    TCP[
        SourcePort=80,
        DestPort=15000,
        Flags=[ACK, FIN]
    ]
] 
```

包含关闭连接的 FIN 的 IP 数据包。

当我们的路由器收到数据包时，它会执行外部到内部的转换，然后将数据包封装在以太网帧中。然后，它通过我们的专用网络发送帧。画面大概是这样的:

```
Ethernet[
    SourceMac=BB:BB:BB:BB:BB:BB,
    DestMac=AA:AA:AA:AA:AA:AA,
    IP[
        SourceIp=93.184.216.34,
        DestIp=192.168.0.10,
        TCP[
            SourcePort=80,
            DestPort=9999,
            Flags=[ACK, FIN]
        ]
    ]
] 
```

包含用于关闭连接的 FIN 的私有可路由数据包。

我们的计算机收到这个包，并在标志中找到一个鳍。现在连接关闭了，剩下的就是我们的计算机向服务器发送一个确认信息，表明它收到了`FIN` / `ACK`数据包。它创建了一个带有源端口`9999`和目的端口`80`的 TCP 段。该数据段被封装在一个 IP 数据包中，其源 IP 为`192.168.0.10`，目的 IP 为`93.184.216.34`。IP 数据包被封装在以太网帧中，并通过专用网络发送。画面大概是这样的:

```
Ethernet[
    SourceMac=AA:AA:AA:AA:AA:AA,
    DestMac=BB:BB:BB:BB:BB:BB,
    IP[
        SourceIp=192.168.0.10,
        DestIp=93.184.216.34,
        TCP[
            SourcePort=9999,
            DestPort=80,
            Flags=[ACK]
        ]
    ]
] 
```

包含确认连接已关闭的最后一个 ACK 的私有可路由数据包。

路由器接收此帧并执行内部到外部的转换。IP 数据包被封装在通过公共网络发送的以太网帧中。以太网帧看起来像这样:

```
Ethernet[
    SourceMac=CC:CC:CC:CC:CC:CC,
    DestMac=DD:DD:DD:DD:DD:DD,
    IP[
        SourceIp=50.0.0.20,
        DestIp=93.184.216.34,
        TCP[
            SourcePort=15000,
            DestPort=80,
            Flags=[ACK]
        ]
    ]
] 
```

可公开路由的数据包，转换后的数据包带有最后一个 ACK。

当服务器收到这个数据包时，它会查看标志并确认我们的计算机收到了 FIN TCP 段。

## 结论

哇，那是一个很长的帖子！我想我们都同意，从您在浏览器中键入 URL 并按 enter 键开始，直到您看到您的网页，网络层发生了很多事情。不过，有几件重要的事情需要提醒一下:

1.  这只是一个 HTTP 请求！通常，一个包含外部脚本、图像、样式表等的页面会发出数十甚至数百个请求！
2.  只需一个请求，我们就能看到缓存的价值。我们只需要询问一次路由器的 MAC 地址，然后就可以从缓存中获取该 MAC 地址。如果我们发出更多的请求，DNS 缓存也会发挥作用，因为我们会缓存`www.example.com`的 IP 地址，而不必向 DNS 服务器请求。
3.  缓存在 HTTP 中也扮演着重要的角色。如果我们的浏览器之前缓存了这个页面，我们就不必进行任何网络调用了！
4.  建立 TCP 连接时会有一些开销。HTTP 客户端试图保持连接活动(使用`Connection: Keep-Alive` HTTP 头),以避免这种开销并重用同一个连接。这个头的一个问题是 HTTP 客户端一次只能发出一个请求(每个 TCP 连接)。 [HTTP/2](https://en.wikipedia.org/wiki/HTTP/2) 和 [QUIC](https://en.wikipedia.org/wiki/QUIC) 通过复用连接解决了这个问题。

感谢阅读！我希望你喜欢它并且学到一些东西！