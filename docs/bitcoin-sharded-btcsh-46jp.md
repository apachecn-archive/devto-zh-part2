# 比特币碎片(BTCSH)

> 原文：<https://dev.to/shogochiai/bitcoin-sharded-btcsh-46jp>

怀着对比特币、BitcoinCash、Zilliqa、Thunderella 最大的敬意。

## TL；速度三角形定位法(dead reckoning)

*   Zilliqa 是分片+智能契约+用于分片确定 GPU 能力
*   所有的无权限区块链被分成三个属性:`Type of Sybil Attack Mitigation Cost + Data Structure for History Consensus + Scalability Trade-off`
*   $BTCSH 只是 Zilliqa 的克隆版，因为...
    *   用 DIY 哈希算法和 DIY ASIC 替代哈希算法
    *   关闭气体模块，并通过自己的构造减轻 DoS
    *   不太热衷于智能合约生态系统

## 规格

*   类型:无功率许可 pBFT(分片，33%暂停，66%恢复)
*   目的:价值储存，一般销售，审查抵抗，没有第二层
*   PoW algo:原装专用 FPGA ASIC(所有矿工必买)
*   块时间，大小:与 Zilliqa 相同
*   有限的硬币供应量(21000000)
*   不可变(不按社区还原)
*   没有 ICO 才能成为商品
*   智能契约:仅有限状态机、形式可验证、帐户模型和基于脚本(可能吗？)
*   从 Zilliqa 移除气体

## 顿悟

*   SoV，但无缝的交易体验
*   有用，但有网络层审查-阻力
*   无大块:添加 Nonce 的分块最终块传播
*   没有敌意的卖空:智能合同放弃设计
*   线性可扩展性
*   BTC 风味激励在商店使用(SoV 病毒)
*   也许比普通战俘的自私采矿更强(不需要矿坑，因为战俘不太频繁)...需要澄清
*   碎片鲁棒性:33%暂停，但 66%恢复。连锁停顿的条件是 BTC 相等，连锁崩溃的条件几乎是不可能的...需要澄清

## 上行

*   这种硬币是纯粹的商品，而不是像 ICO-ed 价值储存证券
*   Tx 没有气体
*   无通道开放:无缝体验，适合 BCH 等参考
*   没有更大的，相同的哈希算法链，如 Monacoin (20++高度重组 51%的攻击)
*   审查阻力提供不像 BCH 大块
*   没有像商品机那样[卖空和 51%攻击](https://ethresear.ch/t/adversarial-shortsell-probability-sum-dapps-price-sum-eth-price/1687)power 智能合约链

## 下行

*   BTC、BCH 具有安全/渠道网络效应
*   有点复杂
*   协议未经正式验证
*   根据 Zilliqa，重组很复杂
*   无需气体定制的 DoS 缓解
*   最终块(TxBlock)会变成千兆块吗？

## 非气体 DoS 缓解

[![img](img/2e8e44939d7c65a86daf67c222a35279.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bhO4E23R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9cutjn87v0choqe5cs44.jpg)
[![img](img/db67dad8ea61f1b4cfc4adcff981defa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BY4NV8m8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7qde4lf0pu0irlah2i2g.jpg)
[![img](img/3babb59757016f4f8ef335d83b3a269d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GxQtTjAm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7jwmyj3o72eo4rjpcmqs.jpg)

## 最后

*   你可能会说“不对，比特币有很强的网络效应”。
*   这个协议的价值天生就是“商品”。
    *   莱特币没有价值吗？不是。
*   商品不承诺任何东西，因此它是安全的。
*   金银都有。它们每一个都有不同的美和用法。商品发现工程不是“赢者通吃”。只是与他人分享新的自然资源/资产类别。