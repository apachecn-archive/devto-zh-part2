# 智能合同审计最佳工具和实践

> 原文：<https://dev.to/dianamaltseva8/smart-contract-audit-best-tools-and-practices-1bih>

构建以太坊项目时，关注智能合约安全性和代码质量。否则，你可能会损失巨大的金钱。例如，平价以太坊钱包因为代码漏洞损失了 3000 万美元。

为此，Satoshi Pie fond 损失了 32.4%的资产。2016 年，攻击者黑了 DAO，拿走了 5000 万美元。

这些例子清楚地表明，即使是智能契约逻辑中的一个微小错误或缺陷也会导致糟糕的结果。这就是为什么智能合同审计是智能合同开发的重要组成部分。

这个阶段的目标是识别代码错误并检查程序逻辑。一个高质量的深思熟虑的审计会让你消除风险，焦虑，节省你的神经和资源。

了解 [如何开发智能合同](https://smartym.pro/blog/ethereum-blockchain-platform-delving-into-smart-contract-development/) 然后 [准备智能合同审计](https://smartym.pro/blog/developing-smart-contracts-smart-contract-audit-best-practices/) 。

### 智能合同审计流程

智能合同审计的过程类似于任何其他代码的测试:在预定义的环境中创建一组标准方法调用，并为它们的结果编写语句。审计是一个复杂的过程，包括:

**测试开发**

*   智能合同状态更改的测试
*   事件测试
*   误差测试
*   检查邮件的发件人

虽然在 [Solidity](https://solidity.readthedocs.io/en/develop/) 中的测试开发受限于这种编程语言的能力，但在我们的工作中，我们也使用 JavaScript、Truffle 框架、奇偶校验以及其他成熟的技术。

### 使用 Truffle 进行智能合约测试

现在 [松露](https://github.com/trufflesuite/truffle) 是以太坊最流行的框架。

Truffle 是一个 Node.js 框架，用于编译、链接和部署智能合约。该框架以完全模块化的方式编写，使工程师能够选择他们需要的功能。

在 Truffle v.2 中，测试是在 JavaScript 上创建的，而在 Truffle v.3 中，开发人员增加了在 Solidity 上编写测试的能力，其语法类似于 JavaScript。

Truffle 提供了许多有用的功能，包括二进制管理、库链接、定制部署支持、迁移框架、可脚本化部署、访问数百个外部包、外部脚本运行器以及使用 Mocha 和 Chai 的自动化合同审计。

考虑到区块链系统的工作速度不是很快，审计人员使用区块链测试客户端，例如 TestRPC，它几乎完全模拟了以太坊客户端的 [JSON RPC API](https://github.com/trufflesuite/truffle) 的工作。

除了标准方法， [TestRPC](https://github.com/ethereum/wiki/wiki/JSON-RPC) 还实现了许多附加方法，如 *evm_increaseTime* 和 *evm_mine* 。

应用 TestRPC 的一个很好的替代方法是使用一个标准的客户机，例如，Parity，它运行在 dev 模式下，可以立即确认事务。

**要开始使用 Truffle 框架，请通过 npm 安装它:**

*npm 安装-g 块菌*

然后，执行 truffle init 命令来创建项目结构:

*$ mkdir solidity-test-example*
*$ CD solidity-test-example/*
*$ truffle init*

合同必须位于合同/目录中，测试必须位于测试/目录中。当你编译契约时，Truffle 期望每个契约都放在一个单独的文件中，并且契约名等于文件名。

学习 **[如何在 Truffle 中开发测试，如何审计智能合约中的事件和变更，以及其他有用的测试工具。](https://smartym.pro/blog/developing-smart-contracts-smart-contract-audit-best-practices/)T6】**