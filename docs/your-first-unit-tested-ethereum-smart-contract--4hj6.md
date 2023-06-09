# 你的第一个单元测试了以太坊智能合约

> 原文：<https://dev.to/brightdevs/your-first-unit-tested-ethereum-smart-contract--4hj6>

这篇博文的目标是为您提供如何开始创建第一个经过单元测试的智能契约的具体步骤。

# 我的工具箱

在写这篇文章的时候，我正在使用奇偶校验 1 . 7 . 8-稳定版(奇偶校验/v 1 . 7 . 8-稳定版-D5 fcf 3 b-2017 10 25/x86 _ 64-MAC OS/rust C1 . 21 . 0)作为以太坊客户端。你可以在这个网站上找到一个安装指南:[奇偶校验-快速、轻量级和健壮的以太坊客户端](https://github.com/paritytech/parity)或者如果你使用的是 macOSX 或基于 Debian 的 Linux，请遵循下面几段中描述的说明。

# 在 macOSX 上安装奇偶校验

如果您还没有安装`homebrew`软件包管理器，请执行:

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 
```

Enter fullscreen mode Exit fullscreen mode

而现在奇偶校验安装:

```
brew tap paritytech/paritytech
brew install parity --stable 
```

Enter fullscreen mode Exit fullscreen mode

# 在 Linux 上安装奇偶校验

几乎所有你能在这里找到的奇偶校验包:`https://github.com/paritytech/parity/releases`。要在基于 Debian 的 Linux 上安装，你需要 wget 和 dpkg。下面以安装奇偶校验 1.7.9 为例-稳定:

```
wget https://parity-downloads-mirror.parity.io/v1.7.9/x86_64-unknown-linux-gnu/parity_1.7.9_amd64.deb
sudo dpkg -i parity_1.7.9_amd64.deb 
```

Enter fullscreen mode Exit fullscreen mode

# 起始宇称

出于该文章的目的，我们将从以下方面着手:

*   私人开发链定义`--chain=dev`
*   JSON RPC API:`--jsonrpc-apis web3,rpc,personal,parity_accounts,eth,net,parity,parity_set,signer`
*   天然气价格设置为 0 `--gasprice 0`
*   接口 IP 地址`--ui-interface 0.0.0.0`
*   禁用主机名验证`--ui-no-validation`
*   允许所有主机(主机头值)使用 JSON RPC API `--jsonrpc-hosts all`
*   接口的 IP 地址-所有接口`--jsonrpc-interface all`

```
parity --chain=dev --jsonrpc-apis web3,rpc,personal,parity_accounts,eth,net,parity,parity_set,signer --gasprice 0 --ui-interface 0.0.0.0 --ui-no-validation --jsonrpc-hosts all --jsonrpc-interface all 
```

Enter fullscreen mode Exit fullscreen mode

现在我们确实需要一个有一定金额的账户来进行交易。平价发展链允许你创建一个几乎无限量的帐户，但只能在一个发展链上。你不会发现创建这样一个帐户作为一个常规的用户界面选项，它可以作为一个黑客。请先打开平价 UI ( `http://0.0.0.0:8180/`)的链接，打开账号然后恢复，你应该看到:
[![Restore super account 1](img/bfe67d685f2937d76dae7677505ce7fc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mO0nU4ul--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cpfp05sfjm3x7o6rd5tn.jpg)

诀窍是用空的恢复短语来恢复帐户。请填写账户名称、密码提示、密码，点击右下角
[![Restore super account 2](img/249afa58a0ed9bb1b34bd166dea8240a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1jAyLEQN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o4mz8qef4603wreeycxo.jpg) 导入

创建帐户后，您应该会看到:
[![Restore super account 3](img/69f3ae9514db4a09b75671c067f8693c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bL0P0YS9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hlpqp7crsis41cenkztd.jpg)

下一步是使用未锁定的帐户运行奇偶校验节点，我们将在该节点上执行所有测试交易，因此您不需要通过奇偶校验 UI 上可用的签名者来签署每个测试交易。为此，请首先终止已经开始的奇偶校验过程，并使用两个附加参数运行该过程:

*   解锁地址`--unlock 0x00a329c0648769A73afAc7F9381E08FB43dBEA72`
*   解锁`--password ./password`的指定帐户的用户密码，其中`./password`是包含用户密码的文件的路径

```
parity --chain=dev --jsonrpc-apis web3,rpc,personal,parity_accounts,eth,net,parity,parity_set,signer --gasprice 0 --ui-interface 0.0.0.0 --ui-no-validation --jsonrpc-hosts all --jsonrpc-interface all --unlock 0x00a329c0648769A73afAc7F9381E08FB43dBEA72 --password ./password 
```

Enter fullscreen mode Exit fullscreen mode

其中应该会产生这样的结果:
[![Parity in terminal](img/85d329918ecf5055a80fd29af0d35e29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LIxqSkmI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4d9t2i73g93ti7eimewn.jpg)

奇偶校验节点准备就绪！:)

# 创建和测试智能合约- Truffle

我所知道的创建、测试和构建智能合同的最快和最简单的方法是使用 [truffle](http://truffleframework.com/docs/getting_started/project) 工具。可以使用 npm(节点版本 6.11.4)包管理器安装 truffle:

`npm install -g truffle@4.0.1`

现在，请为您的新项目准备一个空文件夹，并执行以下命令来初始化 truffle 项目结构:

```
truffle init 
```

Enter fullscreen mode Exit fullscreen mode

然后，请打开并编辑配置文件`truffle.js`，`from`参数是一个地址，所有松露交易都应该从该地址执行。默认情况下，请将已经创建/恢复的帐户的地址设置为一个值。

```
module.exports = {  
  // See <http://truffleframework.com/docs/advanced/configuration> 
    networks: { 
        development: {  
            host: "localhost",  
            port: 8545, 
            network_id: "*",    
            from: "0x00a329c0648769A73afAc7F9381E08FB43dBEA72", 
            gas: 5959115    
        }   
    }   
}; 
```

Enter fullscreen mode Exit fullscreen mode

出于这篇文章的目的，我决定创建一个智能契约，它通过调用`add`方法存储所有添加值的总和。方法`add`被限制为只能由智能契约所有者调用。

```
pragma solidity ^0.4.17;    

contract TestSmartContract {    
    address public owner;   
    uint public total;  

    modifier restricted() { 
        if (msg.sender == owner) _; 
    }   

    function TestSmartContract() public {   
        owner = msg.sender; 
    }   

    function add(uint amount) public restricted {   
        total = total + amount; 
    }   
} 
```

Enter fullscreen mode Exit fullscreen mode

有了智能合约，我们需要创建将部署该智能合约的迁移。为此，我们需要在`migrations`文件夹:
中创建一个新文件:`2_test_smart_contract.js`

```
var TestSmartContract = artifacts.require("./TestSmartContract.sol");      
module.exports = function(deployer) {   
    deployer.deploy(TestSmartContract); 
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们需要通过执行以下命令来执行迁移:

`truffle migrate`

我们已经准备好开始编写和执行单元测试。下面你可以找到两个单元测试，第一个断言如果我们用值 4 调用 add 方法，那么总数将等于 4，第二个断言如果我们加上:6，4，20 总数将等于 30。在`beforeEach`块中，我们创建了一个新的智能合同，并读取了合同所有者地址。

```
const TestContract = artifacts.require('TestSmartContract');

let TestContractDeployed;
let ContractOwner;

contract('TestContract', (accounts) => {

    beforeEach('prepare', () => {
        return TestContract.new().then((instance) => {
            TestContractDeployed = instance;
            return TestContractDeployed.owner();
        }).then(function (owner) {
            ContractOwner = owner;
        });
    });

    describe('test add method', () => {

        it('total should be 4 if add(4)', (done) => {
            TestContractDeployed.add(4, {from: ContractOwner}).then(function () {
                TestContractDeployed.total({from: ContractOwner}).then(function (res) {
                    assert.equal(res.valueOf(), 4);
                    done();
                });
            });
        });

        it('total should be 30 if add(6);add(4);add(20);', (done) => {
            Promise.all([
                TestContractDeployed.add(6, {from: ContractOwner}),
                TestContractDeployed.add(4, {from: ContractOwner}),
                TestContractDeployed.add(20, {from: ContractOwner})
            ]).then(() => {
                TestContractDeployed.total({from: ContractOwner}).then(function (res) {
                    assert.equal(res.valueOf(), 30);
                    done();
                });
            });
        });

    });

}); 
```

Enter fullscreen mode Exit fullscreen mode

要了解更多信息，绝对值得访问[块菌文档](https://truffle.readthedocs.io/en/beta/)

在帖子创建期间生成的所有代码都可以在这里找到[智能合约回购](https://github.com/bright/smart-contract)

希望你喜欢！:)

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

丹尼尔·马库拉特,《聪明的发明创造》

[邮箱](//daniel.makurat@brightinventions.pl) [Github](https://github.com/danielmakurat)