# 以太坊开发:接口和函数修改器

> 原文：<https://dev.to/damcosset/ethereum-development-interfaces-and-function-modifiers-4e4f>

## 简介

在我的[上一篇文章](https://dev.to/damcosset/ethereum-development-getting-started-m09)中，我们通过使用 Truffle 和 Ganache 命令行开始了以太坊的开发。在本文中，我们将使用这个环境来探索 Solidity 中的两个重要概念:接口和函数修饰符。

## 设置事物

假设您已经安装了 Truffle 和 ganache-cli，创建一个新文件夹并在里面运行`truffle init`。如果你还没有这样做，请参考[上一篇文章](https://dev.to/damcosset/ethereum-development-getting-started-m09)。我们将在刚刚创建的*合同*文件夹*松露*中创建两个合同。我们的例子将非常简单。

*   答案. sol

```
pragma solidity ^0.4.18;

contract Answers {
    uint answerUniverse;

    function setAnswerUniverse(uint _answer) external {
        answerUniverse = _answer;
    }

    function getAnswerUniverse() public view returns (uint){
        return answerUniverse;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   问题. sol

```
pragma solidity ^0.4.18;

contract AnswersInterface {
    function getAnswerUniverse() public view returns (uint);
}

contract Questions {
    AnswersInterface answersContract;

    function setAnswersContractAddress(address _address) external{
        answersContract = AnswersInterface(_address);
    }

    function whatIsTheAnswerUniverse() public view returns (uint){
        uint answer = answersContract.getAnswerUniverse();
        return answer;
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

Answers.sol 契约非常简单。一个变量 *answerUniverse* 有一个 getter 和一个 setter。请注意，因为这很重要，setter 是*外部的*，这意味着它必须从契约外的**调用，getter 是公共的，这意味着每个人都可以调用它。**

Questions.sol 契约包含两件事:问题契约和 AnswersInterface 接口。那么，什么是接口呢？一个界面允许您与区块链上的另一个合同对话。如您所见，要定义一个接口，您可以像一个常规契约一样开始，使用关键字 *contract* 。在这个契约中，您只定义了您想要与之交互的函数，而没有它们的主体。这些函数需要是公共的或外部的，这样我们就可以从原始契约之外调用它们。当然，你不能与私有或内部函数交互。

在我们的问题契约中，我们在 *answersContract* 中创建了一个接口实例。函数*setanswerscontractdress*将告诉我们的契约在哪里寻找原始的答案契约。函数 *whatIsTheAnswerUniverse* 检索答案契约中的变量 *answerUniverse* 。

*注意:*当然，我们需要在检索变量之前设置契约的地址。否则，我们就不知道去区块链的哪里找了！

*   接下来，在 *truffle-config.js* 文件中:

```
module.exports = {
    networks: {
            development: {
                    host: '127.0.0.1',
                    port: 7545,
                    network_id: '*'
            }
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

对于 Windows 用户，您必须删除 truffle.js 文件以避免冲突。对于其他人来说，你可以两者都保留，然后把代码放在 truffle.js 中，或者像 Windows 用户一样，这并不重要。

*   接下来，在*迁移*文件夹中，创建两个文件: *2_deploy_questions.js* 和 *3_deploy_answers.js* 。

*   2_deploy_questions.js

```
var Questions = artifacts.require("Questions")

module.exports = function(deployer) {
    deployer.deploy(Questions)
}; 
```

Enter fullscreen mode Exit fullscreen mode

*   3 _ 部署 _ 答案. js

```
var Answers = artifacts.require("./Answers.sol")

module.exports = function(deployer) {
  deployer.deploy(Answers)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 脱臼 yyy

一切都准备好了。打开一个新的终端窗口并运行`ganache-cli -p 7545`。
返回到项目的文件夹并运行:

*   `truffle compile`
*   `truffle migrate --network development`
*   `truffle console --network development`

现在，我们可以玩我们的合同和我们的界面。首先，为每个合同创建一个实例:

```
truffle(development)> Questions.deployed().then(inst => Questions = inst)
truffle(development)> Answers.deployed().then(inst => Answers = inst) 
```

Enter fullscreen mode Exit fullscreen mode

启动 Answers 实例后，您会看到控制台中出现一些内容。会有一个*地址*字段。这是答案的合同地址。这就是我们需要调用的*setanswerscontractdress*函数:

`truffle(development)> Questions.setAnswersContractAddress('0x2e91a07090cfbbc0839e0d76d8110e2518bae18c')`

**注意:**当然，用您在该字段中找到的任何地址替换该地址。

现在，让我们在 Answers 契约中设置 *answersUniverse* 变量:

`truffle(development)> Answers.setAnswerUniverse(76)`

并从问题合同中检索:

```
truffle(development)> Questions.whatIsTheAnswerUniverse().then(answer => answer.toNumber())
76 
```

Enter fullscreen mode Exit fullscreen mode

让我们修改答案，并再次检索:

```
truffle(development)> Answers.setAnswerUniverse(42)
truffle(development)> Questions.whatIsTheAnswerUniverse().then(answer => answer.toNumber())
42 
```

Enter fullscreen mode Exit fullscreen mode

Tadaaaaaa！那是给你的接口:)

## 功能修饰符

现在，您可能已经看到了我们项目中的一个安全问题。函数*setanswerscontractdaddress*是外部的，意味着每个人都可以从契约外部调用它。因此，任何人都可以调用该函数并更改地址！！！不好。为了解决这个问题，我们将添加一个函数修饰符。在函数执行之前调用一个修饰符。基本上，它运行一些检查以确保一切正常。在我们的例子中，我们将确保这个函数只由所有者调用。为此，我们将使用 OpenZeppelin Solidity 库中的一个流行契约，名为 *Ownable* 。我会复制并粘贴它。

如果你不明白这份合同的所有内容，也不用担心。只要知道它确保我们的函数将只被所有者调用。

*   在合同中创建 Ownable.sol 文件

```
/**
 * @title Ownable
 * @dev The Ownable contract has an owner address, and provides basic authorization control
 * functions, this simplifies the implementation of "user permissions".
 */
contract Ownable {
  address public owner;

  event OwnershipTransferred(address indexed previousOwner, address indexed newOwner);

  /**
   * @dev The Ownable constructor sets the original `owner` of the contract to the sender
   * account.
   */
  function Ownable() public {
    owner = msg.sender;
  }

  /**
   * @dev Throws if called by any account other than the owner.
   */
  modifier onlyOwner() {
    require(msg.sender == owner);
    _;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   在问题中导入我们自己的契约，并为*setanswerscontractdress*指定函数修饰符:

```
pragma solidity ^0.4.18;

import "./Ownable.sol";

contract AnswersInterface {
    function getAnswerUniverse() public view returns (uint);
}

contract Questions is Ownable{
    AnswersInterface answersContract;

    function setAnswersContractAddress(address _address) external onlyOwner{
        answersContract = AnswersInterface(_address);
    }

    function whatIsTheAnswerUniverse() public view returns (uint){
        uint answer = answersContract.getAnswerUniverse();
        return answer;
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

重新启动 ganache-cli 命令和 truffle 命令。部署我们的问答合同。默认情况下，合同的所有者是由 ganache-cli 创建的第一个帐户。因此，我们将考虑另一个帐户:

`truffle(development)> account = web3.eth.accounts[3]`

现在，如果我们试图从这个帐户设置接口地址，我们将得到一个错误:

```
truffle(development)> Questions.setAnswersContractAddress('0x37eba1bb7d4c779474a4955437e524fbdbac0dc2', {from: account})
Error: VM Exception while processing transaction: revert 
```

Enter fullscreen mode Exit fullscreen mode

如果您删除 object 参数，或者在 from 键中使用 accounts[0]地址，您将能够很好地设置地址。

这就是接口和函数修饰符。玩得开心！！！