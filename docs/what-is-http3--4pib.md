# 什么是 HTTP/3？

> 原文：<https://dev.to/grigorkh/what-is-http3--4pib>

IETF 的官员透露，HTTP-over-QUIC 实验协议应该重新命名为 HTTP/3。

从 HTTP/1.1(1999 年发布)到 HTTP/2(2015 年发布)的开发有很大的差距，随着 HTTP/3 的发布，事情将在 2019 年发生。

HTTP/3 是 Google 的 QUIC 协议的发展。从马克·诺丁汉的这个[建议](https://mailarchive.ietf.org/arch/msg/quic/RLRs4nB1lwFCZ_7k0iuz0ZBa35s)开始。

## 那么什么是 QUIC 呢？

如 Chromium 项目主页所述:
QUIC(快速 UDP 互联网连接)是一种新的传输方式，与 TCP 相比，它减少了延迟。从表面上看，QUIC 非常类似于 UDP 上实现的 TCP+TLS+HTTP/2。因为 TCP 是在操作系统内核和中间体固件中实现的，所以对 TCP 进行重大修改几乎是不可能的。然而，由于 QUIC 是建立在 UDP 之上的，它没有这样的限制。

与现有的 TCP+TLS+HTTP2 相比，QUIC 的主要特性包括

*   显著减少连接建立时间
*   改进的拥塞控制
*   无行首阻塞的多路复用
*   前向纠错
*   连接迁移

Google [称](https://blog.chromium.org/2015/04/a-quic-update-on-googles-experimental.html)Chrome 对 Google 服务器的所有请求中，大约有一半是通过 QUIC 服务的，他们还在继续增加 QUIC 流量，最终使其成为 Google 客户端——Chrome 和移动应用——对 Google 服务器的默认传输。他们计划正式向 IETF 提出 QUIC 作为互联网标准，但他们首先要做一些日常工作，比如改变线路格式，将他们的参考实现从 SPDY-over-QUIC 更新为 HTTP2-over-QUIC(当前的 [HTTP-over-QUIC 协议草案](https://tools.ietf.org/html/draft-ietf-quic-http-16#page-4)使用新发布的 TLS 1.3 协议)。在接下来的几个月里，谷歌还计划降低握手开销，以实现更好的服务器端可扩展性，改善前向纠错和拥塞控制，并增加对多路径连接的支持。

[![](img/9e6e11edd4f37417fdcac00dc704548a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0hlAeTAi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Ar6NNOhOGncUfvHXKHUM39w.gif)

reddit 用户对 TCP vs QUIC 的精彩解释:

`TCP was developed when we still were transmitting packets over networks that had much larger packet loss than we do today and computer systems had much longer to answer TCP messages. The timeout for connecting to a host for example is still 20 seconds even though you are very unlikely to ever get an answer if the TCP handshake alone can’t even be completed within 5 seconds. These long delays are reasons why networked applications sometimes get stuck for a long time. We haven’t touched these delays since the protocol was invented in the 70s even though we saw massive improvements on reliability and speed.
Instead of finally reducing these defaults which would not alter the packets and is largely compatible with the current TCP implementations, protocol developers just started using UDP and then implement their own TCP on top of it. The transition to IPv6 would have been the ideal time to also update TCP to a version that fixes most of the issues it has, mostly timeouts, window size and TCP slow start. Some of the values can be tweaked in your OS, but the timeout, which is one of the most annoying can’t. If you kill a TCP socket that hangs for 5 seconds, your OS will still leave it open until the 20 seconds expired, consuming system resources.`

*原文发表于此:[https://medium.com/devgorilla/what-is-http-3-94335c57823f](https://medium.com/devgorilla/what-is-http-3-94335c57823f)T3】*

[![](img/14ce46ce82f3476059194b1fa4b9c347.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0IyP1yFi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Az5o-mslkNcdDbGzo6G3zqg.jpeg)