# 网络基础:拥塞控制

> 原文：<https://dev.to/swyx/networking-essentials-congestion-control-26n2>

*这是我参加[免费的 Udacity 计算机网络基础课程](https://www.udacity.com/course/computer-networking--ud436)的一系列课堂笔记中的第六篇。*

## 充血崩溃💩

假设我们有一个连接到目的地 D 的中央交换机 S，其连接容量为 1 Mbps。但是，我们有两台源主机 H1 和 H2 通过 S 连接，分别以 10 Mbps 和 100 Mbps 的速率向 D 发送数据。

确切的数字并不重要，但关键是 H1 和 H2 正试图以远高于 S-D 链路所能承受的速率发送数据。S-D 链路是一个瓶颈！

通过[互联网架构设计](https://dev.to/swyx/networking-essentials-architecture-and-principles-2g5e)，源主机 H1 和 H2 彼此都不知道对方，也不知道 S-D 链路的当前状态。因此，它们会非常低效地竞争瓶颈上的资源，这可能会导致数据包丢失和长时间的延迟，以至于 S-D 链路甚至会因为所有的故障而没有使用其全部容量。这就是所谓的**拥塞崩溃**。

[![https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSMCpIHEbLysGQSMdsWp9qRPhq45l8l2QPu_KkI_Zzyk-o6jD5f](img/db0cddb7e566b49cde0d91ee07dfcc40.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sOzNQSwj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://encrypted-tbn0.gstatic.com/images%3Fq%3Dtbn:ANd9GcSMCpIHEbLysGQSMdsWp9qRPhq45l8l2QPu_KkI_Zzyk-o6jD5f)

**拥塞崩溃**定义为**负载增加导致有用功减少**。它可能由以下原因引起:

*   虚假的重新传输(数据包的重新传输，这是由于 TCP 在数据包被丢弃且未收到 ack 时的工作方式)
    *   解决方案:更好的计时器和 TCP 拥塞控制
*   未送达的数据包(数据包消耗资源，但被丢弃在网络的其他地方)
    *   对所有流量应用拥塞控制

## 拥塞控制的目标👼

鉴于我们网络中不同带宽的现实，我们希望:

*   高效使用网络资源
***   维护**公平**的资源分配*   避免拥塞崩溃**

 **您可以使用相位图绘制两台主机之间的公平性和效率:

[![https://i.ytimg.com/vi/EfeAglStSd4/maxresdefault.jpg](img/e4b0d16e43c127b2abc48eddfe01df5c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EuydRfYQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ytimg.com/vi/EfeAglStSd4/maxresdefault.jpg)

你可以通过**网络辅助拥塞控制**来实现这些目标，其中路由器提供一个比特的反馈来指示拥塞(例如 TCP 中的[显式拥塞通知](https://en.wikipedia.org/wiki/Explicit_Congestion_Notification)扩展)，但更常见的是拥塞控制是隐式的，或者是**端到端**，其中网络不提供反馈，拥塞是通过查看数据包丢失和延迟来推断的。

## TCP 拥塞控制

在 TCP 拥塞控制中，发送方缓慢提高速率，直到数据包被丢弃。丢包被解释为拥塞，速率变慢。在拥塞是暂时的情况下(例如，由于路由器中的缓冲区达到最大值)，再次周期性地增加速率以重新测试网络。

增加/减少算法有两种工作方式。

*   **基于窗口的**算法在任何给定时间都有固定数量的数据包输出。当收到旧包的 ack 时，发送新包。因此，如果现存的数据包被丢弃，新的数据包就不会被发送，有效的发送速率就会降低，反之亦然。所以**窗口大小**是一个自然控制。窗口大小的*增加和减少*是不同的事情:当一个包被成功发送时，窗口大小“附加地”增加 1。当一个数据包失败时，窗口大小会“成倍地”减半。这种不对称的增加/减少算法被称为[加法增加，乘法减少(AIMD)](https://en.wikipedia.org/wiki/Additive_increase/multiplicative_decrease) 。
*   基于速率的算法改为监控它们的丢失率，并使用定时器来调整它们的速率。这种情况不太常见。

在相位图上，AIMD 平行于 X1 = X2“公平”线向上移动利率，并根据其与原点的角度向下移动利率:

[![http://omsnotes.com/62img/fig8.14.png](img/d1eefbbcee9374e2b037b03234f444ab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3TOsaVnf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://omsnotes.com/62img/fig8.14.png)

就随时间变化的个体比率而言，AIMD 还得出一个“锯齿”图:

[![http://omsnotes.com/62img/fig8.15.png](img/f5d41b4920c04d3bf3fa1ff0330718b9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZJ5exuF6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://omsnotes.com/62img/fig8.15.png)

由于 AIMD，TCP 发送方以其窗口大小的 3/4 的平均速率发送。

为了计算**吞吐量**，你用平均速率除以 RTT，或`3/4 * Wm/RTT`。

也是因为 AIMD，波谷之间的时间由`Wm/2 + 1`给出(`Wm/2`是每次增加一半窗户 1 的高度所花费的时间，而`1`是将其减少一半所花费的时间)。

为了计算**丢失率**，比较锯齿线下的面积(成功发送)与发送的总数据包，即`1/2 * Wm/2 * (Wm/2 + 1)`或基本上`Wm^2 / 8`。

**吞吐量和损失率**由`Throughput ~ k/(RTT * sqrt(Lossrate))`关联。

## TCP in cast 问题

在数据中心的条件下，会出现一个特殊的拥塞问题。 **Incast** 是指当使用 TCP 的服务器同时请求数据(有时使用[屏障同步](https://docs.oracle.com/cd/E19120-01/open.solaris/816-5137/gfwek/index.html))时，应用吞吐量的急剧下降，从而导致数据中心等多对一通信网络中网络容量的严重利用不足。这是由于:
-要求高带宽和低延迟
-来自服务器的许多并行请求
-数据中心中的高“扇入”
-交换机中的小缓冲区
-填满的交换机缓冲区导致持续数百毫秒
的 TCP 超时所导致的突发重传，但是(如上所述)要求低延迟通常在 1 毫秒的数量级<。

两种解决方案是:

*   精细粒度的 TCP 重传计时器的数量级为微秒而不是毫秒
*   客户端每隔一个数据包而不是每个数据包进行确认，从而降低网络负载

底线:计时器应该在接近网络 RTT 的粒度上运行，并且在数据中心中小于 1 毫秒。

## 多媒体和流媒体

这篇文章的下半部分着重于处理发送多媒体和流的独特问题。这些情况的一些特征是:

*   大量数据(如带声音的视频)
*   数据量随时间变化(取决于压缩算法)
*   延迟变化容忍度低
*   延迟容忍度低

其中最重要的是延迟变化。我们可以建立一个客户端缓冲区来管理一些变化和差异，但是延迟变化会威胁到平滑播放，尤其是在缓冲区不足的情况下。

TCP 不太适合流式传输。它旨在:

*   可靠地交付——但是客户可能不关心这个
*   丢失时减速——这导致延迟变化
*   每个数据包有 20 字节的报头，开销很大

[UDP](https://en.wikipedia.org/wiki/User_Datagram_Protocol) 更好:

*   不重新传输
*   无发送速率适配
*   小标题

## 案例分析

**Youtube(流媒体视频)**

所有视频都转换成 Flash，每个浏览器都可以用 HTTP/TCP 播放。尽管 TCP 不太适合流媒体，但 Youtube 的创建者更喜欢保持简单，这样它就可以利用现有的 HTTP/TCP 基础设施，如 cdn。

**Skype/VOIP**

Skype 有一个中央登录服务器，但使用 P2P 来交换数据。它压缩到一个相当低的比特率(67 字节/包，140 包/秒或 40kbps)，双向加密。但是它仍然很容易受到网络质量下降的影响。

## 服务质量

我们可以使用显式预留，或者标记和监管更高优先级的数据包流。

如果我们有一个 VOIP 应用程序和一个 FTP 应用程序共享相同的带宽，我们希望确保 VOIP 有更高的优先级。我们可以在音频包到达路由器时用优先级队列标记它们，然后使用调度(例如[加权公平队列](https://en.wikipedia.org/wiki/Weighted_fair_queueing),在这里你只需服务于高优先级队列比低优先级队列更频繁)

## 我们系列的下一个

希望这是对拥塞控制及其一些实际实现/应用的高水平概述。我正在计划更多的初级读本，希望您能就以下方面提供反馈和问题:

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
*   [网络安全](https://dev.to/swyx/networking-essentials-network-security-1fcp)**