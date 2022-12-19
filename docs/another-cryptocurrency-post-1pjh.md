# 另一个加密货币帖子

> 原文：<https://dev.to/zeerorg/another-cryptocurrency-post-1pjh>

# 简介

在这篇文章中，我将分享一些加密货币的概念。这是一篇解释文章，所以我们不会深入代码，尽可能保持它的一般性。我们从存折的一个基本概念开始:当我们进行任何银行交易时，它都被记录在我们的[存折](https://en.wikipedia.org/wiki/Passbook)中，它保存了我们所有的支出和收入历史。
存折是简化总账的一种形式。我们从一个简单的例子开始。

# 两个朋友的例子

现在我们找两个朋友 A 和 B，他们想互相转账假钱，但是他们没有打印自己的钱，而是做了一件事，他们创建了一个共享的存折。这个共享的存折存储了他们两个人的记录。a 通过在他自己的版本中添加一条记录将钱转给 B，他告诉 B 他的交易，现在 B 也可以记录该交易。此共享记录是分布式分类账，是区块链的基础。

[![Shared ledger](../Images/2df8899882c0cc896aa5fc071c5f9b78.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RCdCF6yj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/zeerorg/WriteIt/master/ledger.png)

现在，越来越多的人想用这些假钱与他们交易，这样每个人都可以得到共享的存折。当有人转账时，他们会通知每个人他们已经转账了，这样每个人都会更新他们的账户。

### Q1:启动资金从哪里来？Ans，骰子的游戏。

最初，A 和 B 同意他们将玩骰子，谁得到的数字更大，谁就有 10 个假币。这也添加到分类帐中。他们同意每小时做一次，因此有人每小时收集 10 张假币。
后来，任何拥有账本的人都有权玩掷骰子的游戏。

这是一个基本的**加密货币挖掘**方案。如果很多人都在竞争，骰子游戏是不可行的，因此给他们一个问题来解决。谁先解决了问题，谁就得到钱。

这种结构的美妙之处在于，没有实际的实物票据或任何类型的资产交换，它只是一系列的交易。

### Q2:如果有人开始作弊怎么办？

假设 B 变得贪婪，想要额外的钱，于是他给自己添加了一个来自 A 的交易，并告诉所有人 A 已经这样做了。

为了防止这种情况，他们同意在交易中添加一个签名。就像支票上的签名一样，这可以证明交易是由这个人自己完成的。现在，B 不能复制 A 的签名，因此，他不能创建交易。每个节点在将交易添加到它们的分类帐之前检查签名。

### Q3:花钱不止一次！

这一次 B 变得很有创意，他在某个时间点有 10 种货币，他把它们都寄给了 A 和 C。他告诉每个人(除了 C)他把钱寄给了 A，他告诉 C 他把钱寄给了 C。C 认为交易成功了。

这就是双重支出的问题。为了防止重复消费，参与者需要一种方法来区分已确认和未确认的交易。这是通过区块链实现的。

[![Double Spending](../Images/511538964b2b2b76cd5b8278fae34bb3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zm2mjUwJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j80w5n9vg5xuv4wwtm8a.png)

### Q4:什么是区块链？

Block:它是事务的集合，包含前一个块的散列。(哈希是对应于一个块的唯一数字)

这类似于链表！其中每个块是一个节点，它的散列是一种引用。

每个方块对应一个需要解决的问题。这就是上一节讨论的挖掘问题。这个问题的答案是添加到下一个块的块的散列。在任何时间点都只能解决一个块。

*在一个块被解决后，它包含的交易被确认*。

现在，没有人可以改变交易，因为这将改变块，这将改变问题和问题的答案，引起连锁反应，其中下一个块将改变，等等。

[![Block chain](../Images/3a8c99814f20519500bd0b19691f0af5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NuJT9PlF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/zeerorg/WriteIt/master/blockchain.png)

## 附加阅读:

1.  我发现[比特币维基](https://en.bitcoin.it/wiki/Main_Page)是理解更多概念的绝佳资源。
2.  [比特币区块探索者](https://blockexplorer.com/)四处看看。