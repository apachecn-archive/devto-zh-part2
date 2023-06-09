# 在 CI/CD 系统中使用 Hasura GraphQL 引擎

> 原文：<https://dev.to/hasurahq/using-hasura-graphql-engine-with-a-ci-cd-system-219i>

使用 Postgres 和 Hasura GraphQL 引擎(HGE)的应用程序可能需要在 CI/CD 环境中运行它们来进行测试。让我们看一下在已经有一些测试数据的情况下运行 Postgres 和 HGE 的一般工作流程。

<figure>[![](img/7027fb7e5eacbfc20abbf0564d74f9b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--imz6rnh_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/using-hasura-graphql-engine-with-a-ci-cd-system-9f7e87b3a0df/1-_Tu7vK-SPpAvgp1figE8YQ.png) 

<figcaption>使用 Hasura 搭配 GitLab、Jenkins、Circle CI、Travis CI、Drone CI</figcaption>

</figure>

## Postgres

第一步是运行 Postgres (≥9.5)，因为 HGE 依赖它。最简单的方法是启动 Docker 容器，如果你的 CI/CD 平台支持的话。

```
docker run -d -p 5432:5432 postgres 
```

这将使用名为`postgres`的默认数据库启动 Postgres。数据库 url 将是`postgres://postgres:@localhost:5432/postgres`。

为了等到 Postgres 开始运行，我们可以使用一个简单的 bash 函数: