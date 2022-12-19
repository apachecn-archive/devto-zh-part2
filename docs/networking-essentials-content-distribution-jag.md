# 网络要素:内容分发

> 原文：<https://dev.to/swyx/networking-essentials-content-distribution-jag>

*这是我参加[免费的 Udacity 计算机网络基础课程](https://www.udacity.com/course/computer-networking--ud436)的一系列课堂笔记中的第八篇。*

在 [HTTP](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol) 中，您的浏览器通过网络向服务器发出请求，并接收包含内容的响应。请求被分层在字节流协议之上，通常是 [TCP](https://en.wikipedia.org/wiki/Transmission_Control_Protocol) 。服务器不保留任何关于客户机的信息，所以它是“无状态的”(有一些常见的方法可以解决这个问题)。

## HTTP 请求

*注意，这对于 HTTP/1 来说是成立的；HTTP/2 正在兴起，这里不做介绍*

HTTP 请求的内容包括

*   请求行
    *   请求的**方法**: GET、HEAD、POST、PUT、DELETE
    *   请求的 URL 端点，例如“/index.html”
    *   我们正在使用的 HTTP 版本，例如 1.1
*   头球
    *   **引用者**——是什么导致页面被请求
    *   用户代理 -使用的客户端软件，如 Chrome 或 Firefox

## HTTP 响应

*注意，这对于 HTTP/1 来说是成立的；HTTP/2 正在兴起，这里不做介绍*

HTTP 响应的内容包括

*   状态行
    *   我们正在使用的 HTTP 版本，例如 1.1
    *   响应代码 eg `100, 200 OK, 300, 301, 404, 500`
*   其他标题
    *   位置(用于重定向)
    *   服务器(用于服务器软件)
    *   允许(对于允许的 HTTP 方法)
    *   内容编码(例如，是否经过压缩)
    *   内容长度(以字节为单位)
    *   过期(用于缓存)
    *   最后修改的

## HTTP/2

HTTP/2 不使用换行符分隔请求和响应，而是将数据分成更小的消息和帧。它还允许新的功能，如伪报头字段(`:path`、`:authority`等)和 ALPN 协议标识符。这超出了本文的范围，但是[谷歌网络基础](https://developers.google.com/web/fundamentals/performance/http2/)和 [HPBN](https://hpbn.co/http2/) 以及[维基百科](https://en.wikipedia.org/wiki/HTTP/2)都有很好的资源。

## 缓存

为了提高 web 性能，我们缓存了一些东西，这样我们就不必在每次需要经常获取的东西时都要多次跳转到原始服务器。我们可以把东西藏在不同的地方:

*   在浏览器中(也称为“本地”)
*   在网络中(您的 ISP 或 CDNs)

如果多个客户端(也就是用户)都请求相同的内容，网络中的缓存(比如由您的 ISP 提供)会特别有好处，这使得加载速度更快，并且还为 ISP 节省了资金，因为它不必通过其他昂贵的链接到达源。

缓存根据过期设置器定期使内容过期，并检查来源。如果什么都没有被修改，源服务器发回一个`304 Not Modified`响应。

决定在哪里缓存有两种方式:

1.  指向本地缓存的显式浏览器配置
2.  服务器定向-原始服务器可能会将您指向一个缓存。这是通过对 DNS 请求的特殊回复来完成的

## (Web) CDNs

* *什么是 CDN？

*   CDNs 是一个 web 缓存覆盖网络，旨在从最佳(通常是地理上最近的)位置向客户端交付内容。
*   离用户很近的不同的、地理上分散的服务器。

[![https://mjau-mjau.com/content/2.blog/6.cloudflare-page-caching/network-map.png](../Images/096f5e8e5eef1cffe8993c2a4abd191a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZAIyrfsv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mjau-mjau.com/content/2.blog/6.cloudflare-page-caching/network-map.png)

cdn 可以属于大型内容提供商，如谷歌，或者独立网络(如 Akamai 和 Netlify)和 ISP(如美国电话电报公司或 Level 3)。为了说明这些网络的规模，谷歌有大约 30，000 个前端缓存节点，而 Akamai 在全球 72 个国家的 1000 个独特的网络中有 85，000 个独特的缓存服务器。

**运营 CDN 的挑战**

目标是在许多服务器上复制内容，但未解决的是:

*   如何复制内容？
*   在哪里复制？
*   客户端应该如何找到复制的内容？
*   客户端应该如何选择服务器副本？(又名“服务器选择问题”)
*   选择复制副本后，如何定向客户端？(又名“内容路由”问题)

**服务器选择如何在 CDN 中工作**

将客户端定向到哪个服务器？

*   最低负荷？
*   最低延迟？
*   任何随机的“活”服务器？

**内容路由，也就是客户端如何被重定向到服务器**

1.  基于路由的重定向(例如[任播](https://en.wikipedia.org/wiki/Anycast))。这是最简单的，但是它给服务提供商很少的控制权。
2.  基于应用的路由(例如 HTTP 重定向)。这是有效的，但是要求第一个请求到达源服务器，所以它并不能真正帮助减少第一次加载延迟
3.  **基于命名的路由**(如 DNS)。这是最常见的方法，因为客户端查找域名，得到的响应是附近缓存的地址。这为将不同的客户端定向到不同的服务器副本提供了极大的灵活性。它速度很快，并提供细粒度的控制。

## 基于命名的重定向

当你从纽约查找`symantec.com`时，你可能会得到一个`CNAME`(规范名称)指引你到一个像`a568.d.akamai.net`这样的域名服务器。来自`a568.d.akamai.net`的`A`响应会将你导向一个类似`207.40.194.46`的 IP 地址。

当你在不同的城市做同样的事情时，域名服务器会用一个不同的 IP 地址来响应，比如`81.23.243.152`，它在那个城市离你更近。这就是基于命名的重定向的工作方式。

## CDN 和 ISP 对等

cdn 和 ISP 是非常共生的。cdn 喜欢 ISP，因为:

*   与 ISP 的 CDNs 对等提供了更好的吞吐量，因为没有中间 AS 跳。
*   有更多的向量来路由内容(冗余)增加了可靠性
*   拥有与托管内容的多个网络的直接连接，允许 ISP 将内容分布在多个传输链路上，这有助于处理突发流量，同时降低 95%的负载，从而降低流量成本。

ISP 喜欢 cdn 是因为:

*   它们提高了客户的绩效
*   它们降低了运输成本

## 比特洪流

BitTorrent 是一个点对点 CDN，用于文件共享，特别是大型文件。

源服务器在处理大文件时会有问题，因为每个人对该文件的请求都意味着源服务器的拥塞或过载。因此，解决方案是分割文件并在所有对等点上复制它，这样您就可以从其他对等点而不是单个服务器上获取内容。

发布过程是:

*   一个对等点创建了一个“种子”，它有一个指定的跟踪器并列出一个文件的所有块(以及它们的校验和)
*   “种子”创建文件的初始副本
*   为了下载，客户联系追踪者，追踪者有所有“种子”的列表。它还会返回一个随机的“吸血鬼”列表，这些吸血鬼是包含文件不完整副本的客户端
*   客户端开始从种子下载文件块
*   这些部分可能与其他客户端下载的部分不同。
*   客户端现在可以开始相互交换数据块。

这种模式的问题是**免费下载**，一个客户下载完一个文件就离开网络，没有为其他也想要这个文件的人提供好处。

免费下载的解决方案是**阻塞**——暂时拒绝上传数据块给另一个请求它们的对等体。这似乎是一个违反直觉的限制，但是结合规则，如果一个对等点不能从客户端下载，那么它也不会上传。这就消除了搭便车的动机。

## 组块交换

交换块的算法对 Bit Torrent 很重要。它采用了“先下载最稀有的作品”的策略，把最常见的作品留到最后下载。然而，根据定义，罕见的作品可能在最少的客户端可用，因此可能会降低下载时间。客户可能会选择从种子公司的“随机件优先”策略开始。

最后，您只想从所有对等点请求缺失的部分，并在该部分到达时取消多余的请求。

## 分布式哈希表

分布式哈希表启用**结构化内容覆盖图**。一种常见的分布式哈希表是 [Chord](https://en.wikipedia.org/wiki/Chord_(peer-to-peer)) ，它使用**一致性哈希**来建立一个直观的、可伸缩的、分布式的键值“查找服务”(例如 DNS 或目录)，具有可证明的正确性和良好的性能。

**一致哈希**

这里的主要思想是**键**和**节点**映射到同一个 **ID 空间**。

[![http://blog.plasmaconduit.com/content/images/2014/Sep/consistent_hashing_003.jpg](../Images/1bab762def8743b1e5559fbf7d2ed238.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tmnFR3vW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.plasmaconduit.com/content/images/2014/Sep/consistent_hashing_003.jpg)

像 SHA1 这样的散列函数被用来为密钥(例如散列密钥)和节点(例如散列 IP 地址)分配标识符。完成后，我们将键 id 映射到节点 id，这样我们就知道哪个负责特定键的查找。 **Chord** 的想法是**一个密钥存储在它的后继节点**，也就是 ID 次高的节点。(如果 ID 是最高的数字，它绕到第一个节点，因此是环形格式)

这个系统提供了负载平衡，因为节点接收的密钥数量大致相同(给定像 SHA1 这样的均匀分布的散列算法)。

该系统还提供了灵活性，因为当节点进入或离开网络时，只需要重新映射一些密钥。

系统的最后一部分是节点需要能够找到其他节点。你可以让每个节点都知道其他节点，但是这需要`O(N)`存储。您可以让每个节点只知道它们的成功，这有助于`O(N)`查找。快乐的中间点是**手指表**，其中每个节点都知道`m`其他节点，距离呈指数增长。例如，节点 10 将维护以下地址:

*   10 + 2^0:节点 11
*   10 + 2^1:节点 12
*   10 + 2^2:节点 14
*   等等

这减少了在`O(log N)`的存储和查找。

## 我们系列的下一个

希望这是对 CDNs、BitTorrent 和分布式哈希表如何工作的一个很好的高层次概述。我正在计划更多的初级读本，希望您能就以下方面提供反馈和问题:

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