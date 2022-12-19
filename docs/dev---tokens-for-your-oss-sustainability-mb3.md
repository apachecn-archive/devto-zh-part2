# “Dev”——OSS 可持续发展的象征

> 原文：<https://dev.to/aggre/dev---tokens-for-your-oss-sustainability-mb3>

我们已经发布了“开发”令牌，以支持开源软件(OSS)的可持续性。

[https://devtoken.rocks/](https://devtoken.rocks/)

### “Dev”是什么？

“Dev”致力于实现您的 OSS 的公平货币化。

既不需要佣金，也不需要使用费。

利用代币经济可以让开发者专注于开发，让投资者有效地支持任何操作系统。

* * *

在大多数情况下，开放源码软件是免费提供的。OSS 开发者通过获得独立于他们的 OSS 的收入来源来维护他们的 OSS 项目。

典型的收入来源如下。

1.  在一家公司工作
2.  当自由职业者
3.  捐款
4.  开放源码软件作为 SaaS 提供
5.  企业支持

由于[1]和[2]不是 OSS 本身产生的收入，OSS 只被分配了有限的可支配时间。

由于高评估的任意模型，[3]的收入往往是偏离其
OSS 价值的金额。OSS 开发者被要求将他们的努力集中在技术以外的领域，这样他们就可以继续提升他们的评价。
。

[4]和[5]本质上是直接从 OSS 实现货币化，但这只能通过少数有限的大型项目来实现。

“Dev”致力于实现与 OSS 的实际价值成比例的收入。另外，它不需要改变 OSS 本身。有了这个
松耦合的系统，我们用简单而强大的评估
逻辑评估 OSS，并根据该评估实现货币化。

### “开发”如何工作

首先，可以用一个简单的图表来描述它。

[![Diagram](../Images/8fc010e61df03405b89702f1fe73b4ce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h3V82i4Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f1w5x2ovhx552ca9ue6w.png)

“Dev”是基于以太坊的生态系统打造的。如果一个人了解区块链，这很容易理解，但即使他们不了解也没关系。

“Dev”可以像以太坊或者比特币一样在市场上交易。由于“Dev”符合以太坊标准(ERC20)的
，1 DEV 的价格由 1 DEV 和 1 ETH 之间的比率
决定。

大纲如下。

1.  价格由交易“Dev”(实时更新)的投资者决定
2.  [devtoken.rocks](https://devtoken.rocks/) 每月铸造“Dev”
3.  增加的 [devtoken.rocks](https://devtoken.rocks/) 分配给 OSS

OSS 开发者可以通过用他们的“Dev”换以太坊来清算他们的“Dev”。

由于“Dev”是一个不断创造的符号，因此有一种机制来抑制“Dev”本身价值的
稀释。这涉及到评价逻辑。

### 评估逻辑为“Dev”

以下变量用于计算，以确定对
OSS 的分配:

```
pdl = Number of downloads of the package (OSS) from the 20th day of the last month to the 19th day of the current month
adl = Number of downloads of all packages (OSS) from the 20th day of the last month to the 19th day of the current month
t = Number of tokens retained by the distribution address
d = Number of days elapsed since the OSS registration
p = t / d
ta = Total of p for all addresses
m = Annual number of mints 
```

Enter fullscreen mode Exit fullscreen mode

计算公式如下。

```
x = (p + pdl) / (ta + adl) × m / 12 
```

Enter fullscreen mode Exit fullscreen mode

该 OSS 保留的“Dev”令牌的每日费率被添加到
OSS 下载次数中。单独计算每个 OSS 和所有
OSS 总数的相同等式，并确定每个 OSS 的比率。

分配数量由每月的
薄荷糖数量乘以该百分比确定。

添加 OSS 保留的令牌数是为了抑制“Dev”的
值的稀释。

OSS 拥有的令牌越多，每个
月分配的令牌就越多。也就是说，你可以通过购买或者
保存代币来增加股份数量。这将防止代币被一次性出售，突然
稀释价值。投资者不希望代币的价值被稀释。

作为这种机制的一个应用，如果你向你想要支持的操作系统发送“Dev”令牌，你可以增加分配给该操作系统的令牌数量。

计算逻辑可在[GitHub
(frame 00/dev-distribution)](https://github.com/frame00/dev-distribution)上查看。

#### 未来计划分配给贡献者

“Dev”目前在 alpha 中。令牌被分发给 OSS 的所有者。

我们希望实现向 OSS 贡献者分发令牌的能力。
贡献者将能够获得与其
贡献相关的报酬。

通过积极使用“开发”，我们将能够尽快推进功能性的
开发。

### 如何连接“Dev”

有很多方法可以让你参与到 OSS 的“开发”社区中。

#### 我想将我的操作系统货币化

如果你正在自助发布你的开放源码软件，你可以通过在 [devtoken.rocks](https://devtoken.rocks/) 注册你的开放源码软件来开始赚钱。

注册又快又简单，而且不需要对你的
操作系统做任何改动。

因为目前在 alpha 中，只有在 npm 中发布的操作系统才可以注册。

#### 我要支持 OSS

你可以通过使用“Dev”令牌为 OSS 做贡献。

为了实现一个操作系统得到更积极开发的世界,“开发”可以被有效地贡献出来。

可以从 [devtoken.rocks](https://devtoken.rocks/) 打开订单簿购买
和出售“Dev”以太网增量用于主要交换。

#### 有一个 OSS 我想直接支持

如果您想要支持的 OSS 已在“Dev”中注册，您可以将“Dev”
直接发送到 OSS 的钱包地址。

如果 OSS 保留的令牌数量增加，则 OSS 每月分发的数量
也会增加。这样，即使每个月没有新的贡献，你也可以提供
持续的支持。

参考[GitHub
(frame 00/dev-distribution)](https://github.com/frame00/dev-distribution)到
检查 OSS 的钱包地址。OSS 信息存储在
[config/packages.ts](https://github.com/frame00/dev-distribution/blob/master/config/packages.ts) 中。

* * *

开放源码软件开发者和投资者的积极使用加速了“开发”的增长。

如有疑问，请通过推特联系我们。询问可以用日语或英语进行。

* * *

本文原载于[媒体](https://medium.com/devtoken)。

点击阅读原文[。](https://medium.com/devtoken/dev-tokens-for-oss-a63e55c60e6b)