# 人类以太坊交易详情

> 原文：<https://dev.to/mandarvaze/ethereum-transaction-details-for-humans-5h09>

*这篇文章最初发表在我的博客上，[这里。](https://learnings.desipenguin.com/post/view-eth-txn-details/)T3】*

在从事我的一个项目时，有人问“我们如何确保交易确实记录在区块链上”

虽然我们可以(也确实)将函数调用返回的事务散列存储在数据库中，但是单独使用`txn_hash`并没有什么好处。

例如，获取交易详细信息的“官方”方法(从`txn_hash`)会返回类似下面的内容:

```
web3.eth.getTransaction('0x9fc76417374aa880d4449a1f7f31ec597f00b1f6f3dd2d66f4c9c6c445836d8b§234')
.then(console.log);

> {
    "hash": "0x9fc76417374aa880d4449a1f7f31ec597f00b1f6f3dd2d66f4c9c6c445836d8b",
    "nonce": 2,
    "blockHash": "0xef95f2f1ed3ca60b048b4bf67cde2195961e0bba6f70bcbea9a2c4e133e34b46",
    "blockNumber": 3,
    "transactionIndex": 0,
    "from": "0xa94f5374fce5edbc8e2a8697c15331677e6ebf0b",
    "to": "0x6295ee1b4f6dd65047762f924ecd367c17eabf8f",
    "value": '123450000000000000',
    "gas": 314159,
    "gasPrice": '2000000000000',
    "input": "0x57cb2fc4"
} 
```

即使对开发者来说，这也没有多大意义。当然`from`和`to`地址提供了一些有用的数据，但是我们不知道执行了什么“操作”以及使用了什么数据。(数据在上面显示的`input`字段中，但“按原样”没有意义)

如果您能看到类似下面这样的东西，会怎么样呢？

```
{ name: 'mintToken',
  types: [ 'address', 'uint256' ],
  inputs: [ '5bb4b21e60d0033a1b86b83e4a1f8307ab2d01f9', <BN: 64> ] } 
```

您得到的是函数名，而不是地址，以及传递给函数的参数——这可能“有点”有用，这取决于参数的类型。

例如，在上面的例子中，第一个输入参数是地址，第二个是数字。

很容易猜测(从函数名)我们正在为一个给定的地址铸造 0x64，即 100 个令牌。

那不是好多了吗？

得到这种“人类可读”的输出是由`ethereum-input-data-decoder`库实现的——它完成了*繁重的工作*

我添加了一些包装器功能，使其尽可能通用，而不必修改代码。

您可以将`txn_hash`和由`truffle compile`创建的 JSON 文件的路径作为参数传递给脚本。

因此，它适用于任何合同。

* * *

你可以在这里看到源代码

如果你有任何疑问(关于代码),请随意提出 github 问题(或者在评论中提问)