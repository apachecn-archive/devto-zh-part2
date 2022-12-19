# Swift 中的异步对象初始化

> 原文：<https://dev.to/hugh_jeremy/asynchronous-object-initialisation-in-swift-1394>

小鸟、火箭、新鲜烘焙的咖啡豆，还有…不变的物体。所有这些东西有什么共同点？我爱他们。

不可变对象是指在初始化后不能改变的对象。它没有可变属性。这意味着当在程序中使用它时，我的豌豆大脑不必对对象的状态进行推理。它要么存在，完全准备好完成它的职责，要么不存在。

异步编程对不可变对象提出了挑战。如果创建一个对象需要网络 I/O，那么在我们决定创建该对象之后，我们将不得不取消阻止执行。

作为一个例子，让我们考虑阿马蒂诺·斯威夫特内部的 [`Transaction`](https://github.com/amatino-code/amatino-swift/blob/master/Sources/Amatino/Transaction.swift) 类。 [Amatino 是一个复式记账 API](https://amatino.io) ，而 [Amatino Swift](https://github.com/amatino-code/amatino-swift) 允许 macOS & iOS 开发者在他们的应用中构建财务功能。

为了允许开发人员构建丰富的用户界面，关键是`Transaction`操作是平滑异步的。当 Amatino API 响应时，我们不能阻止呈现界面！为了降低 Amatino Swift 所要求的认知负荷，事务应该是不可变的。

我们将使用简化版的`Transaction`，它只包含两个属性:`transactionTime`和`description`。让我们从简单的同步情况扩展到成熟的异步情况。

```
class Transaction {
  let description: String
  let transactionTime: Date 

  init(description: String, transactionTime: Date) {
    self.description = description
    self.transactionTime = transactionTime
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，很明显。我们可以立即初始化`Transaction`。在现实生活中，`Transaction`不是用逐段值初始化的，而是从 HTTP 请求接收的解码 JSON 数据初始化的。JSON 可能看起来像这样:

```
{  "transaction_time":  "2008-08",  "description":  "short Lehman Bros. stock"  } 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将 JSON 解码成我们的`Transaction`类，就像这样:

```
/* Part of Transaction definition */
enum JSONObjectKeys: String, CodingKey {
  case txTime = "transaction_time"
  case description = "description"
}

init(from decoder: Decoder) throws {
  let container = try decoder.container(
    keyedBy: JSONObjectKeys.self
  )
  description = try container.decode(
    String.self,
    forKey: .description
  )
  let dateFormatter = DateFormatter()
  dateFormatter.dateFormat = "yyyy-MM" //...
  let rawTime = try container.decode(
    String.self,
    forKey: .txTime
  )
  guard let txTime: Date = dateFormatter.date(
    from: rawTime
  ) else {
    throw Error
  }
  transactionTime = txTime
  return
} 
```

Enter fullscreen mode Exit fullscreen mode

哇哦！刚刚发生了什么！我们将一个 JSON 对象解码成一个不可变的 Swift 对象。不错！这是激烈的，所以让我们休息一下，看看可爱的小鸟:

[https://www.youtube.com/embed/JQ_NV6Cf9qA](https://www.youtube.com/embed/JQ_NV6Cf9qA)

休息时间结束了！回到这个问题:假设在我们的应用程序中，我们想要创建一个`Transaction`的实例。也许用户已经点击了界面中的“保存”。因为 Amatino API 将(取决于地理位置)花费大约 50 毫秒来响应，所以我们需要执行异步初始化。

我们可以通过给我们的`Transaction`类一个静态方法来做到这一点，就像这个:

```
/* Part of Transaction definition */
static func create(
  description: String,
  transactionTime: Date,
  callback: @escaping (Error?, Transaction?) -> Void
) throws {
  /* dummyHTTP() stands in for whatever HTTP request
     machinery you use to make an HTTP request. */
  dummyHTTP() { (data: Data?, error: Error?) in
    guard error == nil else { 
      callback(error, nil)
      return
    }
    guard dataToDecode: Data = data else {
      callback(Error(), nil)
      return
    }
    let transaction: Transaction
    guard transaction = JSONDecoder().decode(
      Transaction.self,
      from: dateToDecode
    ) else {
      callback(Error(), nil)
      return
    }
    callback(nil, transaction)
    return
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这种新的`Transaction.create()`方法遵循以下步骤:

1.  接受新事务的参数，以及该事务可用时要调用的函数`callback(Error?:Transaction?)`。因为可能会出错，这个函数可能会收到一个错误，(`Error?`)或者收到一个事务(`Transaction?`)

2.  发出 HTTP 请求，接收可选数据和返回的错误，这些数据和错误在闭包中使用。在这个例子中，`dummyHTTP()`代表您用来发出 HTTP 请求的任何机器。例如，查看[苹果在 Swift](https://developer.apple.com/documentation/foundation/url_loading_system/fetching_website_data_into_memory) 中进行 HTTP 请求的指南

3.  查找是否存在错误或缺少数据，如果找到，则回调这些错误:callback(error，nil)

4.  尝试解码`Transaction`的新实例，如果成功，则使用该事务进行回调:`callback(nil, transaction)`

我们可以像这样使用`.create()`静态方法:

```
Transaction.create(
  description: "Stash sweet loot",
  transactionTime: Date(),
  callback: { (error, transaction) in 
    // Guard against errors, then do cool stuff
    // with the transaction
}) 
```

Enter fullscreen mode Exit fullscreen mode

最终结果？不可变的对象。我们不需要考虑它是否被完全初始化，它要么存在，要么不存在。考虑一个替代方案，其中`Transaction`类跟踪内部状态:

```
class Transaction {
  var HTTPRequestInProgress: bool
  var hadError: Bool? = nil
  var description: String? = nil
  var transactionTime: Date? = nil

  init(
    description: String,
    transactionTime: Date,
    callback: (Error?, Transaction?) -> Void
  ) {
    HTTPRequestInProgress = true
    dummyHTTP() { data: Data?, error: Error? in 
       /* Look for errors, try decoding, set
          `hadError` as appropriate */
       HTTPRequestInProgress = false
       callback(nil, self)
       return
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们必须思考各种新的可能性。我们是在试图利用一个还没有准备好的`Transaction`吗？当我们使用一个表面上已经准备好的`Transaction`时，我们防范过零吗？沿着这条路，是一堆杂乱的`guard`语句、`if-else`从句和悲伤的小鸟。

不要让小鸟难过，异步初始化不可变对象！💕

### 进一步阅读

*   Amatino Swift `Transaction`类的[完整源代码，它是不可变的，大量使用异步操作](https://github.com/amatino-code/amatino-swift/blob/master/Sources/Amatino/Transaction.swift)
*   苹果指南[在 Swift 中进行异步 HTTP 请求](https://developer.apple.com/documentation/foundation/url_loading_system/fetching_website_data_into_memory)
*   苹果的[编码指南&在 Swift 中解码 JSON 数据](https://developer.apple.com/documentation/foundation/archives_and_serialization/encoding_and_decoding_custom_types)

–[休](https://twitter.com/hugh_jeremy)

*原贴于[hughjeremy.com](https://hughjeremy.com/blog/asynchronous-object-initialisation-in-swift/)T3】*