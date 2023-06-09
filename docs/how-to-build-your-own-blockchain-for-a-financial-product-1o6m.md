# 金融产品的区块链:为什么需要它以及如何构建它

> 原文：<https://dev.to/django_stars/how-to-build-your-own-blockchain-for-a-financial-product-1o6m>

> 技术变化很快；人不是。雅各布·尼尔森

区块链是一种相对较新的技术，许多人认为它仅用于购买比特币。他们试图在想到的任何领域实现它，无论是时尚、教育还是医疗保健。我会说这没问题——确定人类活动的哪个领域可以从应用这项技术中受益最多的时间还太少。为了理解区块链的实际应用，我们必须首先定义它为什么会出现，然后研究区块链在什么情况下可以产生重大影响。

**注意:**本文不解释区块链概念；相反，它专注于使用这种技术开发金融科技应用程序。我将解释为什么 fintech 已经可以采用区块链，最重要的是，专注于使用这种技术开发去中心化的应用程序。

## 为区块链做好准备的行业

唐·诺曼(Don Norman)曾写道，许多产品之所以失败，是因为它们在错误的时间发布。我可以改写这句话，说:许多技术未能找到实际应用。当互联网在 90 年代初变得广泛可用时，每个领域都试图将它应用到他们的业务中。这是一场灾难，它的后果仍然可见于数以千计从未访问过的网站，这些网站有着可怕的界面，是由任何一个有电脑的人笨拙地创建的。我们目前正在目睹几乎相同的情况——十年来最有潜力的技术与秘密交易的投机有关。它被广泛用于金融诈骗，尽管它最初是为了相反的目的而创建的。

