# 可破坏转移模式

> 原文：<https://dev.to/rzadp/destructible-transfer-pattern-g5b>

如果你像我一样是一个 Solidity 开发人员，那么**退出契约**模式可能是你学习的第一个[通用模式](https://solidity.readthedocs.io/en/v0.4.24/common-patterns.html)之一。它可以保护您的合同在向另一个地址汇款时不会失败。然而，代价是引入了额外的步骤，这增加了复杂性，并且会干扰自动化过程。

☝️如果你不熟悉**退出合同**的模式，请继续阅读。否则，你可以跳到**可破坏转移**模式部分。

## 案例分析-工资系统

想象一下，你正在开发一个自动工资系统，通过以太网进行支付。所有困难的部分都完成了——剩下唯一要做的就是编写一个智能契约，通过发出一个事件来处理和记录以太坊区块链上的每一笔支付。这是你的初稿:

```
contract NaivePayroll {
    event Paid(address account, uint amount);

    function pay(address account) public payable {
        require(account.send(msg.value));
        emit Paid(account, msg.value);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 安全风险

上面的合同被命名为`NaivePayroll`是有原因的——它天真地假设接收地址要么是一个钱包，要么是一个不会做任何怪异事情的合同。考虑以下合同:

```
contract MaliciousReceiver {
    function() public payable {
        revert();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

未命名的函数是一个[后备函数](https://solidity.readthedocs.io/en/v0.4.24/contracts.html?#fallback-function)——每当有以太网转移到这个契约时，它就被执行。

💥如果`MaliciousReceiver`在工资单上，您的自动化系统将向区块链发送失败的交易。

### 解

你设计的自动化系统要么跳过这些账户，要么优雅地处理失败的交易。然而，这还不够——必须在区块链上记录支付意向，并且资金必须离开自动化系统的账户。

这个问题有两种解决方案:

*   退出合同模式
*   可破坏转移模式

* * *

## 退出合约模式

您可以使用**退出合同**模式快速更改您的`NaivePayroll`。这是你的代码:

```
contract BrokePayroll {
    mapping(address => uint) pending;
    event PayAdded(address account, uint amount);
    event PayWithdrawed(address account, uint amount);

    function pay(address account) public payable {
        pending[account] = pending[account] + msg.value;
        emit PayAdded(account, msg.value);
    }

    function withdrawPay() public {
        require(msg.sender.send(pending[msg.sender]));
        emit PayWithdrawed(msg.sender, pending[msg.sender]);
        pending[msg.sender] = 0;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 安全风险

还是那句话，合同的名字不是没有道理的。考虑以下合同:

```
contract GreedyReceiver {
    function getPaid(address _payroll) public {
        Payroll payroll = Payroll(_payroll);
        payroll.withdrawPay();
    }

    function() public payable {
        if (msg.sender.balance >= msg.value) {
            this.getPaid(msg.sender);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

💸如果贪婪的接受者在工资单上，它可以提取比预期更多的资金。

`GreedyReceiver`的[回退功能](https://solidity.readthedocs.io/en/v0.4.24/contracts.html?#fallback-function)一次又一次地启动取款，直到没有剩余资金，你破产为止。

☝️把这个问题称为**重入**问题

### 纠正撤单模式用法

可重入性的解决方案相当简单——您需要做的就是在发送资金之前，首先清除未决的取款。

```
contract WithdrawalPatternPayroll {
    mapping(address => uint) pending;
    event PayAdded(address account, uint amount);
    event PayWithdrawed(address account, uint amount);

    function pay(address account) public payable {
        pending[account] = pending[account] + msg.value;
        emit PayAdded(account, msg.value);
    }

    function withdrawPay() public {
        uint value = pending[msg.sender];
        pending[msg.sender] = 0;
        msg.sender.send(value);
        emit PayWithdrawed(msg.sender, value);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 可破坏的转移模式

使用**撤销合同**模式的替代方案是使用**可销毁转移**。
这个模式的核心是一个小而有趣的契约:

```
contract DestructibleTransfer {
    constructor(address payee) public payable {
        selfdestruct(payee);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`DestuctibleTransfer`合同充当用于进行支付的中介合同。构造时，它立即自毁，导致剩余资金被转移到给定地址。
`DestuctibleTransferPayroll`合同看起来是这样的:

```
contract DestuctibleTransferPayroll {
    event Paid(address account, uint amount);

    function pay(address account) public payable {
        (new DestructibleTransfer).value(msg.value)(account);
        emit Paid(account, msg.value);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`DestuctibleTransferPayroll`合同不容易受到`MaliciousReceiver`和`GreedyReceiver`的攻击。

### 为什么有效

从析构协定发起的传输不执行接收者的回退函数。
因此，回退功能代码中的失败或可重入对于我们利用**可破坏转移**模式的工资系统来说是不匹配的。

### 缺点

可破坏转移比普通转移消耗更多的气体。为了减少气体的使用，考虑仅在标准传输失败时使用可破坏传输。

```
contract SmartPayroll {
    event Paid(address account, uint amount);

    function pay(address account) public payable {
        if (!account.send(msg.value)) {
            (new DestructibleTransfer).value(msg.value)(account);
        }
        emit Paid(account, msg.value);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

在智能契约开发中，安全性非常重要，尤其是在处理以太存取时。每一个安全漏洞都玷污了社区对以太坊在现实世界中的适用性的信任。

我相信建立可靠的模式和最佳实践将提高以太坊区块链智能合约的总体质量，并希望我的贡献将有助于开发者满怀信心地编写安全的智能合约。