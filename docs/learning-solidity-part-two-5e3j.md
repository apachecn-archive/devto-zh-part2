# 学习的可靠性:第二部分

> 原文：<https://dev.to/damcosset/learning-solidity-part-two-5e3j>

### 简介

在我的上一篇文章中，我们通过创建一个简单的契约和探索一些简单的结构和概念开始了对可靠性的研究。我们将从中断的地方继续。

## 映射

在*结构*和*数组*之后，我们还可以在*映射*中存储数据。映射是一个键值存储。例如:

```
mapping(uint => string) public keyUintStringValue;
mapping(address => uint) public addressToUint; 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们有两个公共映射。第一个有一个 *uint* 键和一个*字符串*值。第二个有一个*地址*键和一个 *uint* 值。

#### 地址

以太坊区块链由地址组成。每个帐户都有一个唯一的地址。它采用以下形式:`0x0cE440255306E921F41612C46F1v6df9Cc969183`。每个地址都有一定数量的*以太*，这是区块链上使用的加密货币，可以接收或向其他地址发送以太。

记住这一点，让我们为 DeveloperFactory 创建一个新的映射:

```
pragma solidity ^0.4.22;

contract DeveloperFactory {

    event NewDeveloper(uint devId, string name, uint age);

    uint maxAge = 100;
    uint minAge = 5;

    struct Developer {
        string name;
        uint id;
        uint age;
    }

    Developer[] public developers;

    mapping (address => uint) public ownerDevCount;
    mapping (uint => address) public devToOwner;

    function _createDeveloper( string _name, uint _id, uint _age ) private{
        uint id = developers.push( Developer( _name, _id, _age ) ) - 1;
        NewDeveloper(id, _name, _age);
    }

    function _generateRandomId( string _str ) private pure returns (uint){
        uint rand = uint(keccak256(_str));
        return rand;
    }

    function createRandomDeveloper( string _name, uint _age ) public view {
        require(_age > minAge);
        require(_age < maxAge);
        uint randId = _generateRandomId( _name );
        _createDeveloper(_name, randId, _age );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在第一个映射中，我们将跟踪每个帐户(地址)创建的 dev 的数量。第二，我们将跟踪每个 dev 的所有者。

### 消息发送者

每个合同都是被动的，他们不做任何事情，直到有人触发他们。 *msg.sender* 是一个全局变量，允许我们知道哪个地址负责触发。它可能是一个帐户或另一个智能合同。

有了这些信息，我们可以适当地更新我们的映射。在 *_createDeveloper* 函数中，我们将为这个特定的地址增加 *ownerDevCount* 。在*devtooowner*映射中，我们将指出新创建的开发者归 *msg.sender* 地址所有。

```
pragma solidity ^0.4.22;

contract DeveloperFactory {

    event NewDeveloper(uint devId, string name, uint age);

    uint maxAge = 100;
    uint minAge = 5;

    struct Developer {
        string name;
        uint id;
        uint age;
    }

    Developer[] public developers;

    mapping (address => uint) public ownerDevCount;
    mapping (uint => address) public devToOwner;

    function _createDeveloper( string _name, uint _id, uint _age ) private{
        uint id = developers.push( Developer( _name, _id, _age ) ) - 1;
        ownerDevCount[msg.sender]++;
        devToOwner[id] = msg.sender;
        NewDeveloper(id, _name, _age);
    }

    function _generateRandomId( string _str ) private pure returns (uint){
        uint rand = uint(keccak256(_str));
        return rand;
    }

    function createRandomDeveloper( string _name, uint _age ) public view {
        require(_age > minAge);
        require(_age < maxAge);
        uint randId = _generateRandomId( _name );
        _createDeveloper(_name, randId, _age );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意 *++* 符号增加*ownerDevCount【msg . sender】*，就活 Javascript！

### 继承和导入

契约可以从其他契约继承。如果你打开一个名为 *DeveloperLearning.sol* 的新文件，你可以让它从*developer factory . sol*:
中继承

```
pragma solidity ^0.4.22;

import "./DeveloperFactory.sol";

contract DeveloperLearning is DeveloperFactory {
    // I have now access to DeveloperFactory functions
} 
```

Enter fullscreen mode Exit fullscreen mode

注意我们是如何导入 *DeveloperFactory* 契约的(假设它在同一个文件夹中)。为了声明继承，我们使用关键字*为*。

### 应付

为了让合同接收到*乙醚*，我们需要将*应付款*关键字传递给一个函数。发送的数量可以在全局变量 *msg.value* 中访问。因此，我们可以确保在创建开发人员之前将一定量的以太网发送到合同:

```
pragma solidity ^0.4.22;

contract DeveloperFactory {

    event NewDeveloper(uint devId, string name, uint age);

    uint maxAge = 100;
    uint minAge = 5;

    struct Developer {
        string name;
        uint id;
        uint age;
    }

    Developer[] public developers;

    mapping (address => uint) public ownerDevCount;
    mapping (uint => address) public devToOwner;

    function _createDeveloper( string _name, uint _id, uint _age ) private{
        uint id = developers.push( Developer( _name, _id, _age ) ) - 1;
        ownerDevCount[msg.sender]++;
        devToOwner[id] = msg.sender;
        NewDeveloper(id, _name, _age);
    }

    function _generateRandomId( string _str ) private pure returns (uint){
        uint rand = uint(keccak256(_str));
        return rand;
    }

    function createRandomDeveloper( string _name, uint _age ) public payable {
        require(_age > minAge);
        require(_age < maxAge);
        require(msg.value == 5);
        uint randId = _generateRandomId( _name );
        _createDeveloper(_name, randId, _age );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 记忆和存储

在 Solidity 中，变量存储在两个地方:存储器或内存。存储在内存中的变量是临时的，它在函数被使用时存在，然后被丢弃。存储在存储器中的变量永久存在于区块链上。大多数时候，你不必担心在哪里存储你的变量，因为 Solidity 为你处理它。

例如，在函数外部声明的状态变量(maxAge、minAge、Developer)存储在存储器中。像 randId，Id，rand 这样的变量存储在内存中。

但是，在某些情况下，您希望显式声明存储某些变量的位置。Solidity 允许你用`memory`和`storage`关键字来做这件事。