[![industries-using-blockc](img/3380a7b28ce1cdcdb5d8b8a94873519c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mr6tIjW1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://djangostars.com/blog/conteimg/2018/08/Img-1.png) 
*来源:[微软 Azure](https://azure.microsoft.com/en-us/blog/accelerating-the-adoption-of-enterprise-blockchain/)*

试图将人的因素排除在商业之外是区块链出现的原因之一。这就是为什么可能成功实施区块链的行业是那些(1)严重依赖人类活动的行业，以及(2)受人为错误影响最大的行业，比如金融。

**重要:**区块链正在应用于不同行业的各种产品；我们只是需要更多敢于冒险的企业家。

金融科技处理的是一个非常棘手的问题——钱。这正是大多数欺诈发生的地方。变得更富有的欲望是推动人们做事的一个基本机制，通常是坏事。金融科技创业公司旨在改善传统的金融机构，例如，从金融活动中排除人的因素。

利用区块链将第三方排除在金融交易之外，就像银行验证进行交易的人一样。它可以用于管理库存和物流、交易商品、优化人员识别、跟踪交易等。

> *阅读:[打造 fintech 产品之前需要考虑什么](https://djangostars.com/blog/what-you-need-to-consider-before-building-a-fintech-product/)*

这并不意味着每个金融科技产品都可以轻松采用区块链。以下是一些可能需要使用区块链的情况:

*   你想要吸引投资。不管你喜不喜欢，区块链仍然是一个时髦词。它比真正的工作产品吸引更多的投资。
*   你想增加你在市场上的竞争力。如果你成功地在区块链打造了一个产品，你将立刻展现出你的专业性，从而对投资者和客户更具吸引力。
*   你已经准备好进行实验了。是的，任何基于区块链的产品都是试验品，因为很少有人知道这项技术能做什么。如果你准备在你的行业取得突破，区块链可能是一个正确的选择。

我不建议在以下情况下实施区块链:

*   你的资源有限。这是一种高风险的产品创造方式，因为(1)区块链的工程师很少，而且(2)团队中有他们的成本很高。
*   你还没有为重大的改变做好准备。变化包括运营管理和人力资源。如果你是一家实施了区块链的银行，你很可能需要解雇很多员工，因为员工的工作量会减少。
*   你目光短浅。区块链讲的是长远眼光。不是一个月左右就能实施的。除非你有一个长期的产品路线图，否则不要梦想明天改变一切。

明天，甚至在阅读这篇文章的时候，你能做的就是建造一个简单的区块链。这是第 2 部分的重点。我将告诉你为金融科技产品建立区块链所需的主要组件，提出一些工具，并展示真实的代码片段和解释。

## 如何在 Fintech 中应用区块链

### 【框架】使用

#### 隐注

CryptoNote 是一个开源项目，允许您创建加密硬币。他们有一个简单的、一步一步的创建加密货币的指南。要启动它，您需要有两个用于运行 Monero 服务器的节点。

**有用链接:**

[如何创造硬币](https://cryptonotestarter.org/inner.html)

[如何创建钱包](https://cryptonotestarter.org/create-wallet.html)

#### 以太坊

一个流行的开放式软件平台，用于构建分散的应用程序。它的重点是运行你的区块链应用程序的编程代码。引用以太坊网站:*“以太坊是一个运行智能合约的去中心化平台:应用程序完全按照编程运行，没有任何宕机、审查、欺诈或第三方干扰的可能性。”*

#### 零网

ZeroNet 用于创建分散的网站。它使用比特币寻址和验证机制，以及 BitTorrent 分布式内容交付网络来创建无法审查、伪造或屏蔽的网站。

### 建造简单的区块链

现在你已经知道了工具——crypto note、Ethereum 和 zero net——我们正在着手构建我们自己的基本区块链。在这个例子中，我将使用 Python，但是如果它不是您的主要编码语言，您仍然可以理解其中的逻辑，并且能够用另一种语言编写它。

首先，我将解释构建一个模块所需的基本元素。我将从创建日期、随机数、校验和以及交易数据开始。在我们的例子中，事务数据可以只是一个字符串，以简化代码。

#### 创建日期

它是 unix 格式的当前和时间。这是你们区块链未来发展的需要；当有许多正在运行的节点，并且您向分支添加了一个新块时，该节点将根据创建日期决定使用哪个块。

#### Nonce

这是一组独特的符号，我们需要将其添加到块中，以构建符合要求的校验和。例如，如果 nonce 值是 5，那么我们必须向数据块添加 5 个零(00000)来计算正确的校验和。

#### Checksum

有时也称为哈希值、哈希代码或简称为哈希。它是具有 nonce 和前一个块的校验和的块数据。SHA256 保护分类帐链不被重写。工作原理:节点计算校验和，并与新块的校验和进行比较；如果匹配，块将被添加到区块链。

#### 数据

这是一组数据，将存储在块中并进行签名。它可以包含任何类型的数据:例如，比特币存储交易列表，而不仅仅是最后一次交易；或者你可以存储创建这个块的计算机的信息，比如它的 MAC 地址；或者您可以有一个更详细的创建日期，比如说，添加时区。

#### 工作证明

工作证明(PoW)是区块链网络中唯一的一致性算法。它用于验证区块链网络中的操作和新链的创建。PoW 的主要思想是增加客户端构建块的复杂性，减少服务器端的负载。例如，我说校验和必须有 5 个前导零；这意味着我们将增加 nonce，直到校验和没有 5 个前导零。

### 先说一个代码

首先，我将为一个块创建一个类。这将是一个非常简单的类，具有构造函数、计算校验和的方法以及检查块是否有效的属性。我们将有两个常量，一个用于校验和中前导零的数量，另一个用于标识我们将在 nonce 中使用的符号。

```
import time
from hashlib import sha256

class Block:
    CHECKSUM_LEAD_ZEROS = 5
    NONCE_SYMBOL = 'Z'

    def __init__(self, prev_block, data):
        self._prev_block = prev_block
        self.data = data
        self.checksum = None
        self.nonce = 0
        self.timestamp = time.time()

    @property
    def is_valid(self):
        checksum = self.calculate_checksum()

        return (
            checksum[:self.CHECKSUM_LEAD_ZEROS] == '0' * self.CHECKSUM_LEAD_ZEROS
            and checksum == self.checksum
        )

    def calculate_checksum(self):
        data = '|'.join([
            str(self.timestamp),
            self.data,
            self._prev_block.checksum,
        ])
        data += self.NONCE_SYMBOL * self.nonce

        return sha256(bytes(data, 'utf-8')).hexdigest() 
```

Enter fullscreen mode Exit fullscreen mode

##### *构造函数*

构造函数只接受两个参数；第一个是前一个块，第二个是当前块数据。此外，构造函数创建时间标记并将 nonce 设置为零作为其初始值。

##### *有效*

一种属性，用于计算校验和并比较当前校验和是否等于计算出的校验和，以及是否具有正确的零个数。

##### *计算校验和*

我们代码中最复杂的方法。该方法将前一个块的时标、数据和校验和打包成一个字符串。然后我们添加一个 nonce 字符串；在我们的例子中，它将是一个“Z”的列表。然后，它计算结果字符串的校验和。

现在我们有了一个简单但功能齐全的模块。我将继续创建一个区块链。目前，它将是一个简单的链，没有存储和加载数据的能力，但它将传达主要思想。

```
import json

class Chain:

    def __init__(self):
        self._chain = [
            self._get_genesis_block(),
        ]

    def is_valid(self):
        prev_block = self._chain[0]
        for block in self._chain[1:]:
            assert prev_block.checksum == self._prev_block.checksum
            assert block.is_valid()
            prev_block = block

    def add_block(self, data):
        block = Block(self._chain[-1], data)
        block = self._find_nonce(block)
        self._chain.append(block)

        return block

    @staticmethod
    def _get_genesis_block():
        genesis_block = Block(None, None)
        genesis_block.checksum = '00000453880b6f9179c0661bdf8ea06135f1575aa372e0e70a19b04de0d4cbc7'

        return genesis_block

    @staticmethod
    def _find_nonce(block):
        beginning = '0' * Block.CHECKSUM_LEAD_ZEROS
        while True:
            checksum = block.calculate_checksum()
            if checksum[:Block.CHECKSUM_LEAD_ZEROS] == beginning:
                break
            block.nonce += 1

        block.checksum = checksum

        return block 
```

Enter fullscreen mode Exit fullscreen mode

**让我们看看我们的链类中的方法:**

##### *构造函数*

我刚刚创建了一个只有一个块的链——一个创世纪块。起源块是链的第一块，并且只有校验和。将第一个实际块添加到链中需要这个块，因为实际块需要链中最后一个块的校验和。

##### *添加新块*

它只有一个参数—新块的数据。此方法使用给定的数据创建一个新块，并运行方法来查找正确的 nonce 值。只有这样，它才会向链中添加一个新的块。

##### *找到随机数*

该方法旨在为块找到正确的随机数。它有一个无限循环，我增加 nonce 并计算一个新的校验和。然后，它将校验和与规则进行比较——目前，它只是零的数量。

##### *验证链条*

这个方法告诉我们这个链是否有效。它遍历链中的所有块，并检查每个块是否有效。

### 底线

在本文中，我试图证明构建一个简单而有效的区块链并不像看起来那么困难。我的总体建议是，吸取您在这里学到的经验，通过对块和数据进行实验，开始玩区块链。所有伟大的产品，包括区块链，都曾经是实验品。

如果你来自金融科技行业，我建议你多研究一下使用区块链的产品。关于他们的一些事情是确定的；它们更安全，对投资更有吸引力，如果它们在全球市场取得成功，将被称为游戏规则改变者。采用新技术的第一步已经迈出。下一步是传播知识，让人们了解区块链的特色。

请继续关注关于区块链和金融科技的下一部分，很可能是更复杂的代码片段和关于其在金融科技中的实际应用的建议。

这篇文章是瓦迪姆·扎科文科写的。这篇关于金融产品区块链的文章最初发表在 Django Stars 博客上。您还可以访问我们的内容平台[产品部落](https://producttribe.com/)，它是由专业人士为那些参与产品开发和成长过程的人创建的。

我们随时欢迎您提出问题，分享您想阅读的话题！