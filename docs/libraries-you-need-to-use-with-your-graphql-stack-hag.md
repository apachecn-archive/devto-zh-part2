# 一些增强 Graphql 堆栈的库

> 原文：<https://dev.to/pedrammarandi/libraries-you-need-to-use-with-your-graphql-stack-hag>

[![enter image description here](../Images/627ccbec1b92297ad6ecca9057577876.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qvxLRGvH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/be1cy1wdqw2ait8hapg2.png)

Hi DEV 社区👩‍💻👨‍💻,

我第一次使用 Graphql 已经有几个月了。毫无疑问，我可以说它极大地提高了我的代码质量。如果你渴望采用这种堆栈，我建议你看看这篇关于 Graphql 的优缺点的文章" [Why GraphQL:优点、缺点&替代品](https://www.robinwieruch.de/why-graphql-advantages-disadvantages-alternatives/)"。

我将写一组我目前在 Graphql 上使用的工具，它们可能会帮助您充分利用 Graphql 堆栈。

### Graphql 客户端

一个好的 Graphql 客户端可以极大地改善前端和后端代码。他们提供不同的解决方案来覆盖你的应用中的广泛领域，比如认证、缓存、获取数据、处理错误和查询。这些客户增长很快，他们聚集了一个非常酷的开发者社区，我认为他们有更多的潜力。

我建议阿波罗，但以下客户太酷了:

1.  [阿波罗](https://www.apollographql.com/)(客户端&服务器)
2.  [图。酷](https://graph.cool)(仅服务器端)
3.  Prisma (一个用于服务器端的 Graphql 表单)
4.  [脸书继电器](https://facebook.github.io/relay)(前端)

### 重用 Graphql 文件

与其把所有东西都放在 JS 文件中，不如把非 JS 代码放在 Javascript 文件之外。为了将 Graphql 语法从我们的 JS 中去掉，并且重用它，我们可以创建。然后使用 Webpack 加载器，我们可以简单地导入。graphql 文件。

`import { fetchActiveUsers } from './users.graphql';`

### 图 ql 游戏场

Graphql 拥有令人惊叹的平台，可以让你查询你的 API 并获取结果。就像 Postman 为普通 API 调用所做的一样。以下是我用过的游乐场列表:

1.  [普里斯马游乐场](https://github.com/prisma/graphql-playground)
2.  [图 ql](https://github.com/graphql/graphiql)

### 查询批处理&缓存

使用 Graphql，您会遇到重复查询被发送到数据库的问题，您需要一种方法来解决这个问题。这通常发生在你的数据有层次结构的时候，比如文章和作者的数组。例如，你有 30 篇文章是由两个作者写的。一个普通的 Graphql 解析器将分别查询每篇文章的作者。脸书数据加载器将帮助您解决这个问题。它将只在单个请求中缓存数据，因此应用程序的所有其他部分将从 DataLoader 缓存中提取数据，而不是查询您的数据库。

> DataLoader 是一个通用实用程序，用作您的
> 应用程序的数据提取层的一部分，通过批处理和缓存，为各种远程数据源(如数据库或
> web 服务)提供一个简化的和
> 一致的 API。

[Facebook 数据加载器](https://github.com/facebook/dataloader)

*注意:DataLoader 不仅适用于 Graphql，它还适用于任何其他类型的数据存储和语言。*

我可以在这里提到更多的库，但是我发现这些库对于一个合适的 Graphql 环境是必不可少的。让我知道是否有其他的库对你的项目有帮助。