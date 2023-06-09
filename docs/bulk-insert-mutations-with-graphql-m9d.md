# GraphQL 批量插入突变

> 原文：<https://dev.to/hasurahq/bulk-insert-mutations-with-graphql-m9d>

### GraphQL 插入突变用于在您的后端(通常是一个数据库)创建新对象，然后可以对其进行查询。

[![](img/17785ee29cdeea32ddced623ce9c7567.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--34nN-7jI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/bulk-insert-mutations-with-graphql-e22727ce5b12/1-8x4j7cCK_mtsl6_K0buCAQ.jpeg)

在任何典型的应用程序中，您经常需要一次将多个对象插入数据库。这些可以是相同类型的对象，也可以是不同类型的对象。

在这种情况下，需要记住以下几点:

*   完成插入需要多少网络请求？
*   实际将在数据库上运行的插入查询有多少，效率如何？
*   每个插入都是独立的吗？即执行的顺序重要吗？
*   如果一些插入失败了，剩下的插入会怎么样？剩余的插入应该通过还是应该回滚所有的插入？

### Hasura graph QL 引擎

[Hasura GraphQL 引擎](https://github.com/hasura/graphql-engine)通过任何 [Postgres 数据库](https://hasura.io/learn/database/postgresql/what-is-postgresql/)提供即时 GraphQL APIs。

考虑到以上几点，Hasura GraphQL 引擎为批量插入突变提供了以下特性，这些特性保证了高性能以及数据库始终处于一致状态:

*   可以使用变异中的单个插入变异字段插入多个相同类型的对象。这将在数据库中作为单个 SQL 语句执行。
*   不同类型的多个对象可以使用同一突变中的多个插入突变字段来插入。
*   如果一个突变中有多个突变字段，它们将在数据库中被顺序执行。
*   如果一个变异中的任何变异字段失败，该变异中的所有变异字段都将被回滚。即一个变异中的所有变异字段都作为一个**事务**运行。

假设我们的数据库中有以下表: