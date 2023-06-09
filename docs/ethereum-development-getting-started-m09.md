# 以太坊开发:入门

> 原文：<https://dev.to/damcosset/ethereum-development-getting-started-m09>

## 简介

在我的前两篇文章中，我探讨了可靠性语言。这种语言是为以太坊虚拟机(EVM)设计的。在本文中，我们将跳过语言理论，为我们的契约使用一个测试环境。有了这个，我们将能够更好地理解以太坊是如何工作的。

## 工具

在这第一篇文章中，我们将使用*松露*和*加纳切-cli* 。Truffle 是以太坊开发的框架。它允许你创建一个测试环境，为你的合同和其他事情写测试。然而在本文中，我们将只使用它来创建一个测试环境。

ganache-cli 将与 truffle 一起使用，以创建一个全功能测试环境。我们在这里要做的不会很像一个真正的生产区块链，但它会让我们看到事情是如何工作的。

## 安装工具

*   松露:`[sudo] npm install -g truffle`
*   加纳切-cli: `[sudo] npm install -g ganache-cli`

## 准备好东西

首先，我们将初始化一个新的 truffle 项目。为此，创建一个新目录，并在其中运行:`truffle init`。

你现在将有几个文件夹。首先，打开 *truffle-config.js* 并在里面粘贴这个:

