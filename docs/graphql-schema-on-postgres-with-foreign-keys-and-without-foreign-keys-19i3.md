# 有外键和没有外键的 Postgres 上的 GraphQL 模式

> 原文：<https://dev.to/hasurahq/graphql-schema-on-postgres-with-foreign-keys-and-without-foreign-keys-19i3>

当使用关系数据库系统对图形建模时，可以将表视为节点，节点之间的链接通常作为这些表的列上的外键约束而导出。Hasura GraphQL 引擎允许您通过在 Postgres 数据库上使用单列外键、多列外键甚至不使用外键来创建关系，从而查询数据。

## 单列外键

让我们看一个例子。该模式包含两个表，`user`和`address`。