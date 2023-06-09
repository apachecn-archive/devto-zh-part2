# 区块链可扩展性问题。

> 原文：<https://dev.to/galyna_chekan/blockchain-scalability-issue-4ep5>

如果你一直在关注比特币/区块链的新闻，你可能听说过 SegWit、Lightning Network 和 SPECTRE(以及其他)这些术语，它们都是最近关于[区块链可扩展性问题](https://perfectial.com/blog/blockchain-scalability-issue/)的。

[![tacklin-blockchain-scalability-problem](img/e8a882eb5fdbf3a15c03d66e30c83434.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fuzg3N1a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://perfectial.com/wp-content/uploads/2018/07/blockchain-02.jpg)

在各种技术博客和密码相关的子网站上，并不缺少关于这个主题的帖子，如果你不熟悉区块链的内部工作方式和开发人员的行话，你可能会发现大多数帖子令人困惑。

今天，我们将试图提炼和淡化技术细节，用简单的英语阐明这个复杂的问题。我们将解释人们所说的扩展问题的含义，讨论其原因，并列出区块链网络试图解决这些问题的方法。

在我们继续之前，让我们快速回顾一下什么是区块，什么是采矿。

Blocks，[根据我们之前的文章](https://perfectial.com/blog/blockchain-transforms-finance-healthcare-music-industry/)，只是区块链用户提交的批量交易；捆绑在一起，它们按时间顺序附加到网络的分类帐中，每个都包含对前一个块的引用，从而形成一个链。

矿工的角色包括编译块并将它们写入网络的历史。然而，要获得这种特权，验证者还需要证明他们愿意投入一些努力——计算资源——来保护网络。

这就是我们所说的提供*工作证明*。

因此，在添加块之前，矿工们竞相解决一个数学难题——确定一个只是长随机数的加密随机数。第一个猜测它的验证者将写入不变的历史，然后由区块链奖励一定数量的加密货币。

在比特币上，网络会调整这些复杂数学问题的难度，这样验证者大约需要 10 分钟才能破解它们。这就是我们得到 10 分钟间隔时间的地方。

工作证明是用来保护区块链的。如您所见，这使得攻击网络变得非常困难，耗费时间和资源。

这些块的大小是有限的。在比特币上，历史上，它们不能超过 1MB(但现在不是这样)。它们在区块链上每 10 分钟出现一次，每次只包含大约 2000 个交易，这意味着网络每秒只能处理 3 个交易。该网络能够处理的交易数量有限，因此交易越积越多，造成了拥堵，并促使着急的用户支付高额费用，以便矿商提取他们的交易，并将其纳入一个区块。

如果你想了解更多关于[区块链技术，以及你的企业如何从中受益](http://blockchain.perfectial.com/) -这里有一本免费的电子书。

帖子[区块链可扩展性问题:为什么会有问题，可以做些什么？](https://perfectial.com/blog/blockchain-scalability-issue/)最早出现在[软件开发公司完美](https://perfectial.com)上。