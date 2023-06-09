# 二等兵区块链:Hyperledger Composer Javascript API

> 原文：<https://dev.to/damcosset/private-blockchains-hyperledger-composer-javascript-api-2630>

## 简介

在我的[上一篇文章](https://dev.to/damcosset/getting-started-with-hyperledger-composer-and-private-blockchains--36ab)中，我简要介绍了 Hyperledger Composer 框架，该框架使用私有区块链技术构建了一个商业网络。我使用了一个土地注册网络来展示这个框架是如何工作的。然后，我们使用 React 应用程序来使用提供的 REST API。

这一次，我没有使用 REST API，而是使用 Javascript API 制作了一个小的命令行应用程序。概念很简单。您在终端中输入命令来触发操作(检索数据、创建资产和/或交易)。我们将重用我在上一篇文章中使用的同一个土地注册网络。

## 连接到 composer 运行时

首先，我们需要运行我们的私人区块链。如果您还没有浏览我的上一篇文章来设置您的环境，您需要现在就做。

如果您阅读了本文，您需要运行几个命令来启动运行时:

*   首先，你需要从我在上一篇文章中称为 *fabric-tools* 的文件夹中启动`./startFabric.sh`命令。

*   接下来，从 *land-registry* 文件夹中，您需要安装 composer 运行时:`composer network install --card PeerAdmin@hlfv1 --archiveFile land-registry@0.0.1.bna`

*   最后，仍然从 *land-registry* 文件夹中，部署业务网络:`composer network start --networkName land-registry --networkVersion 0.0.1 --networkAdmin admin --networkAdminEnrollSecret adminpw --card PeerAdmin@hlfv1 --file networkadmin.card`

假设您之前已经完成了上一篇文章中的所有步骤，这就是您所需要的全部内容。如果只做那三个命令而不设置合适的环境，显然是不行的。

## 代码

*注意:*我会在本文末尾链接到 Github 的资源库。

应用程序相当简单。有一个 *index.js* 文件。

```
const shell = require('shelljs')
const args = process.argv.slice(2)
const getREregistry = require('./getREregistry')
const getPIregistry = require('./getPIregistry')
const getPI = require('./getPI')
const contractLoan = require('./contractLoan')
const getLoans = require('./getLoans')
const getBanks = require('./getBanks')
const createPI = require('./createPI')
const createRE = require('./createRE')
const contractInsurance = require('./contractInsurance')
const getInsurances = require('./getInsurances')
const buyRealEstate = require('./buyRealEstate')
const getREAgents = require('./getREAgents')
const getNotaries = require('./getNotaries')

// get first argument
let arg = args.shift()
let realEstateId, duration, bankId, privateId, address, insuranceId

switch( arg ){
    case 'getAllRE':
        shell.exec('node getREregistry.js')
        process.exit()
        break
    case 'getAllPI': 
        shell.exec('node getPIregistry.js')
        process.exit()
        break
    case 'getREAgents':
        shell.exec('node getREAgents.js')
        process.exit()
        break
    case 'getInsurances':
        shell.exec('node getInsurances.js')
        process.exit()
        break
    case 'getNotaries': 
        shell.exec('node getNotaries.js')
        process.exit()
        break
    case 'getPI':
        const id = args.shift()
        shell.exec(`node getPI.js ${id}`)
        process.exit()
        break
    case 'getLoans':
        shell.exec('node getLoans.js')
        process.exit()
        break
    case 'getBanks':
        shell.exec('node getBanks.js')
        process.exit()
        break
    case 'createPI':
        privateId = args.shift()
        let name = args.shift()
        address = args.shift()
        let balance = args.shift()
        shell.exec(`node createPI.js ${privateId}  ${name}  ${address}  ${balance}`)
        process.exit()
        break
    case 'createRE':
        let reId = args.shift()
        address = args.shift()
        let reSquareMeters = args.shift()
        let price = args.shift()
        let ownerId = args.shift()
        shell.exec(`node createRE.js ${reId}  ${reAddress}  ${reSquareMeters}  ${price}  ${ownerId}`)
        process.exit()
        break
    case 'contractLoan':
        let debtorId = args.shift()
        let bankId = args.shift()
        realEstateId = args.shift()
        let insterestRate = args.shift()
        duration = args.shift()
        shell.exec(`node contractLoan.js ${debtorId}  ${bankId}  ${realEstateId}  ${insterestRate}  ${duration}`)
        process.exit()
        break
    case 'contractInsurance':
        let insuredId = args.shift()
        insuranceId = args.shift()
        realEstateId = args.shift()
        cost = args.shift()
        duration = args.shift()
        shell.exec(`node contractInsurance.js ${insuredId}  ${insuranceId}  ${realEstateId}  ${cost}  ${duration}`)
        process.exit()
        break
    case 'buyRealEstate':
        let buyer = args.shift()
        let seller = args.shift()
        realEstateId = args.shift()
        let loan = args.shift()
        let realEstateAgent = args.shift()
        let notary = args.shift()
        insuranceId = args.shift()
        shell.exec(`node buyRealEstate.js ${buyer}  ${seller}  ${realEstateId}  ${loan}  ${realEstateAgent}  ${notary}  ${insuranceId}`)
        process.exit()
        break
    default:
        console.log('Wrong argument')
        process.exit()
        break
}

shell.exec('node index.js') 
```

Enter fullscreen mode Exit fullscreen mode

### 一条获取路线

我们使用 *shelljs* 与终端交互。根据您提供的参数，我们将执行某个操作。创建资产或参与者时，某些操作需要额外的参数。让我们来看看 *getAllPI* 参数。 *PI* 代表私人个体，我们网络中的参与者。当我们提供这个参数时，我们将检索网络中的每一个私人参与者。在 *getPIRegistry.js* 文件中描述了该动作:

```
const BusinessNetworkConnection = require('composer-client').BusinessNetworkConnection
const Table = require('cli-table2')

const getPIregistry = (async function(){

    try {
        this.bizNetworkConnection = new BusinessNetworkConnection()
        let connection = await this.bizNetworkConnection.connect('admin@land-registry')
        let registry = await this.bizNetworkConnection.getParticipantRegistry('org.acme.landregistry.PrivateIndividual')
        let resources = await registry.getAll()
        let table = new Table({
            head: ['ID', 'Name', 'Address', 'Balance']
        })
        let arrayLength = resources.length
        for(let i = 0; i < arrayLength; i++) {
            let tableLine = []
            tableLine.push(resources[i].id)
            tableLine.push(resources[i].name)
            tableLine.push(resources[i].address)
            tableLine.push(resources[i].balance)
            table.push(tableLine)
        }
        console.log(table.toString())
        process.exit()

    } catch(error) {
        console.log(error)
        process.exit()
    }
}())

module.exports = getPIregistry 
```

Enter fullscreen mode Exit fullscreen mode

为了与 Javascript API 交互，我们只需要一个包:`composer-client`。每个文件的结构都是一样的。我们使用 *admin@land-registry* 管理卡连接到私有区块链。我把所有东西都放在了一个 IIFE 中(立即调用的函数表达式),并使用了 async/await 关键字使其更加清晰。Javascript API 使用承诺，所以您可以将。如果你愿意的话，可以用方法。

在我们的 *getPIRegistry* 文件中，我们获取参与者注册表并在其上调用 *getAll* 方法。这将检索所有私有的个人参与者。然后，我们使用 *cli-table2* 包将数据显示在终端的一个漂亮的表格中。

### 一个驿站路线

##### 创建一项房地产资产

要创建不动产资产，我们使用如下命令:

`node index.js createRE id address squareMeters price ownerId`

我们需要 5 个参数来创建这样的资产。代码在 *createRE.js* 文件中:

```
const BusinessNetworkConnection = require('composer-client').BusinessNetworkConnection

const createRE = (async function(){
    try {
        this.bizNetworkConnection = new BusinessNetworkConnection()
        let connection = await this.bizNetworkConnection.connect('admin@land-registry')
        const args = process.argv.slice(2)
        const reId = args.shift()
        const address = args.shift()
        const squareMeters = args.shift()
        const price = args.shift()
        const ownerId = args.shift()
        let factory = connection.getFactory()
        let re = factory.newResource('org.acme.landregistry', 'RealEstate', reId)
        re.address = address
        re.squareMeters = parseFloat(squareMeters)
        re.price = parseFloat(price)

        this.reRegistry = await this.bizNetworkConnection.getAssetRegistry('org.acme.landregistry.RealEstate')

        let ownerRelationship = factory.newRelationship('org.acme.landregistry', 'PrivateIndividual', ownerId)
        re.owner = ownerRelationship

        await this.reRegistry.add(re)
        console.log('Real Estate asset created!')
        process.exit()

    }catch( err ){
        console.log(err)
        process.exit()
    }
})()

module.exports = createRE 
```

Enter fullscreen mode Exit fullscreen mode

在最初连接到区块链网络之后，我们检索我们需要的参数。然后，我们创建一个*工厂*来创建一个新资源，在本例中是一个 RealEstate 资产。我们指定私人参与者和这个新的不动产资产之间的关系。最后，在检索 RealEstate 注册表之后，我们调用 add 方法。

*注意:*您可以使用 *addAll* 方法一次添加多个资产或参与者。该方法接受要添加到区块链的资源数组。

#### 提交交易

最后但同样重要的是，我将向您展示如何提交交易。该事务将由以下命令触发:

`node index.js buyRealEstate buyerId sellerId realEstateId loanId realEstateAgentId notaryId insuranceId`

我们还需要几个论点来完成这个交易，因为有相当多的关系。如果你想看看我们正在使用的商业模式，你可以回到[以前的文章](https://dev.to/damcosset/getting-started-with-hyperledger-composer-and-private-blockchains--36ab)。

*buyRealEstate.js*

```
const BusinessNetworkConnection = require('composer-client').BusinessNetworkConnection

const contractInsurance = (async function(){
    try{
        this.bizNetworkConnection = new BusinessNetworkConnection()
        let connection = await this.bizNetworkConnection.connect('admin@land-registry')
        const args = process.argv.slice(2)
        const pIdBuyer = args.shift()
        const pIdSeller = args.shift()
        const realEstateId = args.shift()
        const loanId = args.shift()
        const realEstateAgentId = args.shift()
        const notaryId = args.shift()
        const insuranceId = args.shift()
        let transaction = {
            "$class": "org.acme.landregistry.BuyingRealEstate"
        }
        transaction.buyer = pIdBuyer
        transaction.seller = pIdSeller
        transaction.realEstate = realEstateId
        transaction.loan = loanId
        transaction.realEstateAgent = realEstateAgentId
        transaction.notary = notaryId
        transaction.insurance = insuranceId
        transaction.isNewOwnerMainResidence = false

        let serializer = connection.getSerializer()
        let resource = serializer.fromJSON(transaction)
        await this.bizNetworkConnection.submitTransaction(resource)
        console.log('Transaction Completed!')
        process.exit()
    }catch( err ){
        console.log(err)
        process.exit()
    }
})()

module.exports = contractInsurance 
```

Enter fullscreen mode Exit fullscreen mode

我们以同样的方式开始，连接到区块链并检索参数。然后，我们创建一个事务对象。注意对象中的 *$class* 键。我们让序列化程序将我们的 JSON 转换成 Composer 可以理解的资源。最后我们调用 *submitTransaction* 方法。

当然，在做这笔交易之前，你需要签订一份贷款和保险合同。这两个事务都可以通过命令行创建，您可以在 Github 存储库中找到代码。为了使事情简短，我在这里只展示几个动作。

*注意:*您可以(应该)在一些动作中添加一些验证(例如，确保参与者存在，然后在事务中指定它...).我会让你这么做的。)

## 储存库

代码可以在[这里](https://github.com/Tagada85/Land-Registry-HyperledgerComposer)找到。欢迎反馈:)

玩得开心！