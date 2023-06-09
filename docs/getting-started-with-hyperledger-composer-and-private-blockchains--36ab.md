# Hyperledger Composer 和二等兵区块链入门

> 原文：<https://dev.to/damcosset/getting-started-with-hyperledger-composer-and-private-blockchains--36ab>

## 简介

在我的[上一篇文章](https://dev.to/damcosset/making-blockchain-relevant-for-businesses-586l)中，我开始探索区块链需要具备哪些特征才能对企业和商业网络有所帮助。现在是时候深入研究并使用一些已经存在的解决方案了。

### 私立区块链还是公立区块链？

有几种框架和工具可用于创建企业网络的区块链。我们可以沿着以太坊的公共区块链之路走下去。这是一个可能的解决方案。然而，在我的学习中，我选择了一条不同的道路，使用一个私有的区块链框架。我认为，对于企业而言，私有区块链比公有企业更有意义。例如，以太坊区块链提供的匿名并不是特别有用。加密货币也不是特别有用。一些企业会发现像以太坊这样的无许可区块链更有吸引力，但网络使用私有区块链更有意义。

在某些网络中，你需要一个许可区块链，你只能通过邀请或许可才能加入。我选择探索 Hyperledger 框架，尤其是 Fabric 和 Composer。

[Hyperledger](https://www.hyperledger.org/) 是一套开源工具。它由 Linux 基金会主办，试图探索如何在现实世界的商业环境中使用区块链。这背后有很多大公司(IBM、空客、英特尔、摩根大通、日立、博世...).

Hyperledger Fabric 是这次冒险中诞生的第一个框架。这是一个使用容器技术和智能合约的区块链实现。

但是，我们将从使用一个叫做 Hyperledger Composer 的工具开始。Composer 构建在 Fabric 之上，将允许我们使用 Javascript 快速构建一个业务网络。浏览器中还有一个非常有用的游戏区，我们将会用到。

### 我们将在本文中介绍什么

*   使用不同的组合工具创建一个商业网络
*   使用 Composer REST API 创建一些事务
*   创建一个简单的应用程序，并通过 REST API 使用我们的业务网络

我们走吧！

## 设置好一切

*   你将需要[码头工人](https://docs.docker.com/docker-for-mac/install/)

*   您将需要 Composer CLI 工具:`npm install -g composer-cli`

*   这将运行一个 REST 服务器来暴露我们的业务网络:
    `npm install -g composer-rest-server`

*   生成代码的一些事情:

`npm install -g generator-hyperledger-composer`
T1】

*   接下来，我们需要一个本地 Hyperledger Fabric 运行时，在那里部署我们的业务网络。

创建一个目录，命名为 fabric-tools

`mkdir ~/fabric-tools && cd ~/fabric-tools`

在这个新目录中，运行以下命令:

`curl -O https://raw.githubusercontent.com/hyperledger/composer-tools/master/packages/fabric-dev-servers/fabric-dev-servers.zip
unzip fabric-dev-servers.zip`

这为您提供了运行本地 Hyperledger Fabric 运行时所需的一切。

跑:`./downloadFabric.sh`
接下来，跑:`./startFabric.sh`，然后`./createPeerAdminCard.sh`

太好了，我们都准备好了，可以开始写代码了。

## 创建商业网络

你可以选择从头开始创建自己的商业网络。但是，为了加快进度，我给你做了一个。我选择了一个土地注册网络作为例子。

在网络中，有三种类型的资源:

*   参与者:参与网络的实体类型。在我的例子中，有 5 种不同类型的参与者:

    *   私人:他们买卖他们的财产
    *   银行:他们提供贷款(或不提供贷款)
    *   公证人:他们验证交易
    *   房地产经纪人:他们促进私人之间的交易
    *   保险公司:你确实需要房地产保险，对吗？
*   资产:资产是交易的组成部分。在这个网络中，有 3 种不同的资产:

    *   房地产:因为它是一个土地注册处，记得吗？
    *   保险:来自保险公司参与者
    *   贷款:从银行参与者那里，你知道，为房地产融资
*   事务:没有事务，什么都不会移动。交易使事情发生。我的网络中有 3 种不同的交易:

    *   购买不动产:一个私人购买，另一个出售。接下来会发生什么？
    *   收缩贷款:在银行和私人之间
    *   收缩保险:私人和保险公司之间

好了，这就是我们商业网络的轮廓。让我们创造它。

在 Hyperledger 中，业务网络档案由 4 种不同类型的文件组成:

*   模型文件。你描述你的参与者，资产和交易。我们用一种建模语言来描述这一点。别担心，没那么复杂。

*   访问文件。Hyperledger 面料是一个许可的区块链。我们需要定义谁能看到什么。为了简单起见，我们不会在这个文件中做任何疯狂的事情。每个人都会看到一切。在这里，我们使用访问控制语言(ACL)来表达权限。

*   脚本文件。用 Javascript 写的。这定义了当网络中发生交易时会发生什么。

*   查询文件。用于执行查询。用本地查询语言编写。我们暂时不使用这个文件。下次吧。

### 生成代码

我们会用约曼为我们设置密码。运行:

`yo hyperledger-composer:businessnetwork`

输入 land-registry 作为网络名称。根据需要填写作者字段。

选择 Apache-2.0 作为许可证

输入 org.acme.landregistry 作为名称空间。

太好了，现在你有你需要的所有文件了。

### 模型文件

先从车型说起。这是我们定义参与者、资产和交易的地方。打开`org.acme.landregistry.cto`文件并把它放在那里:

```
**
 * Business network model
 */

namespace org.acme.landregistry

participant PrivateIndividual identified by id {
  o String id
  o String name
  o String address
  o Double balance default = 0.0
}

participant Bank identified by id {
  o String id
  o String name
  o Double balance default = 0.0
}

participant InsuranceCompany identified by id {
  o String id
  o String name
  o Double balance default = 0.0
}

participant Notary identified by id {
  o String id
  o String name
  o String address
  o Double balance default = 0.0
}

participant RealEstateAgent identified by id {
  o String id
  o String name
  o Double balance default = 0.0
  o Double feeRate
}

asset RealEstate identified by id {
  o String id
  o String address
  o Double squareMeters
  o Double price
  --> PrivateIndividual owner
}

asset Loan identified by id {
   o String id
   o Double amount
   o Double interestRate
   --> PrivateIndividual debtor
  --> Bank bank
  --> RealEstate realEstate
   o Integer durationInMonths
}

asset Insurance identified by id {
  o String id
  --> RealEstate realEstate
  --> PrivateIndividual insured
  --> InsuranceCompany insuranceCompany
  o Double monthlyCost
  o Integer durationInMonths
}

transaction BuyingRealEstate {
  --> PrivateIndividual buyer
  --> PrivateIndividual seller
  --> RealEstate realEstate
  --> Loan loan
  --> RealEstateAgent realEstateAgent
  --> Notary notary
  --> Insurance insurance
}

transaction ContractingInsurance {
  --> PrivateIndividual insured
  --> InsuranceCompany insuranceCompany
  --> RealEstate realEstate
  o Double monthlyCost
  o Integer durationInMonths
}

transaction ContractingLoan {
  --> PrivateIndividual debtor
  --> Bank bank
  --> RealEstate realEstate
  o Double interestRate
  o Integer durationInMonths
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 参与者

好了，这里有相当多的事情要揭开。我认为它读起来相对容易。首先，我定义了我们的 5 种参与者类型。它们都将由它们的 *id* 字段唯一标识。如您所见，这种建模语言是强类型的。您需要为每个字段指定类型。对我们的参与者来说没什么特别的，一些字符串和双精度类型。

#### 资产

我们的资产也将通过它们的 *id* 字段来标识。但是，这一次，您可以在资产定义中看到一些箭头(- >)。箭头定义了关系。房地产资产与私人，即其所有者有关系。贷款有三种不同的关系:申请贷款的个人、发放贷款的银行和贷款资助的房地产资产。

#### 交易

我们模型中的 3 个交易。它们不像其他资源那样由任何字段来标识。这里指定的字段将被传递给脚本文件中声明的函数。现在让我们看看这个。

### 脚本文件

回到传统的 Javascript。这是我们定义交易过程中发生的事情的地方。打开 lib 文件夹中的 logic.js 文件:

```
'use strict';

/**
 * Contracting an insurance
 * @param {org.acme.landregistry.ContractingInsurance} insurance
 * @transaction
 */

 function contractingInsurance( insurance ){
    return getAssetRegistry('org.acme.landregistry.Insurance')
      .then(function(assetRegistry){
      var factory = getFactory()
      var insuranceId = insurance.insured.id + '' + insurance.insuranceCompany.id + '' + insurance.realEstate.id
      var insuranceAsset = factory.newResource('org.acme.landregistry', 'Insurance', insuranceId)
      insuranceAsset.insured = insurance.insured
      insuranceAsset.insuranceCompany = insurance.insuranceCompany
      insuranceAsset.realEstate = insurance.realEstate
      insuranceAsset.durationInMonths = insurance.durationInMonths
      insuranceAsset.monthlyCost = insurance.monthlyCost

      return assetRegistry.add(insuranceAsset)
    })
 }

/**
 * Contracting a loan
 * @param {org.acme.landregistry.ContractingLoan} loan
 * @transaction
 */

function contractingLoan( loan ){
  return getAssetRegistry('org.acme.landregistry.Loan')
    .then(function(assetRegistry){
    var factory = getFactory()
    var loanId = loan.debtor.id + '' + loan.realEstate.id + '' + loan.bank.id
    var loanAsset = factory.newResource('org.acme.landregistry', 'Loan', loanId) 
    loanAsset.debtor = loan.debtor
    loanAsset.bank = loan.bank
    loanAsset.interestRate = loan.interestRate
    loanAsset.durationInMonths = loan.durationInMonths
    loanAsset.realEstate = loan.realEstate
    loanAsset.amount = loan.realEstate.price

    return assetRegistry.add(loanAsset)
  })
}

/**
 * Buying Real Estate
 * @param {org.acme.landregistry.BuyingRealEstate} trade
 * @transaction
 */

function buyingRealEstate( trade ){
  var notaryFees = 0.1 * trade.realEstate.price
  var realEstateAgentFees = trade.realEstateAgent.feeRate * trade.realEstate.price
  var insuranceCostFirstMonth = trade.insurance.monthlyCost
  var totalCost = notaryFees + realEstateAgentFees + insuranceCostFirstMonth 
  // Updates the seller's balance
  trade.seller.balance += trade.realEstate.price

  // Check if the buyer has enough to pay the notary, real estate agent and insurance
  if( trade.buyer.balance < totalCost ){
    throw new Error('Not enough funds to buy this!')
  }
  trade.buyer.balance -= totalCost
  trade.realEstate.owner = trade.buyer
  trade.realEstateAgent.balance += realEstateAgentFees
  trade.notary.balance += notaryFees

  Promise.all([
    getAssetRegistry('org.acme.landregistry.RealEstate'),
    getParticipantRegistry('org.acme.landregistry.PrivateIndividual'),
    getParticipantRegistry('org.acme.landregistry.PrivateIndividual'),
    getParticipantRegistry('org.acme.landregistry.Notary'),
    getParticipantRegistry('org.acme.landregistry.RealEstateAgent')
  ]).then(function(registries){
    return (
      registries[0].update(trade.realEstate),
      registries[1].update(trade.seller),
      registries[2].update(trade.buyer),
      registries[3].update(trade.notary),
      registries[4].update(trade.realEstateAgent)
    )
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

好的，我们有三个函数，每个交易一个。您使用 JSDoc 描述和标记来描述您的函数应该处理什么事务。如您所见，您提供了名称空间，例如{ org . acme . land registry . contracting insurance }，后面是 *@transaction* 。

**收缩保险**

在我们的模型中，要购买房地产，你首先需要签订贷款和保险合同。该功能创建一项保险资产。我们使用 *getAssetRegistry* 函数来检索保险公司的资产注册。然后，我们创建一个新的保险资产并定义其属性。最后，我们将新资产添加到注册表中。

*注意*:我们使用的所有注册中心都有退货承诺。

**收缩贷款**

完全一样的概念。获取贷款注册表，创建一个新的贷款，添加它。请注意我是如何选择动态创建每个资产 ID 的。在一个更大的网络中，我们需要想出一些不同的东西。在我们的情况下，会没事的。

**购买解除状态**

最后，我们可以购买一些房地产。这里发生了一些事情。首先，我必须解释一下我们网络的一些规则:

*   在这个交易过程中，每个人都得到报酬。以前没有。

*   公证人必须确认交易。她拿房地产价格的 10%作为她的费用。这或多或少是这类交易在法国的成本。因此，如果你花 10 万欧元买了一栋房子，你需要额外支付 1 万欧元给公证人。

*   每笔交易都是通过房地产经纪人进行的。代理得到的是在模型中指定的(feeRate)。

*   保险每月有一笔费用。在交易过程中，买方必须支付第一个月。

*   我假设买方只按房地产价格签订贷款合同。其他费用都要她自己出。正如您在函数中看到的，如果买方的余额不能覆盖保险的第一个月，公证费和房地产经纪人的费用，交易就不会发生。

*   事务是原子的。意思是，要么一切都发生，要么什么都不发生。如果在事务结束时更新我们的一个参与者时发生错误，我们将返回到事务之前的状态。

*   我为网络选择的规则是任意的。有些是基于它在法国的运作方式，有些是为了使事情变得简单一点。

在这个功能中，我们支付每个人。然后，我们获取我们需要的每一个注册表，并更新它们。交易完成！

### 权限文件

最后是权限文件。我们不会做任何疯狂的事。只需复制并粘贴到。acl 文件。我们可以根据参与者定义权限，就像在私有区块链中一样，但这超出了本文的范围。把这个放到*权限. acl* 文件:

```
rule Default {
    description: "Allow all participants access to all resources"
    participant: "ANY"
    operation: ALL
    resource: "org.acme.landregistry.*"
    action: ALLOW
}

rule SystemACL {
  description:  "System ACL to permit all access"
  participant: "ANY"
  operation: ALL
  resource: "org.hyperledger.composer.system.**"
  action: ALLOW
} 
```

Enter fullscreen mode Exit fullscreen mode

基本上就是让大家为所欲为。这不是你想要的生产就绪的区块链，但足够好了。

## 正在部署

一切准备就绪！现在，我们可以在 Hyperledger 结构中部署我们的业务网络。我们需要运行几个命令:

首先，我们需要创建一个 Fabric 可以使用的业务网络档案。为此，请在 land-registry 文件夹中运行以下命令:

`composer archive create -t dir -n .`

这将创建一个*。bna* 文件。

接下来，我们将使用以下组件安装 composer 运行时:

`composer network install --card PeerAdmin@hlfv1 --archiveFile land-registry@0.0.1.bna`

PeerAdmin 卡是您之前通过运行 */.createPeerAdminCard.sh* 创建的卡。

接下来，我们将部署业务网络:

`composer network start --networkName land-registry --networkVersion 0.0.1 --networkAdmin admin --networkAdminEnrollSecret adminpw --card PeerAdmin@hlfv1 --file networkadmin.card`

最后，我们需要将网络管理员卡导入网络:

`composer card import --file networkadmin.card`

### 四处游玩

一切准备就绪。我们现在可以通过运行`composer-rest-server`来创建 REST API。

*   输入`admin@land-registry`作为卡名。
*   从不使用名称空间，然后是不，然后是，然后是不。(只是对 WebSockets 是)

您现在可以导航到 localhost:3000/explorer。

[![](img/5a8fcab6a4943822ccf790730f728579.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w9xw7q13--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3neaen6czlimn7yvdelk.png)

我们现在可以访问 REST API 来与我们的业务网络进行交互。如您所见，我们拥有了之前定义的一切:参与者、资产和交易。

首先，我们需要创建我们的参与者和至少一个 RealEstate 资产，以便我们可以进行交易。

我们转到私人物品，选择 */POST* 路线。我将在这里创建两个参与者。

我将命名一个私有个体 Joe，id 为 *joe* 。我还会给他余额 50000。给你想要的任何地址。点击*试试*创造乔。

另一个人将是索菲，id 为索菲。她将有 10000 英镑的结余。给她一个地址，点击*试试*。

确保每次响应代码都是 200。您可以通过前往 */GET* 路线并获取数据来进行双重检查。

现在让我们转向其他参与者。概念是一样的，只是在不同的项目之间跳转。

公证人将是 Ben，id 为 *ben* 。房地产经纪人将被称为珍妮，id *珍妮*。她的费率将设置为 0.05 ( 5%)。银行将是汇丰银行，id *汇丰银行*。最后保险公司会是安盛，id *安盛*。

现在，让我们创建一个房地产资产。同样的概念，得到了 */POST* 路线并给它以下数据:

```
 {  "$class":  "org.acme.landregistry.RealEstate",  "id":  "buildingOne",  "address":  "France",  "squareMeters":  100,  "price":  100000,  "owner":  "resource:org.acme.landregistry.PrivateIndividual#sophie"  } 
```

Enter fullscreen mode Exit fullscreen mode

正如您在 owner 字段中看到的，我决定通过指定 Sophie 的 id 将该资产交给她。这是不动产和私人的关系。

### 一笔交易

太好了，现在，我们可以做交易了！苏菲决定卖掉她的房子，而乔决定买下它。首先，乔需要去他的银行签一份贷款合同。在我们的网络中，我们不直接创建贷款资产。交易合同贷款负责资产的创建。所以选择列表中的 ContractingLoan 事务，以及 */POST* 路线。要创建我们的贷款，给它以下数据:

```
{  "$class":  "org.acme.landregistry.ContractingLoan",  "debtor":  "org.acme.landregistry.PrivateIndividual#joe",  "bank":  "org.acme.landregistry.Bank#hsbc",  "realEstate":  "org.acme.landregistry.RealEstate#buildingOne",  "interestRate":  2.5,  "durationInMonths":  300  } 
```

Enter fullscreen mode Exit fullscreen mode

同样，您需要为这个事务指定一些关系。债务人是 Joe，所以我指定他的 id *joe* 。银行是*汇丰*，融资的房地产是*一号楼*。我选择了 300 个月(25 年)以上 2.5%的利率。

*注意*:记住给出关系中的参与者或资产 ID。所以= > *乔* **不是** *乔*。！

提交，如果您转到贷款项目并使用 */GET* 路线，您将看到您的新贷款。

接下来，我们来承包一份保险。概念是相同的，交易合同保险负责资产的创建。移动到此项，再次选择 */POST* 路线:

```
{  "$class":  "org.acme.landregistry.ContractingInsurance",  "insured":  "org.acme.landregistry.PrivateIndividual#joe",  "insuranceCompany":  "org.acme.landregistry.InsuranceCompany#axa",  "realEstate":  "org.acme.landregistry.RealEstate#buildingOne",  "monthlyCost":  150,  "durationInMonths":  12  } 
```

Enter fullscreen mode Exit fullscreen mode

再次，被保险人是*乔*。保险公司是*安盛*，房地产仍然是*一号楼*。我选择 150 英镑作为 12 个月的每月保险费。通过检查保险下的 */GET* 路径，提交并检查保险资产是否已经创建。

我们终于有了执行 BuyingRealEstate 事务的所有先决条件。转到所述事务，并为其提供以下数据:

```
{  "$class":  "org.acme.landregistry.BuyingRealEstate",  "buyer":  "org.acme.landregistry.PrivateIndividual#joe",  "seller":  "org.acme.landregistry.PrivateIndividual#sophie",  "realEstate":  "org.acme.landregistry.RealEstate#buildingOne",  "loan":  "org.acme.landregistry.Loan#joebuildingOnehsbc",  "realEstateAgent":  "org.acme.landregistry.RealEstateAgent#jenny",  "notary":  "org.acme.landregistry.Notary#ben",  "insurance":  "org.acme.landregistry.Insurance#joeaxabuildingOne"  } 
```

Enter fullscreen mode Exit fullscreen mode

同样的概念，我们通过添加适当的 id 来指定事务中的关系。*乔*是买方，*索菲*是卖方，*珍妮*是房产中介， *joebuildingOnehsbc* 是贷款的 id， *joeaxabuildingOne* 是保险的 id， *buildingOne* 是房产的 id， *ben* 是公证员的 id。

提交此交易时。您将看到 RealEstate 资产现在有了新的所有者 Joe。您还可以看到参与者的余额已经更新:

*   索菲的余额现在是 110000 英镑(她的 10000 英镑+房地产价格 100000 英镑)

*   乔的余额现在是 34850。(他的 50000 -公证费 10000 -房地产经纪人的费用 5000 -保险的第一个月 150)

*   公证人的余额现在是 10000 英镑

*   房产中介现在余额 5000。

很好，我们与业务网络和区块链进行了互动。现在，让我们创建一个简单的用户界面，它将与这个 REST API 交互并检索一些数据。

## 用 Composer 和 Fabric 创建简单的应用程序

我们不会做任何太花哨的东西，只是为了展示在 Hyperledger Composer 世界中使用业务网络是多么简单。

这个简单的应用程序将使用 create-react-app 工具构建。我们将能够做 4 件事，是吗？

*   获取并显示私人个人参与者
*   获取和显示房地产资产
*   发布和创建新的私人个人参与者
*   发布和创建新的房地产资产

当然，你可以用 REST API 做什么是没有限制的，我可以任意选择我们在这里使用的路径。

### 创建-反应-应用

首先，我们需要安装 create-react-app 工具:

`npm install -g create-react-app`

接下来，创建您的应用程序:

`create-react-app <APP_NAME>`

这将在您的 app 文件夹中创建一个 React 应用程序。你不需要设置任何东西。现在，我们需要改变一些东西来确保我们可以与 REST API 对话。

### 代理和端口

我们将从运行在 localhost:3000 中的 REST API 中检索数据。我们将在不同的端口运行我们的应用程序。为了确保我们的 React 应用程序到达正确的位置来获取数据并避免跨源问题，请转到您的 package.json 文件。

在该文件中，您只需添加一行:

`"proxy": "http://localhost:3000/"`

代理人到此为止。现在，在同一个文件中，在脚本对象中查找 start 命令。 *start* 命令现在应该是这样的:

`"start": "PORT=3001 react-scripts start"`

因为 REST API 将在端口 3000 上运行，这将简单地在端口 3001 上运行 React 应用程序，而不会在每次启动时询问我们不同的端口。

### 代码！

我试着让事情变得简单。只有原始代码，没有 CSS，没有花哨的东西。两个文件:App.js 和 Client.js。您将创建 Client.js 文件并添加这个:

```
function search(query, cb) {
    return new Promise( (resolve,reject) => {
        return fetch(`api/${query}`, {
            accept: "application/json"
        })
            .then(parseJSON)
            .then(data => resolve(data));
    })

}

function create(type, data){
    return new Promise((resolve, reject) => {
        return fetch(`api/${type}`, {
            headers: {
                'Accept': 'application/json',
                'Content-Type': 'application/json'
            },
            method: 'post',
            body: JSON.stringify(data)
        })
        .then(parseJSON)
        .then(() => resolve())
    })

}

function parseJSON(response) {
  return response.json();
}

const Client = { search, create };
export default Client; 
```

Enter fullscreen mode Exit fullscreen mode

这是什么？基本上，我们有两个功能:*搜索*和*创建*。函数 *search* 将处理我们的 GET 请求，函数 *create* 将处理我们的 POST 请求。如果您没有深入研究 REST API，那么每条路线都以前缀 */api* 开始。在我们的例子中，[http://localhost:3000/API/](http://localhost:3000/api/)，这是我们的前缀。我们在 package.json 中添加的代理键会将每个请求发送到正确的位置。我们只需要确保查询的其余部分是好的。

所以:

*   获取并发布 PrivateIndividual => `http://localhost:3000/api/PrivateIndividual`
*   获取并发布房地产=> `http://localhost:3000/api/RealEstate`

现在，让我们转到 App.js 文件:

```
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';
import Client from './Client'

class App extends Component {

    state = {
        privates: [],
        realEstate: []
    }

    componentWillMount = () => {
        this.getPrivateIndividual()
        this.getRealEstate()
    }

    getPrivateIndividual = () => {
        Client.search('PrivateIndividual')
        .then(data => {
            this.setState({
                privates: data
            })
        })
    }

    getRealEstate = () => {
        Client.search('RealEstate')
        .then(data => {
            this.setState({
                realEstate: data
            })
            for( let i = 0; i < this.state.realEstate.length; i++ ){
                let privateIndividual = this.state.realEstate[i].owner.split('#')[1]
                Client.search(`PrivateIndividual/${privateIndividual}`)
                    .then(data => {
                        let realEstate = this.state.realEstate
                        realEstate[i].ownerName = data.name
                        this.setState({
                            realEstate
                        })
                    })
            }
        })
    }

    handlePrivateInputChange = e => {
        const {name, value} = e.target
        this.setState({
            [name]: value
        })
    }

    submitPrivate = () => {
        const data = {
            "$class": "org.acme.landregistry.PrivateIndividual",
            "id": this.state.name.toLowerCase(),
            "name": this.state.name,
            "address": this.state.address,
            "balance": this.state.balance
        }

        Client.create('PrivateIndividual', data)
        .then(() => {
            this.getPrivateIndividual()
        })
    }

    handleRealEstateInputChange = e => {
        const { value, name } = e.target
        this.setState({
            [name]: value
        })
    }

    submitRealEstate = () => {
        const data =  {
            "$class": "org.acme.landregistry.RealEstate",
            "id": this.state.id,
            "address": this.state.realEstateAddress,
            "squareMeters": this.state.squareMeters,
            "price": this.state.price,
            "owner": `org.acme.landregistry.PrivateIndividual#${this.state.owner}`
        }

        Client.create('RealEstate', data)
        .then(() => {
            this.getRealEstate()
        })
    }

  render() {

    return (
      <div className="App">
                <div>
                    <h2>Add PrivateIndividual</h2>
                    <label>Name:</label>
                    <input
                        onChange={this.handlePrivateInputChange}
                        type='text'
                        name='name' />
                    <label>Address:</label>
                    <input 
                        type='text' 
                        onChange={this.handlePrivateInputChange}
                        name='address' />
                    <label>Balance</label>
                    <input 
                        type='number' 
                        onChange={this.handlePrivateInputChange}
                        name='balance' />
                    <button onClick={this.submitPrivate}>Submit New PrivateIndividual</button>
                    <h2>Add RealEstate</h2>
                    <label>ID:</label>
                    <input
                        onChange={this.handleRealEstateInputChange}
                        type='text'
                        name='id' />
                    <label>Address:</label>
                    <input 
                        type='text' 
                        onChange={this.handleRealEstateInputChange}
                        name='realEstateAddress' />
                    <label>Square Meters</label>
                    <input 
                        type='number' 
                        onChange={this.handleRealEstateInputChange}
                        name='squareMeters' />
                    <label>Price:</label>
                    <input 
                        type='number'
                        onChange={this.handleRealEstateInputChange}
                        name='price' />
                    <label>Owner</label>
                    <input 
                        type='text'
                        onChange={this.handleRealEstateInputChange}
                        name='owner' />
                    <button onClick={this.submitRealEstate}>Submit New RealEstate </button>
                    <div style={{display: 'flex', justifyContent: 'space-around'}}>
                        <div>
                            <h3>Private Individual</h3>                             {this.state.privates.map((p, i) => (
                                <div
                                    style={{border: '1px solid black'}} 
                                    key={i}>
                                    <p>Name: {p.name}</p>
                                    <p>Balance: {p.balance}</p>
                                </div>
                            ))}
                        </div>
                        <div>
                            <h3>Real Estate Assets</h3>
                        {this.state.realEstate.map((r, i) => (
                                <div
                                    key={i} 
                                    style={{border: '1px solid red'}}>
                                    <p>ID: {r.id}</p>
                                    <p>Address: {r.address}</p>
                                    <p>Price: {r.price}</p>
                                    <p>Owner: {r.ownerName}</p>
                                </div>
                        ))}
                    </div>
                </div>
      </div>
            </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

这里也没有什么疯狂的。HTML 由两个表单组成:一个创建新的 PrivateIndividual，另一个创建新的 RealEstate 资产。在这些下面，我们循环遍历我们拥有的数据，并创建简单的框来显示我们的资产和参与者。

在 *componentWillMount* 函数中，我们检索我们的数据。 *submitPrivate* 和 *submitRealEstate* 按照吩咐去做；) .注意我们发送给 API 的对象的形状。

### 运行 app

确保您的 REST API 正在运行。如果没有，重新运行`composer-rest-server`。然后，在 land-registry 文件夹中，用`npm start`运行 React 应用程序。

现在，你可以和你的私人区块链一起玩了！

*注意:*在 React 应用程序中，我跳过了错误处理、CSS 样式和事务的其他发布路径。这篇文章看起来足够长，我希望它足够清晰，这样你就可以自己处理这些事情了。玩得开心！