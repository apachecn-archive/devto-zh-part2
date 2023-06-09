# 网络基础:交换

> 原文：<https://dev.to/swyx/networking-essentials-switching-3eba>

*这是我学习[免费的 Udacity 计算机网络基础课程](https://www.udacity.com/course/computer-networking--ud436)的系列课堂笔记中的第二篇。*

让我们从头开始建立自己的内部网。我们首先会遇到什么问题？

假设我们只有两台机器，每台机器都通过某种网络接口(比如以太网适配器)连接在一起。

[![https://4.bp.blogspot.com/-kaAW47FyDRU/WP8WmlbyhzI/AAAAAAAAAdQ/Ml_5Upx4diwlLmLxsmu45eDh2B4F88YCgCLcB/s1600/How%2Bto%2BConnect%2BTwo%2BComputers%2Bwithout%2Ba%2BRouter.png](img/e3e1ecfbf4caa1486c0a986eaf3c994d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rSDujjjg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://4.bp.blogspot.com/-kaAW47FyDRU/WP8WmlbyhzI/AAAAAAAAAdQ/Ml_5Upx4diwlLmLxsmu45eDh2B4F88YCgCLcB/s1600/How%252Bto%252BConnect%252BTwo%252BComputers%252Bwithout%252Ba%252BRouter.png)

制造商给每台机器(或主机)分配一个物理地址(也称为 T2 MAC 地址)，这样我们就可以区分它们。我们还为每台机器分配一个唯一的 IP 地址——这是公开信息，但 MAC 地址不是。

[正如我们之前在分组交换](https://dev.to/swyx/networking-essentials-architecture-and-principles-2g5e)中讨论的那样，我们从一台主机发送到另一台主机的单个数据包，也称为[数据报](https://en.wikipedia.org/wiki/Datagram)，上面简单地印上了它们的目的地地址。(更准确地说，主机**将**数据报封装在[以太网帧](https://en.wikipedia.org/wiki/Ethernet_frame)中，帧上有目的 MAC 地址。)

看起来很简单，但是我们已经遇到了第一个问题。

请记住，我们正在从头开始建立我们的内部网。这些机器是全新的，刚刚第一次连接起来。**主机如何获知另一台主机的 MAC 地址？？**

## 地址解析协议

鉴于以下限制，ARP 几乎是解决此问题的唯一可能方法:

1.  第一次传输到新的 IP 地址时，主机会向网络中的每个人询问谁拥有该特定 IP 地址
2.  匹配该 IP 地址的一方响应(在 DM 中😎)及其 MAC 地址
3.  主机开始构建一个简单的表，根据在其生命周期内收到的响应将每个 IP 地址映射到 MAC 地址
4.  对于将来向该 IP 地址的传输，主机不必再询问，它可以立即开始向存储的 MAC 地址发送数据报*给其网络中的任何人*

相当整洁！你可能会问:为什么这么复杂？为什么每台机器必须有两组地址？今天有许多实际的考虑因素，但最基本的是，这是我们在上一篇文章中讨论的多层网络模型[的结果——你正在从链路层(MAC 地址)转换到互联网层(IP 地址)，它们是分离的，或好或坏。](https://dev.to/swyx/networking-essentials-architecture-and-principles-2g5e)

同样，我们发送的数据包是低层的数据报，但通过互联网层发送的实际上是以太网**帧**(附有目的地址)。

## 集线器:广播

好了，我们已经知道了如何在两台机器之间发送数据。你如何将它扩展到，比如说，10 台机器？

首先让我们考虑一个叫做**中枢**的想法。**集线器**是帮助你广播信息的最简单的互联形式。如果一台主机向集线器发送帧，集线器会将该帧广播给它所连接的所有其它集线器和主机。

[![https://geek-university.com/wp-conteimg/ccna/how_hub_works.jpg?x13092](img/42c1ddbd5dd8d4d6f6e8a7a724bb3641.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QuMb_toy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://geek-university.com/wp-conteimg/ccna/how_hub_works.jpg%3Fx13092)

这被称为[泛洪](https://en.wikipedia.org/wiki/Flooding_(computer_networking))，它会导致延迟，并可能导致任何给定端口中的[冲突](https://en.wikipedia.org/wiki/Collision_domain)。

如果一个黑客想淹没网络，他们所要做的就是发送一堆帧到几个集线器，集线器会很乐意代表黑客关闭网络。这就是为什么我们不希望集线器广播一切，我们应该设法将流量与集线器隔离开来。

## 交换机:流量隔离

交换机将我们的网络细分为**段**。一个数据段中的帧，如果其目的地与源数据段在同一个数据段中，则该帧不会被广播到其它数据段。交换机可以与集线器一起工作:

[![http://www2.ic.uff.br/~michael/kr1999/5-datalink/hubSwitch.jpg](img/a47d0e21dcf1622a945b54bc59c6c201.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B9LlwKsM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www2.ic.uff.br/%257Emichael/kr1999/5-datalink/hubSwitch.jpg)

因此，在一个网段内，集线器负责广播，但**交换机**的工作是确保离开网段的唯一数据是针对其他网段的数据。交换机比仅仅广播数据更聪明，所以它们也建立了**交换表**，将目的 MAC 地址映射到交换机的输出端口。这被称为一个 **[学习开关](https://telconotes.wordpress.com/2013/03/09/how-a-switch-works/)** ，当它遇到一个它不知道的新地址时，它会泛洪它所有的端口并记录下响应者的地址。

然而，当网络中引入环路时，泛洪可能会失去控制(在任何特定连接失败的情况下，为了弹性而添加到节点的多个连接)，并且交换机泛洪它之前已经泛洪的数据(创建[周期](https://en.wikipedia.org/wiki/Cycle_(graph_theory)))。

## 生成树:打破循环

我们用[生成树协议](https://en.wikipedia.org/wiki/Spanning_Tree_Protocol)打破以太网中的循环。基本思想是有一个网络子集，它接触网络的所有节点，但没有任何环。然后，我们将使我们的交换机仅泛洪/广播生成树上的连接，从而保证交换机不会泛洪自己。

构建生成树是一种混乱的递归算法:

*   交换机为树挑选一个根(例如具有最小 ID 的交换机，但是实际的[选举过程](http://www.firewall.cx/networking-topics/protocols/spanning-tree-protocol/1054-spanning-tree-protocol-root-bridge-election.html)非常复杂——如果所有的交换机都具有相同的交换机 ID，那么具有最小 MAC 地址(数字将超过字母)的交换机将成为根交换机，或者您可以通过创建环回地址来强制特定的交换机成为根交换机。)
*   交换机将它们到根的距离传递给它们所连接的其它交换机
*   每台交换机都排除了不在通往根的最短路径上的任何链路(因此每台交换机只有一条出站链路，但可能有多条来自远离根的交换机的入站链路)

## 缓冲区大小调整

因为我们选择使用[统计多路复用](https://dev.to/swyx/networking-essentials-architecture-and-principles-2g5e)，我们的交换机也需要一个缓冲区来存储可能同时到来的多个数据包。他们需要多少？

假设往返延迟为 2T，链路的瓶颈容量为 C，则缓冲区大小的经验法则是 2T * C。这只是表示在任何时间点源、路由器和目的地之间可能存在的最大未处理数据量。你可以从数学上推导出这个值，或者把它想象成锯齿流的高度，但这已经超出了我们的范围。

[![https://i.ytimg.com/vi/vYRJs-ZRISM/mqdefault.jpg](img/aa571b6ec95be24c04c2be94998209c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AfJ7g0_H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ytimg.com/vi/vYRJs-ZRISM/mqdefault.jpg)

**先不说**:2T * C 最大缓冲区大小的经验法则对于现实生活中的网络是不必要的。主干网交换机一次可以有 20，000 多个连接，通过它们进入的 TCP 流不会同步。这意味着锯齿波的最小值和最大值很可能不是所有连接的线性倍数，并且缓冲需求将很可能收敛到统计平均值。所以更多的推导使我们得出结论，假设 TCP 流是非同步的，一个好的缓冲区是 2T * C / sqrt(N)。

[![https://ars.els-cdn.com/content/image/1-s2.0-S0140366415002480-gr1.jpg](img/86d2b5043bb1d72fa3f8e153a675768f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--riRBq1a1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ars.els-cdn.com/content/image/1-s2.0-S0140366415002480-gr1.jpg)

*注意去同步场景中较低的锯齿高度*

## 交换机 vs 路由器

交换和路由是有些竞争，有些平行的概念。在这篇文章中，我们探讨了交换作为一个更简单的概念，通过一个分散的网络有效地传输数据。但是接下来我们将解释路由与交换的对比。

交换机位于 OSI 模型的第 2 层，即以太网层。它们是**自动配置**(正如我们上面看到的，它们只是计算出自己的生成树和地址解析)，并且转发往往很快。然而，生成树和 ARP 查询会给网络带来很大的负载。

路由器和 VLANs 位于 OSI 模型的第 3 层，即 IP 层。它们不限于生成树- [多路径路由](https://en.wikipedia.org/wiki/Multipath_routing)使用多条路径来提高带宽/弹性，软件定义网络等进步进一步模糊了第 2 层和第 3 层之间的界限。他们的主要目的是打破广播域。我们将在下一篇文章中探讨这个问题。

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