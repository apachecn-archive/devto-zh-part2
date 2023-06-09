# 学习可靠性，第一部分

> 原文：<https://dev.to/damcosset/learning-solidity-part-one-dl>

## 简介

Solidity 是一种用于实现智能合约的高级语言。这是一种面向对象的语言，设计目标是以太坊虚拟机。让我们来探索一下吧！

## 走吧！！！

让我们创建一个名为 *Contract.sol*
的文件。首先，您必须定义您正在使用的版本。这是编译器需要的信息。

```
pragma solidity ^0.4.22; 
```

Enter fullscreen mode Exit fullscreen mode

以太坊的所有代码都属于一个契约。让我们创建一个契约，并在其中定义一些变量。

```
pragma solidity ^0.4.22;

contract DeveloperFactory {
    // Let's create a Developer!
    uint dnaDigits = 16;
    uint ageDigits = 2;
} 
```

Enter fullscreen mode Exit fullscreen mode

坚实是一种类型化的语言。 *uint* 代表无符号整数(非负整数)。这些变量是**状态变量**。它们将被永久保存在契约存储器中(以太坊区块链中)。我们的开发者有 16 位数的 dna 和 2 位数的年龄。

让我们继续前进！

### 结构和数组

```
pragma solidity ^0.4.22;

contract DeveloperFactory {
    // Let's create a Developer!
    uint dnaDigits = 16;
    uint ageDigits = 2;

    struct Developer {
        string name;
        uint dna;
        uint age;
    }

    Developer[] public developers;
} 
```

Enter fullscreen mode Exit fullscreen mode

*struct* 变量允许我们定义更复杂的数据结构。这里，开发人员 struc 接受一个名为 name 的字符串、一个名为 dna 的 uint 和一个名为 age 的 uint。

实也有阵。您可以创建动态或固定数组。这里，我们的开发人员数组是动态的，因为我们没有指定长度。因此，我们可以不受任何限制地不断增加开发人员。

`Developer[5] public developers`是一个固定数组，可以包含 5 个 Developer struct。

### 功能

函数应该是这样的:

```
pragma solidity ^0.4.22;

contract DeveloperFactory {
    // Let's create a Developer!
    uint maxAge = 100;
    uint minAge = 5;

    struct Developer {
        string name;
        uint id;
        uint age;
    }

    Developer[] public developers;

    function _createDeveloper( string _name, uint _id, uint _age ) private{
        developers.push( Developer( _name, _id, _age ) );
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

我们用*函数*关键字创建函数。函数可以带参数。默认情况下，函数是*公共的*。我添加了 *private* 关键字来使这个函数私有。我还选择在私有函数或变量前添加下划线，以区别于公共变量。你不必这样做，我只是觉得更容易阅读。

以太坊内置了哈希函数 *keccak256* 。这是 SHA3 的一个版本。给它传递任意字符串，你会得到一个 256 位的十六进制数。

如您所见，我们将 keccak256 值类型转换为一个 uint 值，并返回它。

除了 *private* 关键字之外，还有一些东西可以添加到函数中:

*   函数返回一些东西:使用 *returns* 关键字并指定函数返回的类型。在这里，它将返回一个 *uint* 类型。

*   *view* 关键字意味着我们的函数需要查看合同的一些变量，但不能修改它们。我们的函数 *createRandomDeveloper* 需要查看 *minAge* 和 *maxAge* 变量。

*   *纯*关键字表示该函数不访问应用程序中的任何数据。它只根据它的参数返回一些东西。我们的*_ generator andomid*是纯种的。

我们有三个功能。 *_generateRandomId* 通过使用 keccak256 内置函数为我们的开发者生成一个随机 Id。 *_createDeveloper* 创建一个新的开发人员结构并将其推入我们的开发人员数组。 *createRandomDeveloper* 是唯一的公共函数。它检查提供的年龄是否正确。如果不是这样, *require* 语句将抛出错误(在我们的例子中年龄大于 100，小于 5)。所以，最后一个函数是可以从我们的契约外部调用的。

### 事件

您还可以创建事件，以便将区块链上发生的情况传达给前端应用程序。你的应用程序会监听这些事件并做出相应的反应。

```
pragma solidity ^0.4.22;

contract DeveloperFactory {
    // Let's create a Developer!

    event NewDeveloper(uint devId, string name, uint age);

    uint maxAge = 100;
    uint minAge = 5;

    struct Developer {
        string name;
        uint id;
        uint age;
    }

    Developer[] public developers;

    function _createDeveloper( string _name, uint _id, uint _age ) private{
        uint id = developers.push( Developer( _name, _id, _age ) ) - 1;
        newDeveloper(id, _name, _age);
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

我们用关键字 *event* 创建一个事件。在我们的合同中，事件将有三个参数，devId、name 和 age。我们将在我们的 *_createDeveloper* 函数中触发事件。它像一个函数一样工作。由于 developers 数组上的 *push* 方法，id 被检索到。它将返回数组的新长度。因为数组从索引 0 开始，所以我们减去 1 得到开发者 id。

*注:*本系列灵感来源于[隐僵尸](https://cryptozombies.io)教程。不要犹豫，来看看吧，太神奇了！

*注意之二*:如果你想在 Solidity 中玩，你可以在你的浏览器中使用[混音 IDE](https://remix.ethereum.org)

### 结论

我们已经探讨了一些坚固性的概念。在下一篇文章中，我们将更深入地探讨可靠性允许你做什么。敬请期待！