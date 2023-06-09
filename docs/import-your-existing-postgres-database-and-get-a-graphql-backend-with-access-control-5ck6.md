# 导入您现有的 Postgres 数据库，并获得一个带访问控制的 GraphQL 后端

> 原文：<https://dev.to/hasurahq/import-your-existing-postgres-database-and-get-a-graphql-backend-with-access-control-5ck6>

[![](img/fa996263d3718cdd41a5b48dc8b36fd3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KQFM2KUU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/01/0-zW6_54PZ8WTmzijL.png)

如果你正在使用 [Postgres](https://hasura.io/learn/database/postgresql/introduction/) (比如说作为你后端栈的一部分，比如 Ruby on Rails，Django)，你可以很容易地在你现有的数据库上用 Hasura GraphQL 引擎测试 GraphQL APIs。

用自己熟悉的工具(`pg_dump`和 [psql](https://hasura.io/learn/database/postgresql/installation/psql/) )导出和导入数据库就这么简单！

Hasura GraphQL 引擎无需您编写模式、解析器等就能工作。您在 Postgres 中建模您的模式，我们通过声明性权限在 GraphQL 上公开它，以便您拥有访问控制。

我做了一个 2 分钟的视频，我在拉屎(🚽har har)并在其上展示 GraphQL。看看吧，让我知道你的想法！

[https://www.youtube.com/embed/Tt3bLzNTork](https://www.youtube.com/embed/Tt3bLzNTork)

[***Hasura***](https://goo.gl/QfCiQV)*让你在任何 Postgres 数据库上即时实时 GraphQL APIs，而无需编写任何后端代码。*

*对于那些刚接触 Hasura GraphQL 引擎的人来说，* [***这个***](https://docs.hasura.io/1.0/graphql/manual/index.html) *是一个入门的好地方。*