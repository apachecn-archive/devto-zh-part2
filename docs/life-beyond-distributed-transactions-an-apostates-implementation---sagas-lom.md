# 分布式事务之外的生活:叛教者的实现

> 原文：<https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---sagas-lom>

此系列中的帖子:

*   [一个引子](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---a-primer-2po6-temp-slug-8261948)
*   [文件协调](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---document-coordination-leb-temp-slug-1009285)
*   [文档示例](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---document-example-4fpk-temp-slug-5917155)
*   [调度示例](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---dispatching-example-31i6-temp-slug-684211)
*   [失败和重试](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---failures-and-retries-m6-temp-slug-2730961)
*   [故障恢复](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---dispatcher-failure-recovery-3m38-temp-slug-5793122)
*   [传奇故事](https://jimmybogard.com/life-beyond-distributed-transactions-sagas/)
*   [关系资源](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-relational-resources/)

[本系列的示例代码](https://github.com/jbogard/adventureworkscosmos)

到目前为止，在本系列中，我们已经了解了使用持久化消息作为不同文档(或资源)之间的协调手段来超越分布式事务的来龙去脉。在我给出的例子中，一个常见的问题是“我如何确保两个操作都发生或者都不发生？”要回答这个问题，我们需要认识到这种“全有或全无”的方法是一种交易。但是我们已经说过我们正在努力避免分布式事务！

我们将不会构建一种新的分布式事务，而是一种比任何单个请求都更长寿的事务，或者说是一种长寿事务。要实现一个长寿命的事务，我们需要看看 Saga 模式，最初在[原始 Sagas 论文(Molina，Salem)](https://www.cs.cornell.edu/andru/cs711/2002fa/reading/sagas.pdf) 中描述。我见过的最常见的描述传奇的例子是预订假期。预订假期时，您需要:

*   预订航班
*   预订酒店
*   预订汽车

你不可能同时做这三件事——这就像召集所有公司开一个电话会议，然后达成一致意见。不会发生的！相反，我们将整个业务事务构建为一系列请求和补偿操作，以防出错:

*   取消航班
*   取消酒店
*   取消汽车预订

我们的 saga 操作可以是线性的([控制器模式](https://lostechies.com/jimmybogard/2013/03/14/saga-implementation-patterns-controller/))或并行的([观察者模式](https://lostechies.com/jimmybogard/2013/03/11/saga-implementation-patterns-observer/))，或者用微服务术语来说，[编排/编排](https://microservices.io/patterns/data/saga.html)。

为了满足我们的 saga 约束，我们的请求必须:

*   幂等
*   可以中止

并且补偿请求必须:

*   幂等
*   无法中止
*   可交换的

在我们的例子中，我们有一个基本假设成功的模型。我们:

*   批准订单
*   扣除股票

让我们稍微修改一下，创建一个订单执行传奇。对于这个传奇，我们可以履行订单，当且仅当:

*   我们的订单被批准了
*   我们有足够的存货

如果我们的订单被拒绝，我们需要释放库存。如果我们没有足够的库存，我们需要取消批准(拒绝)我们的订单。和我们的例子一样，我们需要一些东西来协调这个活动——我们的故事。但与其称之为一般的“传奇”，不如给它起个有意义的名字——`OrderFulfillmentSaga`:

[![](../Images/281e6bb132ae296efcc382862a2cf2a2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Z283MXAr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/8/2018/Picture0055.png)

这个 saga 将协调订单请求和库存的活动。因为我们需要这个 saga 具有与其他两个文档相同的通信属性，所以我们可以简单地将这个 saga 建模为带有收件箱/发件箱的另一个文档！

总流量将为:

*   一旦创建了订单，就开始了新的订单执行传奇
*   这个系列将协调库存和订单请求之间的行动
*   如果订单被拒绝，saga 需要启动股票退货
*   如果没有足够的库存，佐贺需要取消订单

让我们从揭开传奇开始吧！

### 拉开序幕

我们应该什么时候开始这个传奇？在创建新订单请求的初始请求中这样做很有吸引力，但是请记住——我们不能在一个事务中放置多个文档，除非我们非常确定完成和订单请求将在我们的 Cosmos DB 实例中紧密结合在一起。这意味着我们需要使用我们的文档消息与一个传奇交流——即使它不存在！

我们不想两次完成一个订单请求，对于我们的简单场景，让我们假设一个订单请求不能被“重试”。最初，我们的`OrderRequest`为每个项目创建了一个`ItemPurchased`文档消息——我们将删除它，取而代之的是一个单独的`OrderCreated`文档消息:

```
public class OrderCreated : IDocumentMessage  
{
    public Guid Id { get; set; }
    public Guid OrderId { get; set; }

    public List<LineItem> LineItems { get; set; }

    public class LineItem
    {
        public int ProductId { get; set; }
        public int Quantity { get; set; }
    }
} 
```

我们*可以*只拥有`OrderId`，然后让接收者加载`OrderRequest`，但是为了简单起见(并且假设你不能在创建之后改变顺序)，我们将把这个信息视为不可变的，并把它保存在消息中。现在，当我们创建一个`OrderRequest`时，我们也将发送这条消息:

```
public class OrderRequest : DocumentBase  
{
    public OrderRequest(ShoppingCart cart)
    {
        Id = Guid.NewGuid();
        Customer = new Customer
        {
            FirstName = "Jane",
            MiddleName = "Mary",
            LastName = "Doe"
        };
        Items = cart.Items.Select(li => new LineItem
        {
            ProductId = li.Key,
            Quantity = li.Value.Quantity,
            ListPrice = li.Value.ListPrice,
            ProductName = li.Value.ProductName
        }).ToList();
        Status = Status.New;

        Send(new OrderCreated
        {
            Id = Guid.NewGuid(),
            OrderId = Id,
            LineItems = Items
                .Select(item => new OrderCreated.LineItem
                {
                    ProductId = item.ProductId,
                    Quantity = item.Quantity
                })
                .ToList()
        });
    } 
```

这与我们最初的订单创建没有太大的不同——我们现在只是包括了启动订单执行传奇的文档消息。

我们的文档消息处理程序需要找到正确的`OrderFulfillment` saga 文档，并让 saga 处理消息:

```
public class OrderCreatedHandler : IDocumentMessageHandler<OrderCreated>  
{
    private readonly IDocumentDBRepository<OrderFulfillment> _repository;

    public OrderCreatedHandler(IDocumentDBRepository<OrderFulfillment> repository)
        => _repository = repository;

    public async Task Handle(OrderCreated message)
    {
        var orderFulfillment = (await _repository
                .GetItemsAsync(s => s.OrderId == message.OrderId))
            .FirstOrDefault();

        if (orderFulfillment == null)
        {
            orderFulfillment = new OrderFulfillment
            {
                Id = Guid.NewGuid(),
                OrderId = message.OrderId
            };

            await _repository.CreateItemAsync(orderFulfillment);
        }

        orderFulfillment.Handle(message);

        await _repository.UpdateItemAsync(orderFulfillment);
    }
} 
```

这里没有显示——但是我们确实需要确保每个订单只有一个履行传奇，所以我们可以在 Cosmos DB `OrderId`中配置一个惟一的索引。

`orderFulfillment.Handle`方法需要启动，并请求库存:

```
public void Handle(OrderCreated message)  
{
    Process(message, m =>
    {
        if (IsCancelled)
            return;

        LineItems = m.LineItems
            .Select(li => new LineItem
            {
                ProductId = li.ProductId,
                AmountRequested = li.Quantity
            })
            .ToList();

        foreach (var lineItem in LineItems)
        {
            Send(new StockRequest
            {
                Id = Guid.NewGuid(),
                ProductId = lineItem.ProductId,
                AmountRequested = lineItem.AmountRequested,
                OrderFulfillmentId = Id
            });
        }
    });
} 
```

在我的例子中，我用我们的`StockRequest`制作了`OrderFulfillment`佐贺坐标`Stock`。这是代替`Stock`监听`OrderCreated`本身。我在这里的一般想法是，fulfillment 管理库存的请求/退货，以及与此相关的任何业务逻辑。

我在开始时也有一个小小的检查——如果一个订单被取消，我们不想发出库存请求。这是执行交换请求的部分——我们可能会在收到订单创建通知之前*收到订单拒绝通知！说到消息传递，我总是假设消息是无序接收的，这意味着我们的业务逻辑需要能够处理这些情况。*

### 处理进货请求

我们最初的`Stock`实现相当幼稚，但是这一次我们想要更智能地处理订单。在我们的 stock handler 中，我们仍然有每个产品的文档，但现在它可以根据可用数量做出决定:

```
public void Handle(StockRequest message)  
{
    Process(message, e =>
    {
        if (QuantityAvailable >= message.AmountRequested)
        {
            QuantityAvailable -= e.AmountRequested;
            Send(new StockRequestConfirmed
            {
                Id = Guid.NewGuid(),
                OrderFulfillmentId = e.OrderFulfillmentId,
                ProductId = ProductId
            });
        }
        else
        {
            Send(new StockRequestDenied
            {
                Id = Guid.NewGuid(),
                OrderFulfillmentId = e.OrderFulfillmentId,
                ProductId = ProductId
            });
        }
    });
} 
```

因为我们将文档消息与收件箱的去杜平消息一起使用，所以我们不需要担心两次处理股票请求。我们的简单逻辑只是检查股票，如果成功，我们可以扣除股票并返回一条`StockRequestConfirmed`消息。如果没有，我们可以返回一个`StockRequestDenied`消息。

### 订单执行成功

我们最初的逻辑是“如果订单被批准，并且我们有足够的库存，就可以执行订单”。批准订单是人的决定，因此我们有一个基本的表单来这样做:

```
@if (Model.Order.Status == Status.New)
{
    <form asp-controller="Order" asp-action="Reject" asp-route-id="@Model.Order.Id" method="post">
        <input type="submit" value="Reject"/>
    </form>
    <form asp-controller="Order" asp-action="Approve" asp-route-id="@Model.Order.Id" method="post">
        <input type="submit" value="Approve"/>
    </form>
} 
```

当订单被批准后，我们只需委托 MediatR 来处理这个请求:

```
public class ApproveOrder  
{
    public class Request : IRequest
    {
        public Guid Id { get; set; }
    }

    public class Handler : IRequestHandler<Request>
    {
        private readonly IDocumentDBRepository<OrderRequest> _orderRepository;

        public Handler(IDocumentDBRepository<OrderRequest> orderRepository)
        {
            _orderRepository = orderRepository;
        }

        public async Task<Unit> Handle(Request request, CancellationToken cancellationToken)
        {
            var orderRequest = await _orderRepository.GetItemAsync(request.Id);

            orderRequest.Approve();

            await _orderRepository.UpdateItemAsync(orderRequest);

            return Unit.Value;
        }
    }
} 
```

然后委托给我们的文档来批准订单请求:

```
public void Approve()  
{
    if (Status == Status.Approved)
        return;

    if (Status == Status.Rejected)
        throw new InvalidOperationException("Cannot approve a rejected order.");

    Status = Status.Approved;
    Send(new OrderApproved
    {
        Id = Guid.NewGuid(),
        OrderId = Id
    });
} 
```

我们只想发送一次`OrderApproved`消息，所以只需要一些基本的状态检查来处理。

在订单执行方面:

```
public void Handle(OrderApproved message)  
{
    Process(message, m =>
    {
        OrderApproved = true;

        if (IsCancelled)
        {
            ProcessCancellation();
        }
        else
        {
            CheckForSuccess();
        }
    });
} 
```

每次我们收到一些外部通知时，我们都需要处理成功/失败路径，我稍后会谈到这一点。我们对`StockRequestConfirmed`的处理程序将是类似的，除了我们是在逐行项目的基础上跟踪库存:

```
public void Handle(StockRequestConfirmed message)  
{
    Process(message, m =>
    {
        var lineItem = LineItems.Single(li => li.ProductId == m.ProductId);
        lineItem.StockConfirmed = true;

        if (IsCancelled)
        {
            ReturnStock(lineItem);
        }
        else
        {
            CheckForSuccess();
        }
    });
} 
```

`CheckForSuccess`方法将查看是否满足所有订单执行要求:

```
private void CheckForSuccess()  
{
    if (IsCancelled)
        return;

    if (LineItems.All(li => li.StockConfirmed) && OrderApproved)
    {
        Send(new OrderFulfillmentSuccessful
        {
            Id = Guid.NewGuid(),
            OrderId = OrderId
        });
    }
} 
```

只有当我们所有的库存都被确认并且我们的订单被批准时，我们才会向`Order`文档发回一条消息，然后最终完成订单:

```
public void Handle(OrderFulfillmentSuccessful message)  
{
    Process(message, m =>
    {
        if (Status == Status.Rejected || Status == Status.Cancelled)
            return;

        Status = Status.Completed;
    });
} 
```

整个消息流如下所示:

[![](../Images/0172b3a4227e168d186e5e1377bb68e9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HM_xG7L9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/8/2018/Picture0056.png)

对于这一过程中的每一步，收件箱/发件箱结构都为我们处理了幂等性。然而，我们仍然需要处理无序的消息，这就是为什么每次我们收到通知时，您都会看到成功/失败检查。

既然我们已经了解了成功的途径，让我们来看看失败的途径。

### 取消订单履行

取消订单执行的第一种方式是订单被拒绝。从 web 应用程序中，我们的`Order`文档处理一个拒绝:

```
public void Reject()  
{
    if (Status == Status.Rejected)
        return;

    if (Status == Status.Approved)
        throw new InvalidOperationException("Cannot reject an approved order.");

    if (Status == Status.Approved)
        throw new InvalidOperationException("Cannot reject a completed order.");

    Status = Status.Rejected;
    Send(new OrderRejected
    {
        Id = Guid.NewGuid(),
        OrderId = Id
    });
} 
```

我们的订单发送一个`OrderRejected`文档消息，我们的订单执行文档接收到该消息:

```
public void Handle(OrderRejected message)  
{
    Process(message, m =>
    {
        OrderRejected = true;

        Cancel();
    });
} 
```

`Cancel`方法将订单执行标记为取消，然后处理取消:

```
private void Cancel()  
{
    IsCancelled = true;

    ProcessCancellation();
} 
```

同样，`StockRequestDenied`的通知将取消订单履行:

```
public void Handle(StockRequestDenied message)  
{
    Process(message, m =>
    {
        Cancel();
    });
} 
```

为了处理订单执行取消，我们需要做几件事。首先，我们需要通知我们的`Order`文档它需要被取消。对于任何已经履行的`Stock`商品，我们需要退回该库存:

```
private void ProcessCancellation()  
{
    if (!CancelOrderRequested && !OrderRejected)
    {
        CancelOrderRequested = true;
        Send(new CancelOrderRequest
        {
            Id = Guid.NewGuid(),
            OrderId = OrderId
        });
    }

    foreach (var lineItem in LineItems.Where(li => li.StockConfirmed))
    {
        ReturnStock(lineItem);
    }
} 
```

在这一过程中的每一步，我们都会跟踪我们已经发出的消息，这样我们就不会发送两次通知。要退回库存:

```
private void ReturnStock(LineItem lineItem)  
{
    if (lineItem.StockReturnRequested)
        return;

    lineItem.StockReturnRequested = true;
    Send(new StockReturnRequested
    {
        Id = Guid.NewGuid(),
        ProductId = lineItem.ProductId,
        AmountToReturn = lineItem.AmountRequested
    });
} 
```

如果库存商品已经请求退货，我们就跳过它。最后，订单可以接收取消订单请求:

```
public void Handle(CancelOrderRequest message)  
{
    Process(message, m =>
    {
        if (Status == Status.Rejected)
            return;

        Status = Status.Cancelled;
    });
} 
```

有了我们的失败流，消息流看起来像这样:

[![](../Images/f8d1a56f0c92081a58be990c5b4a1f5f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eAs2oL71--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/8/2018/Picture0057.png)

我们的订单执行传奇现在可以处理管理库存和订单批准的复杂流程，跟踪过程中的每个步骤，并在收到通知时处理成功/失败。它处理等幂、重试和交换/无序消息。

在下一篇文章中，我们将看看如何为其他资源实现收件箱/发件箱模式，允许我们连接到其他类型的数据库，在这些数据库中，分布式事务是不可能的。