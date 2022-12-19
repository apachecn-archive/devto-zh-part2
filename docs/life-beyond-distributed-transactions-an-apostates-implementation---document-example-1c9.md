# 分布式事务之外的生活:一个叛教者的实现-文档示例

> 原文：<https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---document-example-1c9>

此系列中的帖子:

*   [一个引子](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---a-primer-2po6-temp-slug-8261948)
*   [文件协调](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---document-coordination-leb-temp-slug-1009285)
*   [文档示例](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-document-example/)
*   [调度示例](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-dispatching-example/)
*   [失败和重试](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-failures-and-retries/)
*   [故障恢复](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-dispatcher-failure-recovery/)
*   [传奇故事](https://jimmybogard.com/life-beyond-distributed-transactions-sagas/)
*   [关系资源](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-relational-resources/)

[本系列的示例代码](https://github.com/jbogard/adventureworkscosmos)

在上一篇文章中，我走过了多个资源之间协调通信/活动的“快乐之路”场景，否则这些资源无法参与事务。在这篇文章中，我将介绍一个在 [Azure Cosmos DB](https://azure.microsoft.com/en-us/services/cosmos-db/) 中构建文档协调的代码示例。我的起点是这组用于批准发票和更新库存的代码:

```
[HttpPost]
public async Task<IActionResult> Approve(Guid id)  
{
    var orderRequest = await _orderRepository.GetItemAsync(id);

    orderRequest.Approve();

    await _orderRepository.UpdateItemAsync(orderRequest);

    foreach (var lineItem in orderRequest.Items)
    {
        var stock = (await _stockRepository
            .GetItemsAsync(s => s.ProductId == lineItem.ProductId))
            .FirstOrDefault();

        stock.QuantityAvailable -= lineItem.Quantity;

        await _stockRepository.UpdateItemAsync(stock);
    }

    return RedirectToPage("/Orders/Show", new { id });
} 
```

当您在 Azure 门户中下载一个示例应用程序时，我的示例中的存储库直接来自示例代码，只需包装底层的 [DocumentClient](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet) 。

### 建模我们的文档

首先，我们需要确定文档信息的基线。这些对象可以是 POCOs，但是我们仍然需要一些基本信息。因为我们想要执行幂等操作，所以我们需要能够区分不同的消息。最简单的方法是为每条消息使用一个唯一的标识符:

```
public interface IDocumentMessage  
{
    Guid Id { get; }
} 
```

因为我们的文档需要存储和处理收件箱/发件箱中的消息，所以我们需要构建我们的基本文档类来包含这些项目。我们还可以为收件箱/发件箱构建一个完全独立的对象，但是为了简单起见，我们将只使用一个基类:

```
public abstract class DocumentBase  
{
    [JsonProperty(PropertyName = "id")]
    public Guid Id { get; set; }

    private HashSet<IDocumentMessage> _outbox 
        = new HashSet<IDocumentMessage>(DocumentMessageEqualityComparer.Instance);
    private HashSet<IDocumentMessage> _inbox
        = new HashSet<IDocumentMessage>(DocumentMessageEqualityComparer.Instance);

    public IEnumerable<IDocumentMessage> Outbox
    {
        get => _outbox;
        protected set => _outbox = value == null
            ? new HashSet<IDocumentMessage>(DocumentMessageEqualityComparer.Instance)
            : new HashSet<IDocumentMessage>(value, DocumentMessageEqualityComparer.Instance);
    }

    public IEnumerable<IDocumentMessage> Inbox
    {
        get => _inbox;
        protected set => _inbox = value == null
            ? new HashSet<IDocumentMessage>(DocumentMessageEqualityComparer.Instance)
            : new HashSet<IDocumentMessage>(value, DocumentMessageEqualityComparer.Instance);
    } 
```

我们的每个邮箱都是一个 [HashSet](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.hashset-1?view=netframework-4.7.2) ，以确保我们在文档中强制文档消息的唯一性。出于存储目的，我们将邮箱包装在几个方便的属性中(因为我们的文档是使用 JSON.NET 序列化的，所以我们必须为其序列化需求建立适当的模型)。

我们基于之前添加的接口和 ID 为文档消息使用了一个定制的等式比较器:

```
public class DocumentMessageEqualityComparer  
    : IEqualityComparer<IDocumentMessage>
{
    public static readonly DocumentMessageEqualityComparer Instance
        = new DocumentMessageEqualityComparer();

    public bool Equals(IDocumentMessage x, IDocumentMessage y)
    {
        return x.Id == y.Id;
    }

    public int GetHashCode(IDocumentMessage obj)
    {
        return obj.Id.GetHashCode();
    }
} 
```

这样，我们可以确保我们的文档消息只在收件箱/发件箱中存在一次(假设我们可以选择唯一的 GUIDs)。

接下来，我们需要能够在我们的`DocumentBase`类中发送消息:

```
protected void Send(IDocumentMessage documentMessage)  
{
    if (_outbox == null)
        _outbox = new HashSet<IDocumentMessage>(DocumentMessageEqualityComparer.Instance);

    _outbox.Add(documentMessage);
} 
```

我们必须检查发件箱是否存在，如果不存在就创建它(由于序列化，它可能不存在)，然后简单地将文档消息添加到发件箱中。

为了处理文档消息，我们需要确保这个动作是等幂的。为了检查幂等性，我们将在执行动作之前检查我们的`Inbox`。我们可以用一个方法来完成所有这些，我们的派生文档将使用这个方法:

```
protected void Process<TDocumentMessage>(  
    TDocumentMessage documentMessage, 
    Action<TDocumentMessage> action)
    where TDocumentMessage : IDocumentMessage
{
    if (_inbox == null)
        _inbox = new HashSet<IDocumentMessage>(DocumentMessageEqualityComparer.Instance);

    if (_inbox.Contains(documentMessage))
        return;

    action(documentMessage);

    _inbox.Add(documentMessage);
} 
```

我们的派生文档需要调用这个方法来处理带有幂等检查的消息。邮件处理成功后，我们会将其添加到收件箱中。因为我们的事务边界是文档，所以如果有什么东西失败了，这个动作就不会发生，消息也不会存储到收件箱中。只有将我们的收件箱、发件箱和业务数据保存在事务边界内，我们才能保证所有数据要么成功，要么失败。

### 重构我们的行动

现在我们已经有了存储和处理消息的基本机制，我们可以重构我们最初的操作了。它基本上分为两个动作——一个是批准发票，另一个是更新库存。

我们需要从订单向库存“发送”一条信息。但是这个信息应该是什么样的呢？几个选项:

*   命令，“更新库存”
*   事件，“订单批准”
*   事件，“购买的项目”

如果我使用一个命令，我就把主要动作和预期的副作用联系起来。但是如果这个副作用需要改变呢？被除名？我不想给主订单逻辑增加负担。

第一个事件“订单批准”呢？我可以同意这一点——但是从所做的工作和交流来看，Stock 并不关心订单是否被批准，它只关心一个商品是否被购买。批准实际上是订单的内部业务规则，但最终的副作用是项目最终在这个时间点变为“已购买”。因此，如果我使用“订单批准”，我将把股票与订单的内部业务规则联系起来。尽管这是一个事件，“订单批准”涉及其他文档/服务不应该关心的内部业务流程。

最后，我们有“购买的项目”。这最符合 Stock 所关心的，并且消除了这两个集合之间的任何类型的过程耦合。如果我使用宏事件“order approved”，我仍然需要将它转换为股票的含义。

考虑到这一点，我将创建一个表示该事件的文档消息:

```
public class ItemPurchased : IDocumentMessage  
{
    public int ProductId { get; set; }
    public int Quantity { get; set; }
    public Guid Id { get; set; }
} 
```

我知道购买了多少产品，这足以让 Stock 处理该事件的后果。

然后，我的`Order`类对它的`Approve`方法建模，以包括发送这些新消息:

```
public void Approve()  
{
    Status = Status.Approved;
    foreach (var lineItem in Items)
    {
        Send(new ItemPurchased
        {
            ProductId = lineItem.ProductId,
            Quantity = lineItem.Quantity,
            Id = Guid.NewGuid()
        });
    }
} 
```

这里我没有幂等检查(如果订单已经被批准，什么也不做)，但是您已经明白了。

在股票方面，我需要添加一个方法来处理`ItemPurchased`消息:

```
public void Handle(ItemPurchased message)  
{
    Process(message, e =>
    {
        QuantityAvailable -= e.Quantity;
    });
} 
```

最后，我们需要某种方式将我们的`ItemPurchased`消息与`Stock`链接起来，这就是`IDocumentMessageHandler`的目的:

```
public interface IDocumentMessageHandler<in T>  
    where T : IDocumentMessage
{
    Task Handle(T message);
} 
```

加载每个`Stock`的动作部分是我们将放入处理程序的代码:

```
public class UpdateStockFromItemPurchasedHandler  
    : IDocumentMessageHandler<ItemPurchased>
{
    private readonly IDocumentDBRepository<Stock> _repository;

    public UpdateStockFromItemPurchasedHandler(
        IDocumentDBRepository<Stock> repository) 
        => _repository = repository;

    public async Task Handle(ItemPurchased message)
    {
        var stock = (await _repository
            .GetItemsAsync(s => s.ProductId == message.ProductId))
            .Single();

        stock.Handle(message);

        await _repository.UpdateItemAsync(stock);
    }
} 
```

这并不令人兴奋，因为我们的文档将处理处理请求的实际业务逻辑。这个类只是连接了一个`IDocumentMessageHandler`和某个`DocumentBase`实例之间的点。

有了这些基本的构件，我们将修改我们的操作，只更新`Order`实例:

```
[HttpPost]
public async Task<IActionResult> Approve(Guid id)  
{
    var orderRequest = await _orderRepository.GetItemAsync(id);

    orderRequest.Approve();

    await _orderRepository.UpdateItemAsync(orderRequest);

    return RedirectToPage("/Orders/Show", new { id });
} 
```

现在，当我们批准订单时，我们只在发件箱中创建消息，这些消息随订单一起保存。如果我在 Cosmos DB 中查看保存的订单，我可以验证这些项目是持久的:

```
{
    "Items": [
        {
            "Quantity": 1,
            "ListPrice": 3399.99,
            "ProductId": 771,
            "ProductName": "Mountain-100 Silver, 38",
            "Subtotal": 3399.99
        }
    ],
    "Status": 2,
    "Total": 3399.99,
    "Customer": {
        "FirstName": "Jane",
        "LastName": "Doe",
        "MiddleName": "Mary"
    },
    "id": "8bf4bda2-3796-431e-9936-8511243352d2",
    "Outbox": {
        "$type": "System.Collections.Generic.HashSet`1[[AdventureWorksCosmos.UI.Infrastructure.IDocumentMessage, AdventureWorksCosmos.UI]], System.Core",
        "$values": [
            {
                "$type": "AdventureWorksCosmos.UI.Models.Orders.ItemPurchased, AdventureWorksCosmos.UI",
                "ProductId": 771,
                "Quantity": 1,
                "Id": "987ce801-e7cf-4abf-aba7-83d7eed00610"
            }
        ]
    },
    "Inbox": {
        "$type": "System.Collections.Generic.HashSet`1[[AdventureWorksCosmos.UI.Infrastructure.IDocumentMessage, AdventureWorksCosmos.UI]], System.Core",
        "$values": []
    },
    "_rid": "lJFnANVMlwADAAAAAAAAAA==",
    "_self": "dbs/lJFnAA==/colls/lJFnANVMlwA=/docs/lJFnANVMlwADAAAAAAAAAA==/",
    "_etag": "\"02002652-0000-0000-0000-5b48f2140000\"",
    "_attachments": "attachments/",
    "_ts": 1531507220
} 
```

为了获得我的`IDocumentMessage`集合的多态行为，我需要在我的存储库中配置 JSON 序列化程序设置:

```
_client = new DocumentClient(new Uri(Endpoint), Key,  
    new JsonSerializerSettings
    {
        TypeNameHandling = TypeNameHandling.Auto
    }); 
```

有了这些部分，我就消除了更新订单状态和使用文档消息更新库存项目之间的流程耦合。当然，我们实际上没有任何东西*发送*我们的消息。我们将在下一篇文章中讨论发送消息的基础设施。