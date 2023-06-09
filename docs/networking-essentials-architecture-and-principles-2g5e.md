# 网络基础:架构和原理

> 原文：<https://dev.to/swyx/networking-essentials-architecture-and-principles-2g5e>

*这是我参加[免费的 Udacity 计算机网络基础课程](https://www.udacity.com/course/computer-networking--ud436)的一系列课堂笔记中的第一篇。*

## 根本目标

对于这些信息，我们的主要参考是大卫·克拉克在 1988 年发表的关于 DARPA 互联网协议的设计哲学的论文。

互联网的基本设计目标是实现“现有互联网络的有效复用利用”。在英语中，这意味着我们**共享**连接的**网络的**用法。

*   **共享**的目标由 **[统计复用](https://en.wikipedia.org/wiki/Statistical_time-division_multiplexing)** (又名分组交换)解决。在**分组交换**中，每个发送的分组都携带着它自己的目的地信息，所以它可以被转发，类似于通过邮件发送的信件有它的目的地。这意味着网络可以在[最大努力](https://en.wikipedia.org/wiki/Best-effort_delivery)的基础上传递数据包，这使得共享成为可能，因为许多发送者可以同时使用同一个网络，但存在一些数据包丢失的风险。
    这与**电路交换**形成对比，后者更像一个电话网络——建立了直接连接，传输丢失/丢弃数据包的机会更小，但如果可用容量被占用，你就倒霉了。

*   **连接**网络的目标由“[窄腰](https://www.youtube.com/watch?v=uXumm52oBMo)解决。参考[OSI 7 层系统模型](https://en.wikipedia.org/wiki/OSI_model)，我们的**网络层**非常薄——它主要由[互联网协议](https://en.wikipedia.org/wiki/Internet_Protocol)组成！IP 为位于其上的**传输层**提供保障，通常是 [TCP](https://en.wikipedia.org/wiki/Transmission_Control_Protocol) 或 [UDP](https://en.wikipedia.org/wiki/User_Datagram_Protocol) ，而 [TCP/IP](https://en.wikipedia.org/wiki/Internet_protocol_suite) 的组合是最常见的端到端数据传输栈。在这个坚实的基础之上，我们可以分层我们想要发送数据的任何协议，例如用于网站的 [HTTP](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol) 或用于邮件的 [SMTP](https://en.wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) ！在 IP 层下面，我们还有[链路层](https://en.wikipedia.org/wiki/Data_link_layer)和[物理层](https://en.wikipedia.org/wiki/Physical_layer)，还有更多协议，如[以太网](https://en.wikipedia.org/wiki/Ethernet)和 [SONET](https://en.wikipedia.org/wiki/Synchronous_optical_networking) 。但关键是，中间的网络层是“狭窄的”——它只是由 IP 组成。这意味着在网络上获得任何设备都相当容易，它只需要说 IP！

[![the key parts of OSI model](img/3b16f0425b826b9e5e7813fe5d299991.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--y7YIZZVj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.slideplayer.com/24/7320353/slides/slide_25.jpg)

## 次要目标

次要目标都包括进一步定义上面列出的“基本目标”中“有效”一词的真正含义:

1.  **生存性**:尽管失去了网络或网关，互联网通信**仍必须继续。**
2.  **异构 1** :互联网必须支持**多种类型的通信服务**。
3.  **异构性 2** :互联网架构必须适应**多种网络**。
4.  **去中心化**:互联网架构必须**允许其资源的分布式管理**。
5.  **成本**:互联网架构必须**成本有效**。
6.  **轻松**:互联网架构必须允许**主机轻松连接**。
7.  **可跟踪性**:互联网架构中使用的资源必须**负责**。

我们在这里没有足够的空间来讨论所有这些问题的含义，但是我们将详细讨论几个重要的问题。

## 生存能力

我们认为这意味着即使一些设备出现故障或受到威胁，网络也应该继续工作。

我们如何实现这一点？一种方法是通过**复制**，我们在另一个节点复制状态，以便在出现故障时总有一个备份可以接管。然而，这与我们的另一个目标——成本进行了权衡。

另一个有趣的方法是[命运共享](https://en.wikipedia.org/wiki/Fate-sharing) -如果节点断开连接，我们认为简单地丢失所有与该节点相关的信息是可以接受的。

## 异质性

互联网的“窄腰”设计允许在 IP 上支持多种协议，而不仅仅是 TCP(如前所述)。这非常有助于为工作选择正确的协议，例如选择 UDP 作为流数据的目的，因为实时性比完全无损更重要。你甚至可以使用一个[协议组合](https://en.wikipedia.org/wiki/Domain_Name_System#DNS_Protocol_transport)来获得最好的速度和无损。

“尽力而为”的传递原则也意味着数据可以在任何类型的网络上或网络之间传递(包括[信鸽](https://www.cnet.com/news/pigeon-powered-internet-takes-flight/)！！)具有调试难度的折衷，因为没有失败数据返回。

## 放权

资源的分布式管理可以从[如何寻址](http://what-when-how.com/data-communications-and-networking/addressing-data-communications-and-networking/)中看出——我将在未来的初级读本中详细介绍。我们在世界范围内有五个[地区互联网注册管理机构](https://en.wikipedia.org/wiki/Regional_Internet_registry)——在美国我们有 [ARIN](https://en.wikipedia.org/wiki/American_Registry_for_Internet_Numbers) 和欧洲的对等机构[成熟](https://en.wikipedia.org/wiki/R%C3%A9seaux_IP_Europ%C3%A9ens_Network_Coordination_Centre)。

我们在命名方面也实现了去中心化- [DNS](https://en.wikipedia.org/wiki/Domain_Name_System) 通过为每个域指定权威的名称服务器，来委派分配域名和将这些名称映射到互联网资源的责任。通过[边界网关协议(BGP)](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) 的路由在对等体和社区之间也是分散的。我们还将在未来的初级读本中讨论这两个问题。

因为没有一个单独的实体负责，所以互联网能够有机地发展，并且非常稳定。然而，缺乏所有权也使问题难以解决。

## 问题与成长的烦恼

[![ipv4 rundown](img/36d6d3bbbfca3e7988e1eded6a9b06e6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--swmYtlip--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.theconversation.com/files/83083/original/image-20150527-4812-1iqroxb.png%3Fixlib%3Drb-1.1.0%26q%3D45%26auto%3Dformat%26w%3D1000%26fit%3Dclip)

我们今天在互联网上看到的许多问题都是网络需求超出 70 年代和 80 年代设计考虑的直接结果:

*   我们正在耗尽 IPv4 地址(T1)，只有 40 亿个 IP 地址，[分配效率低下(T3)。](https://www.quora.com/How-did-MIT-end-up-with-an-entire-class-A-subnet-of-the-IP-address-space)
*   拥塞控制——我们的网络在不稳定的连接上变得非常不稳定，尤其是因为重传被触发。我将在以后的初级读本中介绍这一点。
*   [路由](http://www.enterprisenetworkingplanet.com/netsp/article.php/3615896/Networking-101-Understanding-BGP-Routing.htm)——我将在后面的初级读本中介绍这个主题，但基本上它没有安全性，很容易被错误配置，并且表现出较差的收敛性和非确定性。不过，这也挺管用的。
*   安全也值得拥有自己的入门书:加密和认证的方法是存在的，但是我们在确保它们被*使用*方面做得还不够好。此外，密钥管理和安全软件部署也是公开的问题。
*   拒绝服务(Denial of Service)——互联网可能在向收件人递送包裹方面做得太好了，即使收件人不想要它们。

## 我们系列的下一个

希望这是一个很好的高层次的概述，说明了为什么我们需要交换机，以及如果我们要建立自己的互联网，它们将如何工作。我正在计划更多的初级读本，希望您能就以下方面提供反馈和问题:

*   [架构和原理](https://dev.to/swyx/networking-essentials-architecture-and-principles-2g5e)
*   [切换](https://dev.to/swyx/networking-essentials-switching-3eba)
*   [路由](https://dev.to/swyx/networking-essentials-routing-5gb7/)
*   [命名/寻址/转发](https://dev.to/swyx/networking-essentials-naming-addressing-and-forwarding-13kk)
*   [DNS](https://dev.to/swyx/networking-essentials-dns-1dl7)
*   [拥塞控制和流式传输](https://dev.to/swyx/networking-essentials-congestion-control-26n2)
*   [速率限制和流量整形](https://dev.to/swyx/networking-essentials-rate-limiting-and-traffic-shaping-43ii)
*   [内容分发](https://dev.to/swyx/networking-essentials-content-distribution-jag)
*   [软件定义网络](https://dev.to/swyx/networking-essentials-software-defined-networking-35n9)
*   [交通工程](https://dev.to/swyx/networking-essentials-traffic-engineering-13c4)
*   [网络安全](https://dev.to/swyx/networking-essentials-network-security-1fcp)