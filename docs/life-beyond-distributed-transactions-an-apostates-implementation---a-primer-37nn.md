# 分布式事务之外的生活:叛教者的实现-初级读本

> 原文：<https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---a-primer-37nn>

此系列中的帖子:

*   [一个引子](https://jimmybogard.com/life-beyond-transactions-implementation-primer/)
*   [文件协调](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-aggregate-coordination/)
*   [文档示例](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-document-example/)
*   [调度示例](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-dispatching-example/)
*   [失败和重试](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-failures-and-retries/)
*   [故障恢复](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-dispatcher-failure-recovery/)
*   [传奇故事](https://jimmybogard.com/life-beyond-distributed-transactions-sagas/)
*   [关系资源](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-relational-resources/)

[本系列的示例代码](https://github.com/jbogard/adventureworkscosmos)

对于那些使用 SQL 数据库的人来说，使用事务或多或少是理所当然的。我们最需要担心的是:

*   使用适当的隔离级别
*   不要在单个事务中做太多事情，以防止过多的锁

我看到的绝大多数应用程序都可以愉快地忽略数据库中事务的内部工作。我们理所当然地认为我们对一行或多行的操作要么被提交，要么被回滚。

当我们开始处理不再局限于单个资源的事务，或者在许多 NoSQL 数据库的情况下，处理多个实体的事务时，事情变得更加复杂。在许多 NoSQL 数据库中，交易仅限于单个实体/记录。如果多实体事务*受*支持，那么有许多限制可能会使这种选择不受欢迎。

当我们有两个非事务性资源时，我们知道我们有许多整体模式来尝试协调这些动作(在我的[Refactoring to Resilience 系列](https://jimmybogard.com/refactoring-towards-resilience-a-primer/)中有所涉及):

[![](img/9ddae7d7d7dde6933c49b19782f49f61.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jeCxjglZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/0/2017/Picture2.png)

所有这些选项都假设“我必须让这两个动作暂时耦合”并让它们同时发生。

但如果事实并非如此呢？如果我们不再试图协调两个行动，而是在我们的资源之间有更松散的耦合，会怎么样？那会是什么样子呢？

这也是 Pat Helland 的论文《交易之外的生活:一个叛教者的观点》的主要内容。在本文中，Pat 描述了一种机制，用于解决当我们的事务只涉及单个实体——消息传递——时，在资源之间协调动作的基本问题。

[![](img/598f6a4aca9b4959715113bc0643aedb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--57LuYBjo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/7/2018/helland7.png)

我们使用某种消息传递方式，将我们的消息保存在实体内*,以便发送给其他实体:*

[![](img/b75981f9bb097acbf6d15b83b4ab7f59.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x-VNYYkn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/7/2018/helland5.png)

由于事务的范围是单个实体，如果我们需要影响其他实体，我们不能直接这样做。相反，我们将影响变化的意图作为信息存储在我们的实体中。该事务涵盖了我们的业务数据、*和*对外通信。

这当然不是解决这个问题的唯一方法，因为我可以使用 [Saga 模式](http://www.cs.cornell.edu/andru/cs711/2002fa/reading/sagas.pdf)作为管理多实体活动之间失败的手段。 [Caitie McCaffrey](https://caitiem.com/) 有一个[关于这个](https://www.youtube.com/watch?v=0UTOLRTwOX0)的精彩演讲，但是对于我的情况，我不能直接使用 Saga 模式，因为那意味着有某种逻辑上的“撤销”。相反，我希望能够使用我可用的许多协调模式中的任何一种，*包括*传奇模式。

在我们进入实现细节之前，让我们看一些真实数据库中的真实代码，它不完全允许多实体事务。

### 真实世界的例子

让我们假设我有一个电子商务应用程序，在那里我可以查看产品，将商品添加到购物车，结帐，然后最终批准订单。作为批准订单的一部分，我需要减少库存。我们将跳过任何复杂的业务规则，如负库存、预订等。为了简单起见，当我们订购时，我们只需从库存中减去订购数量:

```
var orderRequest = await _orderRepository.GetItemAsync(id);

orderRequest.Approve();

await _orderRepository.UpdateItemAsync(orderRequest);

foreach (var lineItem in orderRequest.Items)  
{
    var stock = (await _stockRepository
        .GetItemsAsync(s => s.ProductId == lineItem.ProductId))
        .Single();

    stock.QuantityAvailable -= lineItem.Quantity;

    await _stockRepository.UpdateItemAsync(stock);
} 
```

在我的例子中，我使用的是 Azure Cosmos DB，它支持各种各样的[一致性级别](https://docs.microsoft.com/en-us/azure/cosmos-db/consistency-levels)。Azure Cosmos DB 也支持多文档事务，但只是以[存储过程和函数](https://docs.microsoft.com/en-us/azure/cosmos-db/programming#database-program-transactions)的形式，即使这样，也只有一些限制。一旦你将其他资源引入其中，比如 Azure Service Bus、Azure SQL Database，或者单个分区键之外的任何东西，我们就不能再使用事务了。

考虑到这一点，我们回头看看我们的原始代码，并问自己，“这个动作需要耦合这些资源吗，或者我们可以将它们解耦吗？”当然，在某些情况下，我们需要协调两个动作(如前所述，使用一个协调器)，但在很多情况下，我们不需要，也不想承担更大范围事务的成本。

对于我们上面的例子，我们需要在批准订单的时候扣除库存吗？还是可以以后再发生？根据业务，扣除股票不需要立即发生，但它确实需要发生，最终。

在接下来的几篇文章中，我将构建一个与其他实体(甚至其他资源)通信的机制，以 Azure Cosmos DB 为例，看看我们如何构建一个原子通信系统。