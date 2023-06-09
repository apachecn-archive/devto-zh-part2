# GraphQL 突变的原子集合和增量运算符

> 原文：<https://dev.to/hasurahq/atomic-set-and-increment-operators-for-graphql-mutations-1lo2>

使用 GraphQL 时，对数据的写入或更新是通过突变来完成的。在本帖中，我们将讨论在 [Hasura GraphQL 引擎](https://hasura.io)上可用的更新突变和相关操作符。

[![](img/bd1be26d620379f4b635345a31e9b9f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lZEi4mzO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/updating-data-using-mutations-on-hasura-graphql-87c7e22503bc/1-yMbKmCoxIJ4CLO5aHQi-aQ.png)

**TL；博士:**

*   Hasura 允许您使用强大的`where`参数一次更新多个对象，该参数允许您创建一个布尔过滤器来选择要更新的对象
*   Hasura 支持一个`set`操作符来设置或替换一个现有的值
*   Hasura 支持一个`increment`操作符，以给定的数量自动递增数值，以防止出现多个客户端递增同一属性的常见竞争情况
*   Hasura 支持本地 JSON/JSONB 操作符，您可以使用这些操作符来修改 postgres 列的 JSON/JSONB 值中的属性，而不需要`set`完整的 JSON 值

## 图式与生成的突变

让我们考虑以下模式作为我们的示例: