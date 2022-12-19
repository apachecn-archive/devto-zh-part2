# 区块链- Hyperledger 结构-高级概述

> 原文：<https://dev.to/sr_balaji/blockchain---hyperledger-fabric---a-high-level-overview--22g4>

[![alt text](../Images/237bef4eac44d0270031e02d3106a3cb.png "Hyperledger fabric")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZF2-iJx5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zbgmg51cjdc0ka21k8is.png)

### 简介:

最近一段时间，区块链已经成为热门话题。在这篇文章中，我试图涵盖区块链/分布式账本以及它如何帮助我们的非常高层次的概述。我还概述了 Hyperledger fabric 区块链组件。

### 什么是区块链？

区块链没有单一的定义。在我看来，区块链是一个不可变的分布式账本，记录去中心化环境下的交易。这是自成立以来所有交易的有序列表。有不同类型区块链，如公共(无许可)和许可。在本文中，我讨论了权限区块链 Hyperledger Fabric。

### 什么是分布式台账？

它是网络参与者之间共享、复制和同步的一组记录。它记录了所有的交易，如网络参与者之间的数据或资产交换。

[![alt text](../Images/cb9a4e7585bb6961933ccbe53f5f1832.png "current state of transaction")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3qxJYiRO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gqujhnb98uv3mhp6s8y5.png)

### 问题陈述:

一个企业有不同的参与者，每个参与者都有自己的分类账副本。交易是双向的，每个参与者都必须相互交流，从而形成一个复杂的网络。交易由一个需要多重批准的中央机构管理。这是一项耗时耗钱的任务。

#### 信任:

在一个网络中建立信任是非常困难的，而且以信誉为主题是很费时间的。知名的管理机构需要时间来建立信任。

#### 透明度:

因为网络中的每个参与者都有自己的不共享的分类账，所以网络中没有透明性。

#### 问责:

银行、票据交换所、证券交易所、法律服务等监管机构需要作为中间人来确保交易的可问责性。

### 区块链有什么帮助？

区块链是一个防篡改的不可变分布式账本，它记录了网络中的所有交易。该分类帐由所有参与者维护，并且分布在对等网络中。区块链不信任第三方或中央政府，而是使用共识协议将交易提交到分类账中。交易的完整性是通过加密哈希和数字签名实现的。

### 区块链是如何工作的？

在分布式对等网络中，交易存储在链接在一起形成链的块中，这被称为块链。每个块包含当前块的散列、最近有效事务的时间戳和前一个块的散列。以前的块散列用于链接块，并防止改变块或在块之间插入。

[![alt text](../Images/3f9ba7cd5b6cc737335059480c3dee43.png "Blockchain state of transaction")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1oC8GWBM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8gp3cx8mhaww98ylpv6m.png)

### Hyperledger–Linux 基础项目

Hyperledger 是一个由 Linux 基金会托管的开源软件，由合作者公开管理，旨在推进跨行业的商业区块链技术。Hyperledger Fabric 是一个区块链框架实现，它是 Hyperledger 项目之一。

织物的一些关键特征:

*   没有象征性许可的区块链
*   基于共识
*   交易批准的背书政策
*   共享机密信息的私人渠道

#### 总账结构的组成部分

##### 同行

这些是维护分类帐的网络服务。它接收用于将新交易提交到分类帐并运行智能联系人的有序更新消息。

##### 提交同行

它提交事务并维护分类帐& state。

##### 背书同行

它接收用于背书的交易，并验证该交易是否满足所有必要和充分的条件。因此，认可对等体通过同意或拒绝认可来响应。

##### 订购服务或订购者

它批准将块包含到分类帐中。它与同行和认可同行进行交流。它为客户端和对等端提供了一个共享的通信通道，通过该通道可以广播事务。

##### 通道

这些是共享单个分类帐的对等网络的子网。它用于限制参与方对交易的访问。这意味着客户端只能看到它们所连接的通道的消息及其相关事务，而不知道其他通道。

##### 证书颁发机构

它为网络上的参与者提供身份服务。它管理运行区块链所需的不同类型的证书。

##### 智能合约

它是运行在每个调用调用上的事务逻辑。事务调用导致更新或查询分类帐状态。

##### 共识

这是在对等网络上获得协议的过程。它负责一致地复制分类帐并就新块达成一致。

##### 共享台账

它是网络上所有交易的不变记录。它是网络上所有参与者都可以访问的记录的集合。

##### 客户端

它是一个终端用户应用程序，必须通过对等体连接到区块链。

#### 超帐架构 v1

[![alt here](../Images/a053d5e3bbb747eb6ae9e1d5a41d34fb.png "Hypeledger fabric architecture")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o9BHGJjf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1m5f3tpu0dkpuluvirh6.png)

#### 它是如何工作的？

客户端应用程序/ SDK 通过定位所需的对等点来提交链码的交易建议。所有背书者将执行交易。这些交易不会在分类帐中更新，因为分类帐仅用于背书。背书完成后，由背书人签字并返还给客户。然后，客户将交易提交给订购者。然后，订购服务收集块中的事务，并将其分发给提交的对等方。这些承诺的对等点然后使用流言蜚语传递给其他对等点。有不同的排序算法可用，如索罗(单节点，发展)，卡夫卡，SBFT。

[![alt here](../Images/282c4a99e464aaf66d940d8c81636829.png "Hyperledger fabric - transaction processing")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5e9rJ3go--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5c6c1fm5zkevo9vpnc9s.png)

提交对等方根据认可策略验证事务，并检查事务对于当前状态是否有效。在所有这些过程之后，交易被记入分类账。当交易成功或失败时，客户端应用程序会得到通知，当块被添加到分类帐中时(如果它们为通知进行了注册)。客户端应用程序还将被它们所连接到的每个对等体通知。

### 结论

我已经概述了什么是区块链以及 Hyperledger fabric 的高级视图。这种方法可用于各种业务领域，如金融、供应链、贸易等。