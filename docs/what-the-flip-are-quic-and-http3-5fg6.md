# QUIC 和 HTTP/3 是什么？

> 原文：<https://dev.to/inductor/what-the-flip-are-quic-and-http3-5fg6>

# TL；速度三角形定位法(dead reckoning)

IETF(互联网工程任务组)已经同意将 HTTP-over-QUIC 重命名为 HTTP/3 。它们到底是什么？

# 各用一句话描述 QUIC 和 HTTP/3。

**注:奎克！= HTTP/3，icac+http/2 API = = = http/3[**

QUIC 是一个新的传输协议，最初是由 Google 开发者提出的，用来解决当前 HTTP/1.1 和 HTTP/2 存在的许多问题。

HTTP/3，以前叫 HTTP-over-QUIC，是一个集成了 QUIC 和 HTTP/2 的协议栈。

# 在你学会它们之前

好了，既然你已经阅读了描述，在你开始了解它们之前，你需要知道当前的 HTTP 是如何工作的。

HTTP(超文本传输协议)是一种应用层协议，用于传送超文本文档，如 HTML 和 XML。如今，它在互联网上几乎无处不在，用于许多不同的目的，现在它被称为“万维网”。

# 他们为什么需要改变？

HTTP 最初是由欧洲粒子物理研究所的科学家蒂姆·伯纳斯·李提出的，但是最初的协议非常简单，只有 GET 方法。它非常简单，只有一页文档。

现在我们对大多数流量使用 HTTP/1.1，但是它有以下问题

*   HTTP 是一个有 30 年历史的协议
*   协议中不包括安全层
    *   除非服务器维护者启用 TLS，否则任何流量都可能被人看到
    *   ref:[https://lets encrypt . org/2014/11/18/announcing-lets-encrypt . html](https://letsencrypt.org/2014/11/18/announcing-lets-encrypt.html)
    *   甚至 TLS 1.2 也有一些问题(如果没有有效的理由，不要使用比 TLS 1.1 更早的版本:P)
*   当你有大量流量时，保持阻塞可能是一个大问题
    *   HTTP/1.1 和 TCP 都从不同方面存在这个问题
    *   HTTP/2 从 HTTP 的角度解决了这个问题
    *   只要 HTTP 依赖于 TCP，即使您启用了 HTTP/2，也无法解决基于 TCP 的 HoL 阻塞
    *   一个包丢失使所有流等待，直到包被接收

# HTTP/3 如何解决这些问题？

*   QUIC 工作于 UDP 协议(！)
*   QUIC 层给出...
    *   加密与具有 0 RTT 握手的 TLS 1.3 集成
    *   ref:[https://speaker deck . com/kazuho/security-privacy-performance-of-next-generation-transport-protocols](https://speakerdeck.com/kazuho/security-privacy-performance-of-next-generation-transport-protocols)
    *   没有“传统”TCP 的拥塞控制
    *   在移动网络和 UUID 连接下的高可访问性(切换 Wi-Fi LTE 不会重新协商)

# 协议概述

[![](img/8bad040d33269c5e933c199e89bc08fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fO39s5-1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/Dq0JawMXQAAv2du.jpg) 
[形象信用](https://twitter.com/kazuho/status/1057511844032798720)

## 译注

認証・鍵交換:认证/密钥交换
優先度制御:优先级控制
多重化:多路复用
暗号化:加密
トランスポート:传输

希望对你理解它们有所帮助:)