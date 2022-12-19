# 网络基础:路由

> 原文：<https://dev.to/swyx/networking-essentials-routing-5gb7>

*这是我参加[免费的 Udacity 计算机网络基础课程](https://www.udacity.com/course/computer-networking--ud436)的一系列课堂笔记中的第三篇。*

[正如我们在第一篇文章](https://dev.to/swyx/networking-essentials-architecture-and-principles-2g5e)中所确立的，互联网不是一个东西，它是由一组分散的网络组成的(从康卡斯特这样的 ISP 到谷歌这样的搜索服务器，再到大学这样的内容服务器)。这些更正式的名称是自治系统。当您通过互联网请求内容时，数据会通过多个 as 流向您。更准确地说，存在域内路由(在您的 AS 内)和域间路由(在 AS 之间)。正如我们在第二篇文章中讨论的，建立域是交换机[的功能。](https://dev.to/swyx/networking-essentials-switching-3eba)

## AS 内拓扑

节点被称为**存在点**，并且靠近主要的人口中心。这里是众所周知的阿比林网络:

[![https://cs.stanford.edu/people/eroberts/courses/soco/projects/2003-04/internet-2/images/domesticpeer.gif](../Images/da8a9702170ed59ea2b41b8cd813d648.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wRG_QABt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cs.stanford.edu/people/eroberts/courses/soco/projects/2003-04/internet-2/images/domesticpeer.gif)

有两种域内路由算法。

**距离矢量**

在距离矢量路由协议的“纯”实现中，路由到各个节点的开销就是它们之间的跳数。像 BGP(稍后解释)这样的高级协议也包括业务关系，并且被称为**路径向量**协议。

基于贝尔曼-福特算法(Bellman-Ford algorithm)的基础上，路由器会向邻居发送自己的路由表副本(向量)，并根据最短路径计算到达每个目的地的成本。因为邻居的每条最短路径都是已知的，所以路由器只需将自己的路由表成本加到它试图到达的邻居的路由表成本上。

这是一个非常简单的模型，当距离成本很低时更新很快，但是当失败发生时(无论什么原因距离成本增加)，坏消息传播很慢。成本的急剧增加导致邻居彼此来回 ping，增加了他们对最低成本邻居的估计，直到他们找到全局稳定的新平衡。这就是[计数到无穷大的问题](http://www.ques10.com/p/3796/what-is-count-to-infinity-problem-in-distance-vect/)。

解决办法是[毒逆转](https://searchnetworking.techtarget.com/definition/poison-reverse)。我们通过在我们绝对不想重新访问的链路中引入“无穷大”成本来建立路由表中的不对称性，从而避免了无用的 pingbacks。

**[路由信息协议](https://en.wikipedia.org/wiki/Routing_Information_Protocol)** 就是这个的一个实现。具体规则是:

*   如果需要 16 跳或更多跳，我们将把链接设置为无穷大。(故意小值)
*   表格每 30 秒刷新一次
*   每一轮都有 180 秒的暂停时间
*   **水平分割规则**:当更新发生时，发送给除更新源之外的所有邻居

RIP 最终只是计数到无穷大问题的短路，导致**收敛**；要真正解决这个问题，我们必须探索一种不同的范式。

**链接状态**

链路状态路由的思想是每个节点分发**网络图**而不是路由表。每个节点使用 [Dijkstra](https://en.wikipedia.org/wiki/Dijkstra%27s_algorithm) 计算自己的最短路径。

从机械角度看，这看起来像:

*   添加近邻的成本
*   邻居的洪水成本

这有两种实现方式:

*   **首先打开最短路径(OSPF)**
*   Int。系统整数。系统(IS-IS) (比较流行)

这是当今流行的路由算法，但它的一个问题是规模。协议的复杂性在于 O(n^3).

为了解决这个规模问题，我们引入了**层次结构**。OSPF 使用“面积”,而 IS-IS 使用“级别”。

例如:OSPF 有一个主干区域称为**区域 0** ，其他每个区域都有一个指定的“区域 0 路由器”。区域 0 路由器在它们之间进行计算，所有其它路由器在它们的区域和它们的区域 0 路由器之间进行计算。基本上，这就像我们已经探讨过的网络主题中的域如何工作一样。

## AS 间拓扑

1000 个 as 中有 10 个，因此它们需要通过使用**边界网关协议** (BGP)发送**路由广告**(或“公告”)来告诉彼此它们的存在。

在**外部 BGP** 中，一条路由广告包含大量数据，但关键是:

*   **目的地前缀**(如大学的 IP 前缀)
*   **下一跳 IP** -原始路由器必须将流量发送到的下一个路由器的地址(通常是相邻网络中的第一个路由器——可行，因为该网络的边界路由器与原始网络的边界路由器共享相同的子网，例如 [/30 子网](https://www.petri.com/how-30-and-32-bit-ip-subnet-masks-can-help-with-cisco-networking)
*   **AS 路径** -描述到达目的地的路径的 AS 编号序列。as 路径上的最后一个数字称为源 AS，它是路由通告的源。

一旦进入 AS 内部，**内部 BGP (iBGP)** 用于将 AS 内部的*路由传输到**外部**目的地。这不要与**域内路由协议(IGP)** 相混淆，后者在*内部传输路由*，至于**内部**目的地。*

从 AS1 内部的起点到 AS2 的目的地:

*   AS1 通过 **eBGP** 学习到 AS2 的路由
*   AS1 内部的路由器通过 **iBGP** 学习到 AS2 的路由
*   源路由器需要使用 **IGP** 到达 AS1 内部的 **iBGP 下一跳**地址。

**BGP 路由选择**

在通往同一目的地的多条路线之间进行选择:

1.  偏好更高的**本地偏好**
2.  最短 AS 路径长度(穿越的 AS 越少越好)
3.  多出口鉴别器(MED) -一个 AS 告诉你，它的多个可能的出口到你的目的地，这是首选。不同 as 间的 MED 值不具有可比性。
4.  最短的 IGP 路径——导致“烫手山芋路由”,在这种情况下，您基本上希望在自己的网络中花费尽可能少的时间
5.  基于稳定性或年龄或具有最低路由器 ID 的路由器的任意平局决胜。

**本地首选项**只是一个系统管理员可以分配给特定路由的号码。它是本地的，所以不会传输到任何地方，但是它允许系统管理员明确地声明一个路由应该优先于其他路由。对于配置主路由和备用路由非常有用。虽然本地首选项是本地的，但是 as 可以将 **BGP 社区**附加到它们的广告上，这样其他 as 就可以基于之前的协议配置它们的本地首选项来与它们一起工作。

**多出口鉴别器**

MED 覆盖了“烫手山芋路由”，允许 AS 明确指定相邻 AS 通过其自己的网络承载流量*，而不是在最近的出口丢弃流量，并强制流量通过邻居的主干。这不是一个友好的举动，这是一种防止一个 AS 搭另一个 AS 主干网的顺风车的方式，例如，当一个传输提供商与一个内容提供商对等并且不希望内容提供商获得免费传输时。*

## 域间路由商业模式

**这一切都是为了赚钱。**有两种类型的关系:

*   客户-提供商:资金从客户流向提供商，与流量流向无关
*   对等:一个 AS 可以免费与另一个 AS 交换流量(也称为“免结算对等”)

所以 as 总是倾向于:

1.  通过它的**客户**路由流量，因为它赚钱
2.  通过**对等点**路由，因为它是免费的
3.  最后一个选择，通过它的**提供者**路由，因为这要花钱

**过滤/导出**

假设一个 AS 从它的邻居那里获知了一条路由，它应该向谁重新通告这条路由？

*   来自客户的路由应该向每个人公布，因为 AS 通过这种方式赚钱
*   来自一个提供商的路线应该只对客户做广告，这样你就不会损失金钱(例如，没有从一个提供商到另一个提供商的路线，你将是两个提供商之间的中转提供商，并向他们支付费用)

根据 [Griffin、Shepherd 和 Wilfong 的安全论文](https://people.eecs.berkeley.edu/~istoica/classes/cs268/05/papers/GSW02.pdf)，路由稳定性从这些确切的规则中得到保证；没有它们，路线可能会无休止地摆动。想象一下，在石头剪刀布的游戏中，3 个具有本地偏好的 as(Varadhan Gorindam，Estrin (1996))。像区域对等和付费对等这样的业务关系可能违反这些条件。

关于这个主题的更多信息可以在这本初级读本中找到:[ISP 网络中的 BGP 路由策略](https://www.cs.princeton.edu/~jrex/papers/policies.pdf)

## 我们系列的下一个

希望这是对路由协议和 BGP 的一个很好的高层次概述。我正在计划更多的初级读本，希望您能就以下方面提供反馈和问题:

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