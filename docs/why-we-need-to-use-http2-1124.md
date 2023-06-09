# 为什么我们需要使用 HTTP/2

> 原文：<https://dev.to/grigorkh/why-we-need-to-use-http2-1124>

HTTP/2 是 15 年来 HTTP 协议的第一次重大升级。在此期间，网站发生了巨大的变化，外部图像、CSS 和 JavaScript 资产的数量逐年增加。HTTP/1.1 不是为这种复杂性而设计的。HTTP/2 针对现代网站进行了优化，提高了性能，而没有像域分片和文件连接这样复杂的攻击。采用 HTTP/2 可以在不改变现有代码库的情况下提高网站的速度。

#### 测试结果

在这个测试中，HTTP/2 一直比 HTTP/1.1 快

*   WiFi / 20Mbps 电缆速度快 4 倍，平均服务器 ping 50ms 毫秒
*   LTE 网络速度快 6 倍，平均服务器 ping 90ms 毫秒
*   3G 网络速度快 15 倍，平均服务器 ping 120ms 毫秒
*   2G 网络速度快 2 倍，平均服务器 ping 400ms 毫秒

HTTP/2 在 2G 网络上仅快 2 倍的原因是由于边缘带宽限制，因为在 170Kbps 时链路会迅速饱和。

HTTP/2 的主要目标是通过启用完整的请求和响应多路复用来减少延迟，通过有效压缩 HTTP 头字段来最小化协议开销，以及添加对请求优先级和服务器推送的支持。为了实现这些要求，有大量其他协议增强的支持，如新的流量控制、错误处理和升级机制，但这些是每个 web 开发人员都应该了解并在他们的应用程序中利用的最重要的功能。

[![](img/111a708de4a4787c4c3b5d717ef0b2f2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UPmrOKax--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Aq7z5lRT1ByNJWxXRXayUVQ.jpeg)

HTTP/2 不会以任何方式修改 HTTP 的应用程序语义。所有的核心概念，比如 HTTP 方法、状态代码、URIs 和头字段，都保持不变。相反，HTTP/2 修改了数据格式化(成帧)和在客户机和服务器之间传输的方式，客户机和服务器都管理整个过程，并在新的成帧层中对我们的应用程序隐藏了所有的复杂性。因此，所有现有的应用程序无需修改即可交付。

> *为什么不是 HTTP/1.2？*
> 
> *为了实现 HTTP 工作组设定的性能目标，HTTP/2
> 引入了一个新的二进制帧层，它与以前的 HTTP/1.x 服务器和客户端不向后兼容，因此主要协议版本增加到了 HTTP/2。*
> 
> 也就是说，除非你通过使用原始 TCP 套接字来实现 web 服务器(或定制客户端),否则你看不到任何区别:所有新的、低级的成帧都是由客户端和服务器代表你执行的。唯一可观察到的区别将是改进的性能和新功能的可用性，如请求优先级、流量控制和服务器推送！

HTTP/2 定义了请求优先级并支持取消，因此如果应用程序需要呈现，即动态图库预览，它可以为初始视图请求高优先级图像，请求列表中的图像，即如果用户将导航离开或向下滚动列表，则取消它们的下载。

#### 时间轴

*   2012 年 3 月:HTTP/2 提案征集
*   2012 年 11 月:HTTP/2 的初稿(基于 SPDY)
*   2014 年 8 月:发布 HTTP/2 草案 17 和 HPACK 草案 12
*   2014 年 8 月:工作组对 HTTP/2 的最后一次呼吁
*   2015 年 2 月:IESG 批准了 HTTP/2 和 HPACK 草案
*   2015 年 5 月:RFC 7540 (HTTP/2)和 RFC 7541 (HPACK)发布

### **HTTP/2 推送—机会**

与 HTTP/1 相比，HTTP/2 在许多方面都有巨大的性能提升，而[服务器推送](http://httpwg.org/specs/rfc7540.html#PushResources)是其性能特性之一。

一个典型的(也是简化的)HTTP 请求/响应流程是这样的(下面的截图是用来连接黑客新闻的):

[![](img/9763acbce40332247319f1a7eed07275.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--q2aMy-1v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AYvOWVbP5yd5nmJ55nKuKRA.png)

1.  浏览器请求一个 HTML 文档。
2.  服务器处理请求并生成/发送 HTML 文档。
3.  浏览器接收响应并解析 HTML 文档。
4.  它标识了呈现 HTML 文档所需的更多资源，如样式表、图像、JavaScript 文件等。它发送更多对这些资源的请求。
5.  服务器用相应的资源响应每个请求。
6.  浏览器使用 HTML 文档和相关资源呈现页面。

这意味着呈现一个 HTML 文档通常需要多次往返的请求/响应，因为有额外的资源与之相关联，并且浏览器需要它们来正确地呈现文档。如果所有这些相关的资源都可以和原始的 HTML 文档一起发送到浏览器，而不需要浏览器请求它们，那就太好了。这就是 HTTP/2 服务器推送的目的。

在 HTTP/2 中，服务器可以主动*推送*额外的资源以及对它认为浏览器稍后将请求的原始请求的响应。后来，如果浏览器真的需要它们，它只是使用已经推送的资源，而不是发送额外的请求。