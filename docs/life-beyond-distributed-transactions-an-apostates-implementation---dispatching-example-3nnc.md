# 超越分布式事务的生活:一个叛教者的实现-调度示例

> 原文：<https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---dispatching-example-3nnc>

此系列中的帖子:

*   [一个引子](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---a-primer-2po6-temp-slug-8261948)
*   [文件协调](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---document-coordination-leb-temp-slug-1009285)
*   [文档示例](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---document-example-4fpk-temp-slug-5917155)
*   [调度示例](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-dispatching-example/)
*   [失败和重试](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-failures-and-retries/)
*   [故障恢复](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-dispatcher-failure-recovery/)
*   [传奇故事](https://jimmybogard.com/life-beyond-distributed-transactions-sagas/)
*   [关系资源](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-relational-resources/)

[本系列的示例代码](https://github.com/jbogard/adventureworkscosmos)

在上一篇文章中，我们看了如何重构我们的文档，使用消息传递来交流变化。然而，我们仍然遗漏了一些东西——调度员:

[![](img/b91938a1b90843fe9315460df61b2efa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uPSqc1jt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/7/2018/Picture0050.png)

我们的 dispatcher 是促进文档通信的主要组件。对于给定的文档，它需要:

*   从文档的发件箱中读取邮件
*   找到每个的文档消息处理程序，并调用
*   管理文档消息处理程序的失败

我们将在以后的文章中解决最后一个问题。有一件事我们确实需要首先考虑——调度程序从哪里获得它的文档列表来调度消息？

在到达调度程序之前，我们需要解决这个问题——知道哪些文档需要调度！

### 介绍一个工作单位

对于一个给定的请求，我们将加载一个文档并对其进行一些修改。我们已经有了一个加载文档的夹点——存储库。如果我们想在同一个请求中发送文档消息，我们需要跟踪已经加载到请求中的文档。为此，我们可以使用一个[工作单元](https://martinfowler.com/eaaCatalog/unitOfWork.html)。

您使用的任何 ORM 都将实现这种模式——例如，对于实体框架，DbContext 是您的工作单元。对于 Cosmos DB 的 SDK，真的没有这些 ORM 模式的概念。我们得自己介绍他们。

我们的工作单元将跟踪给定会话/请求的文档，让我们在请求的分派阶段与加载的文档进行交互。我们的工作单元还将充当[身份映射](https://www.martinfowler.com/eaaCatalog/identityMap.html)——确保当我们在请求中加载文档时，它只被加载一次。这是我们的基本`IUnitOfWork`界面:

```
public interface IUnitOfWork  
{
    T Find<T>(Guid id) where T : DocumentBase;
    void Register(DocumentBase document);
    void Register(IEnumerable<DocumentBase> documents);
    Task Complete();
} 
```

该实现包含一个简单的“身份图”

```
public class UnitOfWork : IUnitOfWork  
{
    private readonly ISet<DocumentBase> _identityMap 
        = new HashSet<DocumentBase>(DocumentBaseEqualityComparer.Instance); 
```

然后我们可以用我们的`UnitOfWork`注册一个实例:

```
public void Register(DocumentBase document)  
{
    _identityMap.Add(document);
}

public void Register(IEnumerable<DocumentBase> documents)  
{
    foreach (var document in documents)
    {
        Register(document);
    }
} 
```

查找现有的`DocumentBase`只是搜索我们的身份地图:

```
public T Find<T>(Guid id)  
    where T : DocumentBase
    => _identityMap.OfType<T>().FirstOrDefault(ab => ab.Id == id); 
```

我们将回到`Complete`方法，因为这将是我们调度的部分。我们仍然需要在工作单元中注册文档的部分，这将在我们的存储库实现中实现:

```
public async Task<T> GetItemAsync(Guid id)  
{
    try
    {
        var root = _unitOfWork.Find<T>(id);

        if (root != null)
            return root;

        Document document = await _client.ReadDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id.ToString()));
        var item = (T)(dynamic)document;

        _unitOfWork.Register(item);

        return item;
    }
    catch (DocumentClientException e)
    {
        if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
        {
            return null;
        }

        throw;
    }
} 
```

我们将对存储库中加载文档、在我们的工作单元中注册和查找的任何方法重复这一过程。

有了跟踪我们的文档的方法，让我们看看我们将如何调度。

### 调度公文消息

我们的调度程序相当简单——唯一的问题是我们需要发现任何潜在的异常。我们不想在出现问题时崩溃，而是想让异常浮出水面，让调用者决定如何处理失败:

```
public interface IDocumentMessageDispatcher  
{
    Task<Exception> Dispatch(DocumentBase document);
} 
```

如果我将一个文档消息分派给三个处理程序，我不希望一个处理程序阻止分派给其他处理程序。

我们还有另一个挑战——我们的接口不是调度的通用接口，但是处理程序和存储库是！我们将不得不使用一些泛型技巧来将我们的基本类型展开为正确的泛型类型。基本流程是:

*   对于每个文档消息:
*   查找文档消息处理程序
*   打电话给负责人
*   从发件箱中删除文档消息
*   保存文档

下面是我们的基本实现:

```
public async Task<Exception> Dispatch(DocumentBase document)  
{
    var repository = GetRepository(document.GetType());
    foreach (var documentMessage in document.Outbox.ToArray())
    {
        try
        {
            var handler = GetHandler(documentMessage);

            await handler.Handle(documentMessage, _serviceFactory);

            document.ProcessDocumentMessage(documentMessage);

            await repository.Update(document);
        }
        catch (Exception ex)
        {
            return ex;
        }
    }
    return null;
} 
```

我们首先基于文档类型构建一个存储库。接下来，我们遍历发件箱中的每个文档消息。对于每个文档消息，我们将找到处理程序并调用它们。一旦成功，我们将处理我们的文档消息(从发件箱中删除它)并更新我们的文档。我们希望更新发件箱中的每个文档消息——如果发件箱中有 3 个文档消息，我们保存 3 次以确保一旦消息完成，如果出现问题，我们不必返回。

方法有点不靠谱，因为我们在桥接泛型。基本上，我们创建了文档消息处理程序的非通用版本:

```
private abstract class DomainEventDispatcherHandler  
{
    public abstract Task Handle(
        IDocumentMessage documentMessage, 
        ServiceFactory factory);
} 
```

然后创建一个从此继承的通用版本:

```
private class DomainEventDispatcherHandler<T> : DomainEventDispatcherHandler  
    where T : IDocumentMessage
{
    public override Task Handle(IDocumentMessage documentMessage, ServiceFactory factory)
    {
        return HandleCore((T)documentMessage, factory);
    }

    private static async Task HandleCore(T domainEvent, ServiceFactory factory)
    {
        var handlers = factory.GetInstances<IDocumentMessageHandler<T>>();
        foreach (var handler in handlers)
        {
            await handler.Handle(domainEvent);
        }
    }
} 
```

这个模式我用过无数次了，基本上是为了让编译器满意。我也尝试过`dynamic`，但是它引入了其他问题。然后为了调用它，我们的`GetHandler`实例化了泛型版本，但是返回了非泛型基类:

```
private static DomainEventDispatcherHandler GetHandler(  
    IDocumentMessage documentMessage)
{
    var genericDispatcherType = typeof(DomainEventDispatcherHandler<>)
        .MakeGenericType(documentMessage.GetType());

    return (DomainEventDispatcherHandler)
        Activator.CreateInstance(genericDispatcherType);
} 
```

这样，我就可以让非泛型代码调用泛型。我将对存储库做一些类似的事情:

```
private abstract class DocumentDbRepo  
{
    public abstract Task<DocumentBase> FindById(Guid id);
    public abstract Task Update(DocumentBase document);
} 
```

有了这些桥梁，我的调度程序可以与具体的通用存储库和处理程序进行交互。最后一部分是清理发件箱的文档:

```
public void ProcessDocumentMessage(  
    IDocumentMessage documentMessage)
{
    _outbox?.Remove(documentMessage);
} 
```

随着我们的调度程序完成，我们的工作单元就位，我们现在可以专注于将调用我们的工作单元的部分。

### 构建一个 MediatR 行为

一旦一切都“完成”，我们希望我们的工作单元完成每个请求。对于 ASP.NET 核心应用程序，这可能意味着某种过滤器。对我们来说，我希望调度真正适用于任何上下文，因此一种可能性是使用[mediator 行为](https://github.com/jbogard/MediatR/wiki/Behaviors)来包装我们的 mediator 处理程序。过滤器当然也可以工作，但是如果我们想让所有的东西都被适当地分派，我们需要在测试中模仿我们的过滤器。

行为非常简单:

```
public class UnitOfWorkBehavior<TRequest, TResponse>  
    : IPipelineBehavior<TRequest, TResponse>
{
    private readonly IUnitOfWork _unitOfWork;

    public UnitOfWorkBehavior(IUnitOfWork unitOfWork)
    {
        _unitOfWork = unitOfWork;
    }

    public async Task<TResponse> Handle(
        TRequest request, 
        CancellationToken token, 
        RequestHandlerDelegate<TResponse> next)
    {
        var response = await next();

        await _unitOfWork.Complete();

        return response;
    }
} 
```

我们做主要的工作，然后一旦完成，完成我们的工作单元。

这就是我们所有的基础设施，最后一部分是在启动时向 DI 容器注册这些组件:

```
services.AddMediatR(typeof(Startup));

services.AddScoped(typeof(IDocumentDBRepository<>), typeof(DocumentDBRepository<>));  
services.AddScoped<IUnitOfWork, UnitOfWork>();  
services.AddScoped<IDocumentMessageDispatcher, DocumentMessageDispatcher>();  
services.AddScoped(typeof(IPipelineBehavior<,>), typeof(UnitOfWorkBehavior<,>));  
services.Scan(c =>  
{
    c.FromAssembliesOf(typeof(Startup))
        .AddClasses(t => t.AssignableTo(typeof(IDocumentMessageHandler<>)))
        .AsImplementedInterfaces()
        .WithTransientLifetime();
}); 
```

我们使用[mediator 添加我们的 mediator 处理程序。extensions . Microsoft . dependency injection](https://www.nuget.org/packages/MediatR.Extensions.Microsoft.DependencyInjection)包，我们的通用存储库、工作单元、调度程序和工作行为单元。最后，我们使用 [Scrutor](https://github.com/khellang/Scrutor) 添加了所有的`IDocumentMessageHandler`实现，使得一次性添加所有处理程序变得更加容易。

有了所有这些，我们可以运行并验证我们的处理程序触发，我们可以在股票项目的收件箱中看到消息:

```
{
    "QuantityAvailable": 99,
    "ProductId": 771,
    "id": "cfbb6333-ed9f-49e7-8640-bb920d5c9106",
    "Outbox": {
        "$type": "System.Collections.Generic.HashSet`1[[AdventureWorksCosmos.UI.Infrastructure.IDocumentMessage, AdventureWorksCosmos.UI]], System.Core",
        "$values": []
    },
    "Inbox": {
        "$type": "System.Collections.Generic.HashSet`1[[AdventureWorksCosmos.UI.Infrastructure.IDocumentMessage, AdventureWorksCosmos.UI]], System.Core",
        "$values": [
            {
                "$type": "AdventureWorksCosmos.UI.Models.Orders.ItemPurchased, AdventureWorksCosmos.UI",
                "ProductId": 771,
                "Quantity": 1,
                "Id": "2ab2108c-9698-49e8-93de-a3ced453836a"
            }
        ]
    },
    "_rid": "WQk4AKSQMwACAAAAAAAAAA==",
    "_self": "dbs/WQk4AA==/colls/WQk4AKSQMwA=/docs/WQk4AKSQMwACAAAAAAAAAA==/",
    "_etag": "\"060077c2-0000-0000-0000-5b71d8a10000\"",
    "_attachments": "attachments/",
    "_ts": 1534187681
} 
```

现在，我们的文档之间有了有效的文档消息传递！

嗯，差不多了。

在下一篇文章中，我们将讨论当事情出错时应该怎么做:失败和重试。