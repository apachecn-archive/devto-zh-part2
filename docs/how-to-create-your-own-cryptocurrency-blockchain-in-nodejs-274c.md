# 如何在 Node.js 中创建自己的加密货币区块链

> 原文：<https://dev.to/educationecosystem/how-to-create-your-own-cryptocurrency-blockchain-in-nodejs-274c>

(本文原载于我的博客[此处](http://blog.liveedu.tv/how-to-create-your-own-cryptocurrency-blockchain-in-node-js/))。

最近加密货币及其底层区块链技术的爆炸席卷了全世界。

尽管区块链如今是一个热门词汇，但很少有人真正理解这项技术是如何推动比特币和以太坊等加密技术的惊人增长的。

埃利奥特·明斯(Elliot Minns)拥有超过六年的软件开发经验，目前正在教授人们如何创建加密货币的技能，他说，“通过学习如何创建区块链来弄脏你的手，将有助于你欣赏这项技术及其工作原理。”

你可以点击查看他的一个基于实践的项目[。](https://www.liveedu.tv/elliottminns/l3Xa0-how-to-create-your-own-cryptocurrency-in-c/)

在本文中，我们将解释如何在 Node.js 中创建自己的简单区块链(并向全世界发布，希望如此！).

我们称之为**liveeducoun**。

## 区块链

区块链是一个不断增长的记录列表，称为块，它们使用加密技术安全地相互连接。

这些块被链接在一起，这样，如果区块链中的任何块被篡改，链的其余部分就会失效。

这种不变性对加密货币的发展至关重要，因为这使得人们很难在完成交易后改变交易。

## 创建一个块

如前所述，区块链由几个相互连接的模块组成。加密哈希用于维护区块链的完整性。

每个块都有一个基于其数据计算的哈希。它还具有前一个块的散列。如果任何块的散列被改变，它将使区块链的剩余部分无效。

下面是 Node.js 中的**块**类的样子:

```
const SHA256 = require("crypto-js/sha256");
class Block {
    constructor(index, timestamp, data, previousHash = '') {
        this.index = index;
        this.previousHash = previousHash;
        this.timestamp = timestamp;
        this.data = data;
        this.hash = this.computeHash();
        this.nonce = 0;
    }
    computeHash() {
        return SHA256(this.index + this.previousHash + this.timestamp + JSON.stringify(this.data) + this.nonce).toString();
    } 
```

正如您在上面看到的，实例化该类的**构造函数**接受以下参数:

*   索引-它跟踪块在区块链中的位置。
*   timestamp——它为每个完成的事务添加一个时间戳。
*   数据—它提供有关已完成交易的信息，例如购买的数量。
*   previousHash 它引用区块链中前一个块的哈希。

我们使用 computeHash 函数根据上述值生成每个块的加密哈希。为此，我们导入了 [crypto-js](https://www.npmjs.com/package/crypto-js) 库，并利用了它的 SHA256 散列函数。

SHA256 是一个强大的、不可逆的哈希函数，用于确保大多数加密货币的安全性。

要设置 crypto-js 库，导航到终端，在与项目文件夹相同的文件夹中，使用 **npm** 安装它。

以下是您可以使用的代码:

```
 //remember to run npm init first
npm install --save crypto-js 
```

## 创造区块链

块链基于块彼此“链接”的概念。

因此，我们将开始在一个**区块链**类中将这些区块相互链接起来。
这里是代码:

```
class Blockchain{
    constructor() {
        this.chain = [this.buildGenesisBlock()];
        this.complexity = 5;
    }
    buildGenesisBlock() {
        return new Block(0, "17/07/2018", "genesis block", "0");
    }
    obtainLatestBlock() {
        return this.chain[this.chain.length - 1];
    }
    addBlock(newBlock) {
        newBlock.previousHash = this.obtainLatestBlock().hash;
        newBlock.mineBlock(this.complexity);
        this.chain.push(newBlock);
    }
    } 
```

正如您在上面的代码中看到的，该类由以下助手函数组成:

**a)。构造函数**

通过传递 **buildGenesisBlock** 来初始化区块链。

**b)。建造创世积木**

在区块链中，创世纪板块象征着区块链的开始。这个初始块没有任何前置块，后续块是在其上构建的。

我们将使用 **buildGenesisBlock()** 函数来创建它。

**c)。获取最新的块**

为了获得区块链中的最新块，我们将使用**obsolatestblock()**函数。

**d)。添加新块**