```
module.exports = {
  // See <http://truffleframework.com/docs/advanced/configuration>
    // to customize your Truffle configuration!
    networks: {
        development: {
            host: '127.0.0.1',
            port: 7545,
            network_id: '*'
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于 Windows 用户，你将不得不删除 *truffle.js* 文件以避免冲突。对于其他人来说，你可以两者都保留，并把这段代码放在 *truffle.js* 中，或者像 Windows 用户那样做，这并不重要。

这个文件表明我们的开发网络将在 localhost:7545 上运行。

接下来，在*合同*文件夹中，创建一个新的 *DeveloperFactory.sol* 文件。这就是我们的合同要写的地方。把下面的放进去:

```
 pragma solidity ^0.4.18;

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

    mapping (uint => address) public devToOwner;
    mapping (address => uint) public ownerDevCount;

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
    require(msg.value == 5000000000000000000);
        uint randId = _generateRandomId( _name );
        _createDeveloper(_name, randId, _age );
    }

    function getAllDevelopers() public view returns (uint) {
    return developers.length;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你想更详细地了解这里发生了什么，我在我的第[篇第](https://dev.to/damcosset/learning-solidity-part-one-dl)篇和第[篇第](https://dev.to/damcosset/learning-solidity-part-two-5e3j)篇关于可靠性的文章中已经提到了。但是，简而言之，可以调用此契约来创建具有名称和年龄的开发人员结构。在我们的例子中，创建一个新的开发者将需要 5 以太或(500000000000000 魏，以太坊中可能的最低面额)。

接下来，进入*迁移*文件夹，创建一个名为*2 _ deploy _ contracts . js*:
的文件

```
const DeveloperFactory = artifacts.require('./DeveloperFactory.sol')

module.exports = function(deployer){
    deployer.deploy(DeveloperFactory)
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个文件中，我们导入我们的合同并将其部署到我们的区块链。

## 启动我们的测试环境

打开一个新的终端窗口并运行`ganache-cli -p 7545`。这将在端口 7545 上运行 ganache-cli(与我们在 *truffle-config.js* 文件中指定的端口相同)并为我们创建一些帐户。默认情况下，每个帐户有 100 个以太网。您应该会在您的控制台中看到类似这样的内容:

```
 Available Accounts
==================
(0) 0x473c0be352f997aa0b194786c27d26e29a3f75b1
(1) 0x9657290da5570b17a03198f490b0a2d7eea84ecf
(2) 0x516c0e0152d7b85facb7e3da2d30f67e42a80ca9
(3) 0xf81be8bbe99d2302b85f7cb0f60103c435ae703b
(4) 0xcfacf5ac5567cfdd70ee5a8a9fe4bf7f74d80b02
(5) 0x623e18e34b2de07933fe179862f038230cc69012
(6) 0xd7100dbc1d6f72777ae2a6f5d95c4b8d71f7ce07
(7) 0x7f40df6c6042888a37124821130910e77051b1cf
(8) 0x26a2c2be1f31571f289b7fb60e41f31f7c57a5be
(9) 0x08a945825a28166466987d5fc77b016fe3d80aa5 
```

Enter fullscreen mode Exit fullscreen mode

当然，帐户的地址对你来说是不同的，但是你会有 10 个帐户可以玩。

现在，回到你的第一个终端窗口。确保您在为 truffle 项目创建的文件夹中，运行:`truffle compile`，然后运行`truffle migrate --network development`。这将用以太坊虚拟机(EVM)可以理解的语言编译我们的代码。在这里，*号*将效仿 EVM 号。

如果一切顺利，您的终端应该显示如下内容:

```
truffle migrate --network development
Using network 'development'.

Running migration: 1_initial_migration.js
  Deploying Migrations...
  ... 0xc83617394674cd65f751ff9c05438e16339414ccf1e1662ba66479d79335af13
  Migrations: 0xe982e78028e0dfcbdb135e7a3c1e1ed3d98e36e5
Saving successful migration to network...
  ... 0x78bdff98e4dac310de4650048a0856075a460bed9de0c4d4ea879ea399d142c4
Saving artifacts...
Running migration: 2_deploy_contracts.js
  Deploying DeveloperFactory...
  ... 0x0a314c5ed99c772019ea358ac98e002a1442e26903122528d705bf3ff7ed02ed
  DeveloperFactory: 0xc34cc3e53850673db1dea31d267ea1738edc629f
Saving successful migration to network...
  ... 0x95a3cd861f067cdbe8c96f13477526eacd2a7936662f31724e1354922af49664
Saving artifacts... 
```

Enter fullscreen mode Exit fullscreen mode

注意在 *ganache-cli* 输出中，我们的契约已经被实例化:

```
Transaction: 0xc83617394674cd65f751ff9c05438e16339414ccf1e1662ba66479d79335af13
  Contract created: 0xe982e78028e0dfcbdb135e7a3c1e1ed3d98e36e5
  Gas usage: 269607
  Block Number: 1
  Block Time: Thu May 03 2018 21:04:55 GMT+0200 (CEST) 
```

Enter fullscreen mode Exit fullscreen mode

## 控制台，四处游玩

现在，在你运行 truffle 命令的同一个终端窗口中，运行`truffle console --network development`。这将启动松露控制台，并允许您与我们的区块链互动。我们将使用 Web3 Javascript API 来简化这一过程。首先，让我们将其中一个帐户放入变量中:

`account = web3.eth.accounts[4]`

接下来，让我们运行以下命令:

`DeveloperFactory.deployed().then(inst => {Factory = inst})`

这将在工厂变量中分配一个契约的实例。让我们确保我们的帐户有 100 以太:

```
truffle(development)> web3.fromWei(web3.eth.getBalance(account).toNumber())
'100'
truffle(development)> 
```

Enter fullscreen mode Exit fullscreen mode

方法 getBalance 将返回一个 *BigNumber* 类型。 *toNumber()* 会给出我们在魏的账户余额。我们用*把它转化为以太。*

*   创建开发人员

好了，现在我们要调用函数 createRandomDeveloper。正如您所看到的，这个函数有两个参数，一个 string _name 和一个 uint _age。因为我们还需要 5 ether 来调用这个函数，所以我们需要在函数调用中指定这个:

```
truffle(development)> Factory.createRandomDeveloper('Damien', 26, {from: account, value: web3.toWei(5, "ether")}) 
```

Enter fullscreen mode Exit fullscreen mode

工厂是我们的合同实例。我们给我们的函数三个参数。戴米恩是名字，26 岁是年龄。第三个是一个对象，它有一个来自的键*来知道哪个帐户正在调用它，还有一个键*值*来指定帐户发送的值。这里，来自*值的*是*账户*，我们之前创建的变量。我们将乙醚转换成魏，以符合合同中所要求的价值。*

您的终端应该显示如下内容:

```
{ tx: '0xa3792da93311fdf60054f8a30e7624dd385ccf36cc639881eeb25308ddad5e0e',
  receipt:
   { transactionHash: '0xa3792da93311fdf60054f8a30e7624dd385ccf36cc639881eeb25308ddad5e0e',
     transactionIndex: 0,
     blockHash: '0x3ef1c41cbc79d65c1282a86da3a68120a5c069709a6a5bd3e206ed85d9c270c5',
     blockNumber: 5,
     gasUsed: 148160,
     cumulativeGasUsed: 148160,
     contractAddress: null,
     logs: [ [Object] ],
     status: '0x01',
     logsBloom: '0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000002000080000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000800000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000040000000000000000000200000000000000000000000000000000000000000000000000000002000000000000000000000' },
  logs:
   [ { logIndex: 0,
       transactionIndex: 0,
       transactionHash: '0xa3792da93311fdf60054f8a30e7624dd385ccf36cc639881eeb25308ddad5e0e',
       blockHash: '0x3ef1c41cbc79d65c1282a86da3a68120a5c069709a6a5bd3e206ed85d9c270c5',
       blockNumber: 5,
       address: '0x033711f6fd408b10cc94a21a3e8c20f0e75a4615',
       type: 'mined',
       event: 'NewDeveloper',
       args: [Object] } ] }
truffle(development)> 
```

Enter fullscreen mode Exit fullscreen mode

交易已经成功。这里有很多信息。我们可以看到，事件“NewDeveloper”已经被触发，这是意料之中的。我们有事务的散列、块散列、使用的 gas...现在让我们检查一下我们账户的余额:

```
truffle(development)> web3.fromWei(web3.eth.getBalance(account).toNumber())
'94.985184' 
```

Enter fullscreen mode Exit fullscreen mode

注意这不是 95 乙醚。这是因为当你与合同互动时，你还必须支付额外的费用来进行交易。我们的交易信息中有累计金额(148160)。这意味着 148160 魏已被用来完成这一交易。我们需要把这个乘以汽油价格。每笔交易都有一个汽油价格。我们可以用 transactionHash 来检索它，然后用它乘以累计量来获得事务的开销:

```
truffle(development)> web3.eth.getTransaction('0xa3792da93311fdf60054f8a30e7624dd385ccf36cc639881eeb25308ddad5e0e').gasPrice.toNumber() * 148160
14816000000000000
truffle(development)> web3.fromWei(14816000000000000)
'0.014816'
truffle(development)> 100 - 0.014816
99.985184 
```

Enter fullscreen mode Exit fullscreen mode

我们可以这样计算我们的余额！我们还可以确保我们的合同余额为 5 ether:

```
truffle(development)> web3.fromWei(web3.eth.getBalance('0x033711f6fd408b10cc94a21a3e8c20f0e75a4615').toNumber())
'5' 
```

Enter fullscreen mode Exit fullscreen mode

您将在上面的交易日志字段*地址*中获得您的合同地址。最后，如果我们不把乙醚送到我们的合同中会发生什么？让我们获得一个新账户:

```
truffle(development)> account1 = web3.eth.accounts[9]
'0x5e273389dba808789a27cb792faaf31429c8de8c'
truffle(development)> web3.fromWei(web3.eth.getBalance(account1).toNumber())
'100' 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们调用我们的 createRandomDeveloper 函数:

```
truffle(development)> Factory.createRandomDeveloper('Johnny', 43, {from: account1, value: web3.toWei(10, "ether")})
Error: VM Exception while processing transaction: revert
    at Object.InvalidResponse (/usr/local/lib/node_modules/truffle/build/cli.bundled.js:41484:16)

truffle(development)> web3.fromWei(web3.eth.getBalance(account1).toNumber())
'99.9976828' 
```

Enter fullscreen mode Exit fullscreen mode

我们得到一个错误。但是，用于启动交易的气体无论如何都是损失的！你可以看到账户的余额不再是 100 以太了。

## 结论

在下一篇文章中，我们将通过使用 geth 和 mist 来探索一些接近真实环境的东西。我希望这篇文章能让你更好地理解如何开始以太坊开发。

玩得开心！