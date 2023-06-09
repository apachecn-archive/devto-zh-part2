# 没有区块链的加密货币？了解有关 DAG 的更多信息

> 原文：<https://dev.to/galyna_chekan/cryptocurrencies-without-blockchain-learn-more-about-dag-50hi>

事实证明，攀登区块链是一项艰巨而复杂的任务。正是因为这个原因，所有数字货币的先驱比特币多年来一直是一个停滞不前的低吞吐量网络。

目前，围绕加密领域有许多旨在增强比特币(和其他平台)TPS 的计划。一些专家提议增加块的大小，另一些专家希望减少网络上的处理时间。关于应该改变哪个变量存在大量争论，鉴于比特币社区对任何修改都非常反感，不知道这个问题何时会得到解决。

然而，一些项目提供了一种更激进的方法来解决区块链的问题:他们建立了完全不使用区块链数据结构的全新网络。IOTA 和 Byteball 等已经在加密领域掀起了波澜，它们将重新定义加密货币的托管方式。代替区块链，他们正在寻求实现一种叫做[有向无环图(DAG)](https://perfectial.com/blog/dag-vs-blockchain/) 的东西。

今天，我们将简要概述什么是 DAG，有向无环图如何工作，并详细描述 IOTA 和 Byteball 试图通过使用它来实现什么。

## 什么是[有向无环图](https://perfectial.com/blog/dag-vs-blockchain/)？

[![What is a DAG (Directed Acyclic Graph)?](img/feb014ff69dda4464e9e27d9ff3d82b0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lZmR8npT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://perfectial.com/wp-content/uploads/2018/03/img3-4.jpg)

就数据结构而言，区块链可以被认为是简单的链表。比特币或以太坊(或其他网络)上的每个条目都被放在它所引用的前一个条目之上。这就是我们如何得到数字事件的线性序列，我们称之为链。

区块链允许人们追踪分类帐历史中存储任何记录，但是它们的顺序结构也是显著阻碍它们的交易吞吐量的原因；区块链的扁平列表特性是其扩展能力的最大瓶颈。

嗯， [DAG 以不同的方式运行](https://en.wikipedia.org/wiki/Directed_acyclic_graph)。这种数据结构类似于一个流程图，其中所有点都指向一个方向。您可以将有向无环图(DAG)与文件目录结构进行比较，在文件目录结构中，文件夹包含分支到其他子文件夹的子文件夹，依此类推；它们像树一样。

无环这个词只是意味着图中没有节点可以引用回自己；不能是自己的母节点。

区块链 vs DAG(有向无环图)vs 字节球的帖子最早出现在[软件开发公司完美](https://perfectial.com)上。