为了向区块链 Node.js 示例添加一个新块，我们将使用 **addBlock()** 函数。为了实现这一点，我们将把前一个块的散列添加到新块中，以保持区块链的完整性。

因为我们改变了新块的细节，所以必须再次计算它的散列。完成后，我们将把块推入链数组。

**e)。确认区块链的有效性**

**confirmValidity()** 函数对于评估区块链的完整性和确保不存在缺陷至关重要。这个函数使用一系列的 **if** 语句来确认每个块的散列是否未被改变。

此外，它还检查每两个连续块的哈希值是否指向彼此。如果一切都有效，则返回 true 否则，它返回 false。

代码如下:

```
confirmValidity() {
        for (let i = 1; i < this.chain.length; i++){
            const currentBlock = this.chain[i];
            const previousBlock = this.chain[i - 1];
            if (currentBlock.hash !== currentBlock.computeHash()) {
                return false;
            }
            if (currentBlock.previousHash !== previousBlock.hash) {
                return false;
            }
        }
        return true;
    } 
```

## 试探区块链

这是最精彩的部分！

代码如下:

```
let liveEduCoin = new Blockchain();
console.log('<<Lets mine block 1>>');
liveEduCoin.addBlock(new Block(1, "27/07/2018", { quantity: 10 }));
console.log('<<Lets mine block 2>>');
liveEduCoin.addBlock(new Block(2, "27/07/2018", { quantity: 20 })); 
```

我们将创建一个 Blockchain 类的新实例，并将其命名为 **liveEduCoin** 。

此后，我们将添加一些任意块到区块链。您可以将任何类型的数据添加到块中。

在这个简单的区块链 Node.js 教程中，我们决定添加一个具有**数量**属性的对象。

以下是我们区块链项目的完整代码:

```
const SHA256 = require("crypto-js/sha256");
class Block {
    constructor(index, timestamp, data, previousHash = '') {
        this.index = index;
        this.previousHash = previousHash;
        this.timestamp = timestamp;
        this.data = data;
        this.hash = this.computeHash();
        this.nonce = 0;
    }
    computeHash() {
        return SHA256(this.index + this.previousHash + this.timestamp + JSON.stringify(this.data) + this.nonce).toString();
    }
    mineBlock(complexity) {
        while (this.hash.substring(0, complexity) !== Array(complexity + 1).join("0")) {
            this.nonce++;
            this.hash = this.computeHash();
        }
        console.log("Mining is taking place: " + this.hash);
    }
}
class Blockchain{
    constructor() {
        this.chain = [this.buildGenesisBlock()];
        this.complexity = 5;
    }

    buildGenesisBlock() {
        return new Block(0, "17/07/2018", "genesis block", "0");
    }

    obtainLatestBlock() {
        return this.chain[this.chain.length - 1];
    }

    addBlock(newBlock) {
        newBlock.previousHash = this.obtainLatestBlock().hash;
        newBlock.mineBlock(this.complexity);
        this.chain.push(newBlock);
    }

    confirmValidity() {
        for (let i = 1; i < this.chain.length; i++){
            const currentBlock = this.chain[i];
            const previousBlock = this.chain[i - 1];

            if (currentBlock.hash !== currentBlock.computeHash()) {
                return false;
            }

            if (currentBlock.previousHash !== previousBlock.hash) {
                return false;
            }
        }
        return true;
    }
}

let liveEduCoin = new Blockchain();
console.log('<<Lets mine block 1>>');
liveEduCoin.addBlock(new Block(1, "27/07/2018", { quantity: 10 }));
console.log('<<Lets mine block 2>>');
liveEduCoin.addBlock(new Block(2, "27/07/2018", { quantity: 20 })); 
```

如果我们将代码保存在 blockchain.js 文件中，并在终端上运行，则输出如下:

[![cryptocurrency blockchain in Node.js](img/1bcf1b530ad7d0c341b1434f200256be.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kpI0gpRA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iq4w64n1yhza0pgglrjr.jpg)

有用！

## 结论

Node.js 中的上述加密货币区块链还远未完成。事实上，如果你把它发布给全世界，你可能会成为唯一一个使用它的人！

例如，它缺乏成功的加密货币的重要原则，如工作证明和点对点网络。

尽管如此，区块链 node.js 演示展示了区块链是如何工作的。与许多人的想法相反，这个简单的项目说明了区块链的概念简单且易于实施。

当然，如果你正在寻找一个更高级的项目来完全沉浸在加密货币的世界中，你可以[点击此处](https://www.liveedu.tv/projects/premium/cryptocurrency/?q=crypto)从 LiveEdu 网站获取教程。