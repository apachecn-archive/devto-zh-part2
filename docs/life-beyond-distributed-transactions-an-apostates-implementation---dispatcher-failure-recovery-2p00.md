# 超越分布式事务的生活:叛教者的实现——调度程序故障恢复

> 原文：<https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---dispatcher-failure-recovery-2p00>

此系列中的帖子:

*   [一个引子](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---a-primer-2po6-temp-slug-8261948)
*   [文件协调](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---document-coordination-leb-temp-slug-1009285)
*   [文档示例](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---document-example-4fpk-temp-slug-5917155)
*   [调度示例](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---dispatching-example-31i6-temp-slug-684211)
*   [失败和重试](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---failures-and-retries-m6-temp-slug-2730961)
*   [故障恢复](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-dispatcher-failure-recovery/)
*   [传奇故事](https://jimmybogard.com/life-beyond-distributed-transactions-sagas/)
*   [关系资源](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-relational-resources/)

[本系列的示例代码](https://github.com/jbogard/adventureworkscosmos)

在上一篇文章中，我们讨论了如何从代码处理消息中的异常中恢复。我们在文档中执行了一些操作，然后出现了错误。但是当*在*调度过程中出现问题时会发生什么呢:

[![](img/33a507de8268af02b5b946eea7f26ee7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mX6PIgQ6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/7/2018/Picture0054.png)

如果我们的调度程序本身出现故障，或者:

*   从发件箱中提取邮件
*   向接收者发送消息
*   保存文档

那么我们的文档仍然是一致的，但是我们已经将执行流丢失到 dispatch。我们已经在一定程度上减轻了我们的失败，但是我们仍然有可能在我们的 dispatcher 中出现一些不可恢复的失败，并且再多的异常处理也无法阻止发件箱中有消息的文档等待处理。

如果我们能够在一个事务中包装所有的东西，文档和队列，那么我们就可以优雅地恢复。然而，这个系列的要点是我们*没有*访问分布式事务的权限，所以这个选项是不存在的。

我们需要某种后台进程来查找发件箱中有未决消息的文档，准备进行处理。

### 设计派遣救援人员

我们已经有了使用异步消息传递来重试单个调度的调度恢复过程，这在应用程序代码出现故障时非常有用。当失败是环境原因时，我们唯一的线索是发件箱中有一个文档有消息。

从这些故障中恢复的一般过程是:

*   查找任何包含未处理消息的文档(最好是最早的)
*   一次重试一个

尽管我们有这样的可能性:

*   在飞行调度中
*   进行中重试

理想情况下，我们有某种滞后的处理器，这样当我们有问题时，我们不会干扰正常的处理。对我们来说幸运的是，Cosmos DB 已经提供了通知文档已被更改的能力，即[更改提要](https://docs.microsoft.com/en-us/azure/cosmos-db/change-feed)，这个更改提要甚至允许我们使用内置的延迟来工作。在每个文档更改之后，我们可以等待一段时间，在这段时间里我们假设分派发生了，然后重新检查文档以确保分派发生了。

我们的救援人员将:

*   当文档更改时获得通知
*   检查是否还有要处理的发件箱邮件
*   发送消息以重新处理文档

这有点天真，因为我们会收到所有文档更改的通知。为了让我们的生活更轻松一点，我们可以关闭文档消息调度的即时处理，只通过异步进程进行调度，但这不是必需的。

### 创建变更进给处理器

使用[文档作为我们的指南](https://docs.microsoft.com/en-us/azure/cosmos-db/change-feed#using-the-change-feed-processor-library)，我们需要创建两个组件:

*   文档提要观察者，用于接收文档更改通知
*   一个变更提要处理器来托管和调用我们的观察者

因为在我们的 dispatcher 中已经有了一个后台处理器，所以我们可以简单地将观察者托管在同一个端点中。然而，观察者实际上并不做这项工作——我们仍然会发送一条消息来处理文档。这是因为 NServiceBus 仍然提供所有关于重试和有害消息的逻辑，我不想再编写这些逻辑了。像我的大多数集成一样，我尽可能快地将工作转换成持久的消息和 NServiceBus。

这让我的观察者变得很小:

```
public class DocumentFeedObserver<T> : IChangeFeedObserver  
    where T : DocumentBase
{
    static ILog log = LogManager.GetLogger<DocumentFeedObserver<T>>();

    public Task OpenAsync(IChangeFeedObserverContext context) 
        => Task.CompletedTask;

    public Task CloseAsync(
        IChangeFeedObserverContext context, 
        ChangeFeedObserverCloseReason reason)
        => Task.CompletedTask;

    public async Task ProcessChangesAsync(
        IChangeFeedObserverContext context, 
        IReadOnlyList<Document> docs, 
        CancellationToken cancellationToken)
    {
        foreach (var doc in docs)
        {
            log.Info($"Processing changes for document {doc.Id}");

            var item = (dynamic)doc;

            if (item.Outbox.Count > 0)
            {
                ProcessDocumentMessages message = ProcessDocumentMessages.New<T>(item);

                await Program.Endpoint.SendLocal(message);
            }
        }
    }
} 
```

`OpenAsync`和`CloseAsync`方法不会做任何事情，我所有的逻辑都在`ProcessChangesAsync`方法中。在这个方法中，我得到了一个已更改文档的集合。我使我的`DocumentChangeObserver`通用，因为每个观察者只观察一个集合，所以我必须为每个具体的`DocumentBase`类型创建不同的观察者实例。

在方法中，我循环所有传入的文档，并查看发件箱中是否有任何消息。如果是这样，我将创建一个新的`ProcessDocumentMessages`发送给我自己(因为我也在这个应用程序中托管 NServiceBus)，然后它将处理文档消息。

有了简单的观察器，我们需要将观察器合并到我们的应用程序启动中。

### 配置观察者

对于我们的观察者来说，我们有几个选择来处理文档变更。因为每次文档变更，我们的观察者都会被*调用，所以我们要小心它所做的工作。*

我们最初的设计是在与原始工作相同的请求中发送文档消息。如果我们保持这一点，我们希望确保尽量减少对包含消息的文档的返工量。理想情况下，我们的观察者只在调度确实有问题时才发出消息。这也将最小化队列消息的数量，为错误情况保留它们。

因此，一个简单的解决方案就是在我们的处理过程中引入一些延迟:

```
private static ChangeFeedProcessorBuilder CreateBuilder<T>(DocumentClient client)  
    where T : DocumentBase
{
    var builder = new ChangeFeedProcessorBuilder();
    var uri = new Uri(CosmosUrl);
    var dbClient = new ChangeFeedDocumentClient(client);

    builder
        .WithHostName(HostName)
        .WithFeedCollection(new DocumentCollectionInfo
        {
            DatabaseName = typeof(T).Name,
            CollectionName = "Items",
            Uri = uri,
            MasterKey = CosmosKey
        })
        .WithLeaseCollection(new DocumentCollectionInfo
        {
            DatabaseName = typeof(T).Name,
            CollectionName = "Leases",
            Uri = uri,
            MasterKey = CosmosKey
        })
        .WithProcessorOptions(new ChangeFeedProcessorOptions
        {
            FeedPollDelay = TimeSpan.FromSeconds(15),
        })
        .WithFeedDocumentClient(dbClient)
        .WithLeaseDocumentClient(dbClient)
        .WithObserver<DocumentFeedObserver<T>>();

    return builder;
} 
```

`ChangeFeedProcessorBuilder`是为我们想要观察的每种文档类型配置的，在本例中时间跨度为 15 秒。我可以提前一点，比如说一个小时左右。这实际上取决于业务、他们期望完成的工作的 SLA。

最后，在我们的应用程序启动中，我们需要创建构建器、处理器并启动它:

```
Endpoint = await NServiceBus.Endpoint.Start(endpointConfiguration)  
    .ConfigureAwait(false);

var builder = CreateBuilder<OrderRequest>(client);  
var processor = await builder.BuildAsync();

await processor.StartAsync();

Console.WriteLine("Press any key to exit");  
Console.ReadKey();

await Endpoint.Stop()  
    .ConfigureAwait(false);

await processor.StopAsync(); 
```

有了这些，我们就可以对失败做最后的防范，假设有人完全拔掉了我们应用程序的插头，我们只剩下一个发件箱中有消息的文档。

在我们的下一篇文章中，我们将看看如何使用 sagas 来协调文档之间的更改——如果我们希望在文档中处理所有的更改，或者不处理任何更改，会发生什么情况？