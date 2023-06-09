# GraphQL 突变的 Postgres 触发器

> 原文：<https://dev.to/hasurahq/postgres-triggers-on-graphql-mutations-5d1e>

<figure>[![](img/d4a8847d599d720e58919d12ef5ec1ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UKxORtQV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/postgres-triggers-on-graphql-mutations-682bf48db023/1-BPEyAMkUQVEY5FnvmAPBRw.jpeg) 

<figcaption>Postgres 在动作中触发 GraphQL 突变</figcaption>

</figure>

## TL；速度三角形定位法(dead reckoning)

设置 Postgres 函数和触发器来执行后端操作，如验证和插入/更新其他相关数据，只要表中发生一些插入/更新。现在，当您使用 GraphQL 变体时， [Postgres 触发器](https://hasura.io/docs/latest/graphql/core/guides/postgres/triggers.html)运行，执行所需的后端操作。

## 简介

有些情况下，您会希望在插入发生后运行一些定制的后端函数。

这方面的一些例子有:

1.  在服务器端执行一些验证。例如，在银行应用程序中，验证存款不能小于 0。
2.  在单个事务中插入一些相关数据。例如，在笔记应用程序中，每当更新笔记时，创建一个修订。

## 什么是 Postgres 触发器？

触发器与表或视图相关联，每当事件发生时触发。“事件”是插入、删除、更新或截断。

触发器最终会调用一个函数，该函数会在特定事件发生时自动调用。

可以在下列情况下调用触发器:

*   手术前。
*   手术后。
*   而不是手术。

## 在服务器端验证

考虑以下类似银行的应用程序的数据模型:

`customer` — `id, name`

`account_savings` — `account_no, customer_id, is_active, created_at`

`deposit_activity` — `account_no, deposit_amount, deposit_time`

`withdraw_activity` — `account_no, withdraw_amount, withdraw_time`

为了演示第一个场景，我们将在插入到`deposit_activity`之前执行基本的验证。如果`deposit_amount`小于或等于 0，或者帐户处于非活动状态，我们希望确保不会发生插入。

让我们创建一个触发器。您可以在 Hasura 控制台的“SQL”选项卡中复制粘贴: