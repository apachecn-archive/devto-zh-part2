# 一个失败的单元测试

> 原文：<https://dev.to/kapil_aggarwal1/one-failing-unit-test-368>

[![](../Images/3f9d6722c135abf8d5b950d90ccbe722.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RdfUeoFd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aq1dabCG6YzaqeLtf5ZZmgw.jpeg) 

<figcaption>单位</figcaption>

目前我在一个 QA 项目中工作，我们正在对 Sql 数据库进行重新建模，以生成新的 Graph db，并在此基础上编写一个新的 rest 服务。

绿地“耶”。我已经听说了很多关于测试金字塔的事情，现在是见证它是如何被建造起来的时候了。

在测试金字塔中，大部分的自动化测试应该在单元级被覆盖，然后一些在集成级，少数用于端到端的旅程。

在我职业生涯的大部分时间里，单元测试都是黑盒。同样作为一个 App 的看门人(这种说法是有争议的，让我们把它放在未来)，信任大多数数量的测试用例，你假装理解并且还没有写，这不是一个很舒服的位置。

因此，我想留意单元测试中没有发现的任何东西。我的愿望很快就实现了。

这一切都是从我们团队中的一位 BA 提出的一个小小的临时变更请求开始的。

### 要求

*BA* :嘿，我们能不能把回复里的“id”改成“key”。

*Dev* :当然。

QA :马上就要测试了。

[![](../Images/0e7bfdddccc12445c27a4ebfbd4a456f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jDfoO4EB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ak2I1T8faygpFoOGZQoyxJg.png)T3】要求

让我们看一些伪代码

### 实现

让我们假设我们正在构建一个返回员工详细信息的简单应用程序。伪代码将如下所示。

*   Scala case 类作为模型
*   序列化和反序列化
*   Neo4j 连接
*   获取员工的查询

```
**case class** Employee(id: String, firstName:String, lastName:String)
**implicit val** employeeFormat = jsonFormat3(Employee)

def getEmployee(id:String): Employee {
//read neo4j data and return case class 
query = "match (e:Employee {id:id})

//some code to parse query result and make Employee model
return Employee(e)
} 
```

上述代码的最小测试用例及种子测试数据将。

```
**def** testEmployee():Unit={
//spin in-memory neo4j
//add create (e1:Employee {id:'1', firstName:'Joe', lastName:'Doe')
//val employee = getEmployee("1")
//assert employee model with "key" 
```

到目前为止，一切都很好，开发人员实际上通过在 testEmployeModel 测试用例、固定代码和“Yay”TDD 的断言中将“id”更改为“key”而使测试用例失败。

### 问题

在进行 e2e 测试时，我们将数据加载到测试数据库中。

```
create (e1:Employee {key:’1', firstName:’Joe’, lastName:’Doe’) 
```

打服务和“内部服务器错误”。

虽然对这个错误的修复非常简单，但它让我一直在想这里可能出了什么问题。我们已经为这个需求编写了单元测试用例，即使开发是以 TDD 的方式完成的，现在怎么会出现这个 bug 呢？

如果你知道答案，那么继续执行，否则再执行一次，看看你是否能找出问题所在。

### 修罗

将 getEmployee 方法中的查询更改为

```
query = "match (e:Employee {key:id}) return Employee(e) 
```

### 错过了

这里有两个错误。

*   需求理解
*   试验夹具

BA 的要求从未被理解/解释清楚，因为实施很容易，而且从未讨论过背后的“原因”。服务的消费者想要“key”而不是“id ”,但在数据接收部分，数据库值也将发生变化。

如果在对代码进行任何更改之前，在理解测试用例，尤其是单元测试用例的测试数据方面花费了更多的精力。这个问题一定是当场就被发现并解决了。

但是现在想象一下，一旦项目成熟，这些测试装置将会出现在成千上万个测试中，人们如何知道要修复/更新什么呢？

> 没有测试用例比测试用例给出假阳性结果要好。

你有没有遇到过这种问题，更新或忘记更新数百个测试夹具的任何功能变化，如果有任何解决方案/策略来处理。请在评论区分享。