# 智能合约错误&保护智能合约的最佳方式

> 原文：<https://dev.to/dianamaltseva8/what-are-smart-contract-bugs-and-how-to-protect-against-them-1j96>

当开始智能合约开发时，许多程序员强烈认为学习 Solidity 需要不到两周的时间，特别是如果他们知道 JavaScript 并有使用它的经验。

虽然 [坚固度](https://solidity.readthedocs.io/en/v0.4.21/) 相当简单，但专门保证智能合同安全的公司，要求巨额资金用于智能合同审计。

此外，有时即使是大公司和有经验的开发人员也会犯错误，导致数百万美元的损失。你可能听说过攻击者拿走 5000 万美元的 [刀。](https://www.wired.com/2016/06/50-million-hack-just-showed-dao-human/) 那么，它为什么会发生呢？

不幸的是，智能合约有一些漏洞，这使得使用保护工具和智能合约审计非常重要。在这篇文章中，我将考虑主要类型的错误以及可以用来提供智能合约安全性的技术。

### 智能合同漏洞

**交易排序依赖(TOD)**

矿工控制交易的顺序，这意味着您的交易可以通过在另一个中支付更多的汽油来超越(汽油量越高，您的交易对矿工的优先级越高)。

只有关闭块的矿工决定事务顺序，这就是漏洞，叫做事务顺序依赖。在 TOD 中，你可能会面临矿工意想不到的恶意行为。

**时间戳依赖性**

不幸的是，时间戳可能被矿工操纵，而矿工并不总是用户的朋友。矿工可以调整几秒钟提供的时间戳，从而改变合同的输出，使其对自己有利。

它可以按如下方式工作:当智能合同使用时间戳来生成随机数时，矿工可以在块被验证后的 30 秒内张贴时间戳，从而能够预测他在这次抽奖中的机会的更优选选项。

所以，要知道块的 [时间戳可以被矿工](https://ethereum.stackexchange.com/questions/15047/solidity-timestamp-dependency-is-it-possible-to-do-safely) 操纵，时间戳的所有直接和间接使用都要考虑。块号和平均块时间可用于估计时间，但这不是未来的证据，因为块时间可能会改变(如 Casper 期间预期的变化)。

**重入**

可重入性漏洞的第一个版本涉及到在第一次函数调用结束之前可能被重复调用的函数。因此，不同的函数调用可能会以破坏性的方式相互作用。看看下面的代码:

*//不安全
映射(address = > uint)私有用户余额；*

*函数 withdraw balance()public {
uint amountToWithdraw = user balances[msg . sender]；
如果(！(msg . sender . call . value(amountToWithdraw)())){ throw；} //此时调用方的代码被执行，可以再次调用 withdraw balance
user balances[msg . sender]= 0；
}
]*

了解更多关于其他 **[智能合约漏洞以及提供智能合约安全和保护的方法。](https://smartym.pro/blog/smart-contract-security-issues-smart-contract-vulnerabilities-and-how-to-protect/)T6】**

另外，请查看 **[智能合同审计最佳工具和实践，它们有助于交付最高质量的代码。](https://smartym.pro/blog/developing-smart-contracts-smart-contract-audit-best-practices/%22)T6】**