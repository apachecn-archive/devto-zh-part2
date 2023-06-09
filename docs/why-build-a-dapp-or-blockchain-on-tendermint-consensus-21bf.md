# 为什么要在嫩薄荷共识上建立一个 dapp 或区块链？

> 原文：<https://dev.to/smn/why-build-a-dapp-or-blockchain-on-tendermint-consensus-21bf>

[![Tendermint building blocks](img/2d4d2c650cfd59da37fb01773f837056.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--osMWs7y3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ckz4f9fb3wlivtda5mgl.png)

人们一直问我，为什么我决定在 Tendermint 共识上建立我们的 [Egeon](http://egeon.io) 区块链，而不是其他区块链框架，比如 Hyperledger Fabric/Sawtooth，甚至以太坊/EOS 智能合约。所以这里是我对[嫩薄荷](https://tendermint.com/)的支持:

1.  嫩薄荷很快。我的意思是非常快:我的测试显示每秒钟有数千笔交易。这是可能的，因为它不使用工作证明，而是实现了一个 [BFT 共识](https://medium.com/loom-network/understanding-blockchain-fundamentals-part-1-byzantine-fault-tolerance-245f46fe8419)——简单地说，就是当 2/3+的节点就分类帐的状态达成一致。节点是那些运行 Tendermint 软件的服务器。

2.  嫩薄荷非常柔韧。你可以用任何编程语言扩展它:Go、JavaScript、Java、C#、Python，你说得出的(感谢 [gRPC](https://grpc.io/docs/guides/) )。这允许您创建一个特定于业务任务的区块链来满足您的所有需求和要求。您可以用编写应用程序相同编程语言轻松开发 Tendermint。不用说这种方法的优点。在我的例子中，我用 Node.js 编程。

3.  Tendermint 是整个宇宙网络项目----区块链的互联网----的一个主要构件。他们在 Tendermint 实施了分散的 Cosmos 中心。这个中心是所有区块链人通过 Tendermint 编写的侧链互相交流的地方。该中心将允许硬币从一个区块链转移到另一个。如果你的软件是在 Tendermint 或 [Cosmos-SDK](https://github.com/cosmos/cosmos-sdk) 上编写的，那么将你的 dapp 或区块链集成到整个区块链互联网世界将会非常容易——Tendermint 是 tender mint 的扩展，增加了堆叠、硬币支持和其他有用的东西。

4.  Tendermint 已经在 Hyperledger Burrow 中用作共识引擎——这是以太坊智能合约引擎(EVM)的替代实现。并且 [Hyperledger Burrow](https://www.hyperledger.org/projects/hyperledger-burrow) 被用作 Hyperledger 锯齿的智能契约引擎(虚拟机)——Hyperledger 家族中的另一个区块链框架。Tendermint 上有更多的产品正在开发，请看[这个列表](https://forum.cosmos.network/t/list-of-projects-in-cosmos-tendermint-ecosystem/243)。

* * *

## 如果您发现本文有价值，请点击❤️和🦄这样其他人就可以找到这篇文章

* * *

### 在[推特](https://twitter.com/AlexSiman)和 [LinkedIn](https://www.linkedin.com/in/alexsiman/) 上找我