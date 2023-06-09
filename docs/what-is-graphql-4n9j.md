# GraphQL 是什么？

> 原文：<https://dev.to/grigorkh/what-is-graphql-4n9j>

最初发表于[媒体](https://medium.com/devgorilla/what-is-graphql-f0902a959e4)

GraphQL 是构建和查询 API 的最现代的方式之一。

[![](img/210182867b9afc021d7ed1b01c0e9a5f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M5dHch5L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AR6WHuWI0M9I4t-og2yNq-w.png)

GraphQL 是描述如何请求数据的语法，通常用于从服务器向客户端加载数据的 T2。GraphQL 有三个主要的
特征:

*   它让客户端确切地指定它需要什么数据。
*   这使得从多个来源汇总数据变得更加容易。
*   它使用类型系统来描述数据。

使用 **GraphQL** ，用户能够进行一次调用来获取所需的
信息，而不是构建几个 REST 请求来获取相同的信息。

> GitHub 使用 **GraphQL** ，因为它为开发者提供了更多的灵活性。与发送多个 REST 调用来接收相同的信息相比，精确生成用户想要的信息的
> 选项是一个巨大的
> 优势。使用 REST 调用生成
> 信息需要两个阶段的过程——一个阶段收集
> 用户的信息，另一个阶段获取与用户相关的
> 组织的信息。GraphQL 有助于减轻这个两步
> 过程。

### 那么 GraphQL 是什么呢？

GraphQL 查询是一个字符串，它被发送到服务器进行解释并完成
,然后服务器将 JSON 返回给客户机。

[![](img/922258e506a99f4e576c4924908e9fa1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qaD4KroP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AdryZIFuTTY-9WiGlxSVEag.png)

**定义数据形状:**您首先会注意到 GraphQL 查询
反映了它们的响应。这使得预测从查询返回的数据
的形状变得容易，如果你知道你的应用
需要的数据，也可以编写一个查询。更重要的是，这使得 GraphQL 非常容易学习和使用。GraphQL
是由产品的数据需求和构建产品的
设计者和开发者驱动的。

GraphQL 的另一个重要方面是它的层次化
特性。GraphQL 自然遵循对象之间的关系，其中 RESTful
服务可能需要多次往返(移动网络上的资源密集型)
或 SQL 中复杂的 join 语句。这种数据层次结构与
图形结构的数据存储很好地匹配，最终与使用它的层次用户界面
很好地匹配。

[![](img/12e790a958205b963e6d75e49bb18f72.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_3cAsKLa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dht94x4ujlzk504ytsqa.png)

**强类型:**graph QL 查询的每一层对应一个特定的
类型，每种类型描述一组可用字段。与 SQL 类似，这个
允许 GraphQL 在执行查询之前提供描述性的错误消息。

**协议，而不是存储:**服务器上的每个 GraphQL 字段都由任何
任意函数支持。GraphQL 必须利用所有这些现有的工作才是有用的，
因此它不指定或提供任何后备存储。相反，GraphQL 利用了现有代码的
优势。

**自省:**可以向 GraphQL 服务器查询它支持的类型。
这为工具和客户端软件创建了一个强大的平台，在这些信息之上
构建静态类型语言的代码生成、中继或
IDEs，如* *GraphiQL ** (如下图)。GraphiQL 帮助开发人员快速学习和探索 API，而不需要修改代码库或与 cURL 争论。

[![](img/5ccdd41ac4adbdd3be699f22677181b6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--naHON7UN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AfOvtr8ooBvqfF5LrEgn_2A.jpeg)

**版本自由:**返回数据的形状完全由
客户端的查询决定，所以服务器变得更简单，更容易通用化。当你
添加新产品功能时，额外的字段可以添加到服务器上，
保持现有的客户端不受影响。当您淘汰旧功能时，相应的服务器字段
可能会被弃用，但会继续工作。这个渐进的、向后兼容的过程消除了对递增的版本号的需要。

#### **即将到来:**

*   GraphQL basics(图形 SQL 基础)
*   REST vs GraphQL(剩馀 vs 图形 SQL)
*   使用无服务器运行 GraphQL 端点