# 网络基础:流量工程

> 原文：<https://dev.to/swyx/networking-essentials-traffic-engineering-13c4>

*这是我学习[免费的 Udacity 计算机网络基础课程](https://www.udacity.com/course/computer-networking--ud436)的系列课堂笔记中的第十篇。*

* * *

这是关于网络操作和管理的三部分迷你系列的第二部分。

*   [软件定义的网络](https://dev.to/swyx/networking-essentials-software-defined-networking-35n9)
*   [交通工程](https://dev.to/swyx/networking-essentials-traffic-engineering-13c4)
*   [网络安全](https://dev.to/swyx/networking-essentials-network-security-1fcp)

* * *

## 什么是流量工程？

流量工程是网络运营商处理流经其网络的大量数据的方式。它们根据不断变化的流量负载重新配置网络，以实现一些操作目标，如:

*   对等关系中的流量比率(也称为“对等比率”)
*   缓解拥堵
*   更均匀地平衡负载

[软件定义网络](https://dev.to/swyx/networking-essentials-software-defined-networking-35n9)用于简化**数据中心网络**和**传输网络**的流量工程。

## 网络不自我管理吗？

尽管我们已经介绍了 [TCP](https://dev.to/swyx/networking-essentials-congestion-control-26n2) 和[路由](https://dev.to/swyx/networking-essentials-routing-5gb7/)如何管理自己(适应拥塞，或适应拓扑变化)，网络仍然可能无法高效运行**。未使用的空闲路径可能会造成不必要的拥塞。因此，流量工程师要解决的关键问题是:“路由应该如何适应流量？”**

 **## 域内流量工程

在标准网络拓扑中，每条链路都有一个与之关联的权重。一个非常简单的配置是根据你的优先级调整权重。例如:

*   链路权重与容量成反比
*   与传播延迟成比例的链路权重
*   其他一些基于流量的全网优化

## 流量工程的 3 个步骤

*   **测量**:计算当前的流量负荷
*   **模型**:配置如何影响网络中的路径
*   **控制**:重新配置网络以控制流量的流动

例如，您可以测量**拓扑**和**流量**，将它们输入预测性的“假设”模型，**针对目标函数**进行优化，生成您想要进行的更改，然后通过重新调整链路权重将这些更改反馈到网络中。

目标函数是这个过程中的一个重要决策。我们可以选择最小化网络中的最大拥塞链路，或者只是在链路之间平均分配流量负载，或者其他方式。

## 针对链路利用率目标进行优化

即使是一个简单的“拥堵成本”与拥堵的平方关系越来越密切的模型[也是一个 NP-完全问题](https://en.wikipedia.org/wiki/NP-completeness) -因此它在数学上是不可解的。相反，我们必须搜索大量的链接权重设置组合来找到一个好的设置。实际上，这很好。

我们的搜索还有其他限制，这减少了我们尝试的次数。例如，我们希望尽量减少对网络的更改。通常只有 1 或 2 个链接权重的变化就足够了。我们的解决方案还必须能够抵抗故障，并对测量噪声具有鲁棒性。

## 域间流量工程

回想一下，域间路由涉及发生在域或 as 之间的路由。(参见[我们对边界网关协议](https://dev.to/swyx/networking-essentials-routing-5gb7/)的讨论)。因此，域间流量工程涉及重新配置网络中各个边缘路由器上运行的 BGP 策略或配置。

注意:

*   在边缘上更改这些策略会导致网络内部的路由器将流量导向或导向远离某些边缘链路的地方。
*   我们还可以根据拥塞、链路质量的变化或对等协议的某种违反(如在某个时间窗口内超过约定的负载)来更改特定目的地的出口链路集

我们的行动源于我们对域间 TE 的目标:

*   **可预测性**(预测流量将如何随着网络配置的变化而变化)
    *   下游邻国可能会对我们的变化做出反应，这又是我们的问题
    *   所以我们不应该做出任何**全局可见的改变**
*   **限制邻域的影响**
    *   因此，我们应该制作**一致的路由广告**并限制 AS 路径长度的影响
*   **减少路由变更的过载**(即尽可能少地变更 IP 前缀)
    *   因此，我们根据具有共同 AS 路径的前缀对前缀进行分组，并通过分组的前缀移动流量

## 多路径路由

一种适用于域间和域内路由的技术是多路径路由，即通过多条路径路由流量。最简单的例子是在多路径上设置相等的权重，或者相等成本多路径(ECMP)。这将在这些路径上发送等量的流量。

源路由器还可以在路径上设置百分比权重，例如一条路径上的权重为 35%，另一条路径上的权重为 65%，它可能会根据观察到的拥塞情况来这样做！

## 数据中心联网

数据中心网络有三个特征:

*   [多租户](https://www.datacenterknowledge.com/archives/2014/08/07/four-big-advantages-multi-tenant-data-centers) -允许成本分摊，但也必须提供安全性和资源隔离
*   弹性资源-根据需求上下分配。允许按使用付费的商业模式。
*   灵活的服务管理-能够通过虚拟机迁移将工作负载移动到数据中心内的其他位置。

所以我们的需求也相应地发展。我们需要:

*   负载平衡流量
*   支持虚拟机迁移
*   省电
*   供应(当需求波动时)
*   提供安全保障

典型的数据中心拓扑有 3 层:

[![https://tse4.mm.bing.net/th?id=OIP.7ZfRQrajOY55baa9vvqPZwHaFj&pid=Api](../Images/744d214b485895f435eb63b52e2f76bb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cfHeq9zM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tse4.mm.bing.net/th%3Fid%3DOIP.7ZfRQrajOY55baa9vvqPZwHaFj%26pid%3DApi)

*   **访问**层连接到服务器本身
*   **聚合**层
*   **核心**层

核心层现在通常由第 2 层拓扑来完成，这使得迁移和负载平衡流量更容易，但更难扩展，因为我们现在在单个扁平拓扑上有 x0，000 台服务器。

这种层次结构还会产生单点故障，核心中的链路可能会被超额预订。在现实生活中，数据中心顶部的链路可以承载底部链路高达 200 倍的流量，因此存在容量不匹配的问题。

## 在接入层缩放

处理伸缩问题的一个有趣的方法是“pod”。

在接入层，每台机器都有一个独立的 MAC 地址。这意味着上一层中的每个交换机都需要为每个 MAC 地址存储一个转发表条目。解决方案是通过交换机将服务器组分配为“Pods”，并为它们分配“伪 MAC 地址”。因此，服务器只需要维护到达拓扑中的其他 pod 的条目。

[![pods](../Images/68488b4e741b3f17dad947fcf2addbe1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--00FZuTro--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.cisco.com/c/dam/en/us/products/collateral/switches/nexus-7000-series-switches/white-paper-c11-737022.docx/_jcr_content/renditions/white-paper-c11-737022_1.jpg)

## 跨数据中心的负载均衡

为了在数据中心的服务器之间平均分配流量，微软在 2009 年发明了 [Valiant 负载平衡](https://www.microsoft.com/en-us/research/publication/vl2-a-scalable-and-flexible-data-center-network/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D80693)。它通过在交换层次结构中插入一个“间接层”来实现平衡。该交换机被随机选择*——一旦被选中，它就完成了向目的地发送流量的工作。挑选随机交互点来平衡拓扑中的流量实际上来自多处理器体系结构，并且已经被重新发现用于数据中心。*

 *## 水母术

*在这里看[水母论文](https://www.usenix.org/system/files/conference/nsdi12/nsdi12-final82.pdf)T3】*

与 Valiant 类似，水母随机组网数据中心，以支持高吞吐量(例如大数据或虚拟机的灵活放置)和增量可扩展性(因此您可以轻松添加或更换服务器和交换机)。

数据中心的结构往往会限制扩展。例如，[超立方体](https://hellosemi.com/hypercube/pmwiki.php?n=Main.HypercubeDataCentre)需要`2^k`个交换机(`k` =服务器数量)。[胖树](https://en.wikipedia.org/wiki/Fat_tree)效率更高(O( `k^2`))但还是二次的。

这是一棵胖树，您可以看到顶层(访问)的拥塞情况:

[![https://2.bp.blogspot.com/-8uKSOcP-FDQ/T5ZhZpef-wI/AAAAAAAAAOE/lw-Pw0aMed4/s400/FatTree.png](../Images/48e0bd832d760e8405c047fcf625e5d1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1528C8xi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://2.bp.blogspot.com/-8uKSOcP-FDQ/T5ZhZpef-wI/AAAAAAAAAOE/lw-Pw0aMed4/s400/FatTree.png)

水母的拓扑是一个“随机正则图”——每个图都是从所有“正则”图的集合中统一随机选取的。“规则”图是指每个节点(在这个上下文中是一个开关)具有相同度数的图。

这是一只水母——有*没有*的结构对坚固性很有帮助！

[![https://2.bp.blogspot.com/-fES5sXO_rF4/T5Zhy7AeinI/AAAAAAAAAOQ/KMZ0g6eMv8s/s1600/Jellyfish.png](../Images/549cc092f2cd63370b2ea055df5eb252.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--S6AEn1we--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://2.bp.blogspot.com/-fES5sXO_rF4/T5Zhy7AeinI/AAAAAAAAAOQ/KMZ0g6eMv8s/s1600/Jellyfish.png)

## 我们系列的下一个

希望这是对流量工程如何工作的一个很好的高层次概述。我正在计划更多的初级读本，希望您能就以下方面提供反馈和问题:

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
*   [网络安全](https://dev.to/swyx/networking-essentials-network-security-1fcp)***