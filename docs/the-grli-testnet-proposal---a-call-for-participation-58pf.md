# 格利试验网提案——呼吁参与。

> 原文：<https://dev.to/5chdn/the-grli-testnet-proposal---a-call-for-participation-58pf>

在以太坊主网络上线之前，有许多以太坊测试网可用于试验智能合约和部署去中心化的应用程序。然而，没有一个测试网既能广泛用于所有客户机实现，又足够健壮以保证一致的可用性和高可靠性。

### 现有公共测试网

**奥林匹克**曾经是链`0`，在以太坊主网络的公共前沿发布之前的公共以太坊测试网。以太坊起源街区[是基于奥运网络的 1028201](https://blog.ethereum.org/2015/07/27/final-steps/) 街区模板。这个网络在 Frontier 发布后不久就退役了。

**现代**是作为新的公共工作验证测试网推出的，它是随着以太坊公共主网络的前沿发布而推出的。以太坊社区放弃了它，因为虚假的龙测试的[问题。然而，它仍然被以太坊经典社区使用，也被称为*现代经典*。](https://blog.ethereum.org/2016/11/20/from-morden-to-ropsten/)

Ropsten 是最后一个公开的工作验证测试网，也是目前为止最后一个跨不同客户端实现的测试网。是规格最接近以太坊主网的网络。由于稳定性问题，它大部分被放弃了，但仍然存在，因为这是在 Geth 和奇偶以太坊上测试契约和 dapps 的唯一方法。

作为对[垃圾邮件攻击](https://ethereum.stackexchange.com/questions/12477/ropsten-testnet-is-under-kind-of-attack-what-can-we-do)的回应，Kovan 是第一个权威证明测试网。一个由[开发者和服务提供商组成的财团加速利用 Parity 的 *Aura* 权威证明引擎来运行一个测试网。](https://medium.com/@Digix/announcing-kovan-a-stable-ethereum-public-testnet-10ac7cb6c85f)

Rinkeby 是第二个权威证明网络，也是为了应对 Ropsten 垃圾邮件攻击而推出的。然而，它没有使用 Aura，而是使用了 Geth 的*派系*权威证明引擎[，其规格](https://github.com/ethereum/EIPs/issues/225)有所不同。

许多其他具有各种规范的公共以太坊测试网是可用的，即 **Sokol** 测试网，然而，它们中没有一个是跨不同客户端可用的，同时也是高度可用和健壮的。

截止到今天，只有 Geth 实现了 Clique，只有宇称以太坊实现了 Aura。要运行一个跨客户端的 testnet，用户必须依赖 Ropsten 工作验证网络。然而，在没有价值的网络上进行工作证明挖掘很难受到激励，并且由此产生的低散列率使得潜在的攻击者能够容易地接管网络。

### 格利测试网提案

现在，那个 EIP-1011，*混合 Casper* 工作证明/利害关系证明测试网想法[已经被最新以太坊研究](https://medium.com/@VidrihMarko/roadmap-change-at-ethereum-sharding-and-casper-at-the-same-time-745f0587ae10)放弃，**格力**可能是下一代公共以太坊测试网络。

我们在此提议，

1.  在 EIP 中充分指定权威证明引擎，例如 Aura 或 Clique，
2.  要在不同的客户端上实现一个或多个这样的引擎，比如 Geth 和奇偶以太坊，
3.  并且基于模拟主要网络条件的可用实现来引导新的简化的**glri**权威证明测试网。

### 展望和路线图

ChainSafe 团队开始在#ETHBerlin 黑客马拉松上开发 Aura 端口来获得[，](https://twitter.com/ETHBerlin/status/1038803516511473664)[建议也将 Clique 移植到宇称以太坊](https://medium.com/@aidanhyman/chainsafe-systems-g%C3%B6rlitestnet-2583fa13107e)。这不是一个偏爱一种技术胜过另一种技术的政治建议；而是希望让开发人员拥有尽可能多的不同工具。

光环 EIP 正在 Github 上被起草。缺少的是光环链分数规则的细节，封印是如何组成的，以及终结是如何工作的。目前，在宇称以太坊团队的帮助下，艾丹和我正在研究 EIP。

Github 上的这个库[里准备了一个基本的 Aura 引擎。虽然集成引擎和测试网配置的工作已经完成，但在 Aura 共识机制的实际实现中仍有大量工作要做。目前，Elizabeth、David、Dustin 和 Tim 正在进行这项工作，但是他们希望工程师们能够更有信心在 Go-Ethereum 代码库中编写一致性关键代码。](https://github.com/goerli/go-ethereum-aura)

宇称以太坊默认支持 Aura，这里有一个[工作链规格的预测试网](https://github.com/goerli/testnet/blob/master/parity/goerli.json)。准备了两个[验证器节点和引导节点](http://ethstats.goerli.ethberl.in:3000)，以实现与 Geth 节点的无缝集成。

### 全体船员集合！

还有很多事情要做。以下是您可以提供帮助的方式:

*   加入 Gitter 上的聊天:[gitter.im/goerli/testnet](https://gitter.im/goerli/testnet)
*   运行一个节点:[github.com/goerli/testnet](https://github.com/goerli/testnet)
*   向仪表板报告:[eth stats . goer Li . eth berl . in](http://ethstats.goerli.ethberl.in:3000)
*   帮助光环规范: [eip-aura.md](https://github.com/goerli/eips-aura-spec/blob/master/EIPS/eip-aura.md) ，【gitter.im/goerli/eip-aura】T2
*   帮助 Go 实现:[Go-以太坊-aura/tree/aura-dev](https://github.com/goerli/go-ethereum-aura/tree/aura-dev) ，【gitter.im/goerli/geth-aura】T2

### 鸣谢

感谢 MP 和 [#ETHBerlin](https://ethberlin.com) 团队在世界上最令人惊叹的黑客马拉松上招待我们。

感谢来自[宇称以太坊](https://paritytech.io/ethereum/)的 Tomasz、André和 Thibaut 帮助分析权威回合规范。

感谢来自[以太坊基金会](https://ethereum.org/)的 Martin 帮助我们实现 Golang，并整晚调试我们的代码。

感谢 Kirill 和宇称以太坊为我们提供了一个节点仪表盘。

感谢 Aidan 和 [ChainSafe](https://chainsafe.io/) 团队来启动这个项目。