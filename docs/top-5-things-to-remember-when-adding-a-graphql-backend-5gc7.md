# 添加 GraphQL 后端时要记住的 5 件事

> 原文：<https://dev.to/hasurahq/top-5-things-to-remember-when-adding-a-graphql-backend-5gc7>

[![](img/4a9d5fcae28fad311f68b548d90a9dd3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xTnBc1RF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/top-5-things-to-remember-when-adding-a-graphql-backend-6e08709d1e6b/1-GbDcwTfHonrUFdFjtPE8BA.jpeg)

采用 GraphQL 可以显著提高特征速度，缩短产品开发周期。这导致它被每个人用于生产系统，从财富 500 强公司，如 Intuit，沃尔玛和纽约时报，到全球初创公司，包括 Airbnb，Docker 和 Github。

然而，就像任何闪亮的新技术一样，重要的是你如何使用它。获得 GraphQL 好处的关键在于深思熟虑的实现。如果您打算将 GraphQL 引入您当前的堆栈，请记住以下几点:

*   从零开始实现 GraphQL 服务器的学习曲线是艰难的。但是不断改进的社区工具的可用性使得这项工作变得更加容易。
*   找出 GraphQL 服务器在当前后端架构中的位置非常重要。
*   您需要仔细考虑 GraphQL 服务器的性能。同样，社区工具会有所帮助。
*   与 REST APIs 相比，保护 GraphQL APIs 和查询本质上是不同的，尤其是在数据访问控制列表(ACL)方面。
*   需要显式添加对特定数据的支持，例如实时数据或地理位置等数据类型。

我们在 [**可编程网页**](https://www.programmableweb.com/news/top-5-things-to-remember-when-adding-graphql-backend/analysis/2018/10/01) 上更详细地探讨了这些问题。