# 网络基础:软件定义的网络

> 原文：<https://dev.to/swyx/networking-essentials-software-defined-networking-35n9>

*这是我参加[免费的 Udacity 计算机网络基础课程](https://www.udacity.com/course/computer-networking--ud436)的系列课堂笔记中的第九篇。*

* * *

这是关于网络操作和管理的 3 部分迷你系列的第一部分。

*   [软件定义的网络](https://dev.to/swyx/networking-essentials-software-defined-networking-35n9)
*   [交通工程](https://dev.to/swyx/networking-essentials-traffic-engineering-13c4)
*   [网络安全](https://dev.to/swyx/networking-essentials-network-security-1fcp)

* * *

在讨论网络管理的先驱 SDNs 之前:

## 网络管理

网络管理是配置网络以完成各种任务的过程:

*   负载平衡
*   安全性
*   商业关系

如果网络配置不正确，很多事情都会出错。配置错误会导致如下问题:

*   持续振荡(路由器不同意如何路由到目的地)
*   环路(数据包卡在路由器之间，无法到达目的地)
*   分区(网络分成两个不相连的网段)
*   黑洞(数据包到达路由器后会被丢弃)

配置很难，因为:

*   定义“正确”很难
*   协议之间的交互导致不可预测性
*   人们会在数百个特定于供应商的低级配置中犯错误

**软件定义的网络**改变了这种情况，通过提供以下功能将逻辑集中在一个控制器中:

*   拓扑和流量的全网络视图
*   网络级目标(如负载平衡和安全性)
*   直接控制

这意味着我们以前拥有的完全分布式路由模型必须结束，路由器不再需要计算自己的路由，也就是从路由器中删除路由。

## 软件定义网络(SDN)

什么是 SDN？

这有助于为通常的 2D 网络概念增加第三个维度。我们可以将网络的**数据平面**的基本工作定义为**转发流量**。但是我们也可以定义一个网络的**控制平面**，它计算路由表，考虑每个路由器的状态。

控制平面是控制转发行为的逻辑，例如路由协议和网络中间盒配置。

数据计划只是根据控制平面逻辑转发和交换流量。

在传统网络中，数据和控制平面放在路由器上。但是在 SDN 中，控制是集中的，而数据仍然是分散的。

这一想法始于 2004 年的 BGP 协议的[路由控制平台](https://queue.acm.org/detail.cfm?id=2560327)，并在不久后推广到 4 个不同的平面( **4D** ):

*   决策平面
*   传播/发现平面
*   数据平面

2008 年，随着交换机供应商开放他们的 API 并采用 [Openflow](https://en.wikipedia.org/wiki/OpenFlow) 协议，这一点变得切实可行。

**SDN 的优势是什么？**

他们更容易:

1.  坐标
2.  演进(如数据中心的虚拟机迁移)
3.  关于...的理由

通过在现有网络旁边部署研究实验也更容易进行实验。

拥有一个独立的**控制平面**允许我们将软件技术应用于旧的网络问题。我们用 Python 或 C 之类的高级编程语言编写控制平面，数据平面是遵循 OpenFlow 控制命令来操作交换机的可编程硬件。

我们可以将 sdn 应用于数据中心、骨干网络、企业网络(在这里更容易编写安全协议)、互联网交换点和家庭网络。我们将关注前 3 个。

## 数据中心中的 sdn

数据中心由服务器机架组成(多达 20k 台服务器/集群)。如果每台服务器可以运行 400 台虚拟机，那么一个集群中就有 40 万台虚拟机。为响应负载而配置/迁移这些机器是一个问题。因此，解决方案是从中央数据库对开关状态进行编程。

我们还让服务器采用第 2 层“平面”寻址，这样服务器就可以迁移到数据中心的另一个部分，而无需虚拟机获取新地址，只需更新交换机状态即可，这要容易得多。

## SDNs 与互联网骨干网安全

目标是过滤来自 DDoS 攻击者的攻击流量。如果检测到高流量，控制器(如 RCP)可能会安装一个“空路由”,以确保不会再有来自攻击者的流量到达受害者。

## 挑战 sdn

1.  可扩展性:如何扩展超过 1000 台交换机？
2.  一致性:确保不同的副本看到相同的视图
3.  安全性/健壮性:如何从失败/妥协中恢复？

关于 OpenFlow、Nicira 及其创始人 Martin Casado 的更多信息。

## 不同的 SDN 控制器

一些选项

*   [氮氧化物](https://searchsdn.techtarget.com/definition/NOX)
*   支持 OpenStack 和 1.2+版本 OpenFlow 的 Python 控制器
*   [泛光灯](http://www.projectfloodlight.org/floodlight/) -一个 Java 控制器

高级控制器

*   [狂热/狂热](http://frenetic-lang.org/pyretic/)
*   [过程](https://www.sdxcentral.com/listings/procera-networks/)
*   [RouteFlow](https://www.sdxcentral.com/listings/routeflow-project/)
*   [特雷马](https://trema.github.io/trema/)
*   等等。

## Nox

这是第一代 OpenFlow 控制器。它是[开源](https://github.com/noxrepo?tab=overview&from=2018-03-01&to=2018-03-31)，稳定，应用广泛。NOX 架构有一组交换机和一组网络连接服务器。

Nox 支持的基本抽象是开关控制抽象，其中 OpenFlow 是主流协议。控制由 OpenFlow 规范中定义的数据包报头定义，它具有:

*   标题( [10 元组](https://www.sdxcentral.com/articles/contributed/sdn-openflow-tcam-need-to-know/2012/07/))
*   计数器:该流量的统计数据
*   操作:例如转发、丢弃、发送到控制器

当交换机收到数据包时，它会:

*   更新该流的计数器
*   应用相应的操作

NOX 是基于事件的。NOX 控制器的基本编程接口，知道如何处理不同类型的事件，例如(交换机加入/离开，或数据包进入/接收事件)。控制器还保持网络视图，包括底层拓扑的视图，并且还向网络中的交换机说出控制协议，允许它更新它们的状态。

NOX 提供了良好的性能，但需要使用低级 OpenFlow 命令和 C++。POX 是一个使用 Python 的 fork，级别更高，性能不太好。

## 我们系列的下一个

希望这是对软件定义的网络如何工作的一个很好的概括。我正在计划更多的初级读本，希望您能就以下方面提供反馈和问题:

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