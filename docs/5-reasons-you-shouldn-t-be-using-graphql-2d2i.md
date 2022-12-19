# 不应该使用 GraphQL 的 5 个理由

> 原文：<https://dev.to/bnevilleoneill/5-reasons-you-shouldn-t-be-using-graphql-2d2i>

[![](../Images/039db5c5b80dfa601efa95618367e5b0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cP3rCnWX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ArmWsDg6J6PtLVOhXb3zr7w.png)

GraphQL 很棒，它允许您以声明的方式工作，只选择您需要的信息或操作。

然而，就像任何其他工具一样，它不是一个[银弹](https://en.wikipedia.org/wiki/No_Silver_Bullet)。

在这篇文章中，我将讨论当替代方案是 REST 架构时不应该使用 GraphQL 的五个原因:

1.  REST 可以做 GraphQL 做的很多事情
2.  GraphQL 将使一些任务变得更加复杂
3.  您将无法通过 GraphQL 使用 web 缓存
4.  GraphQL 查询可能会有性能问题
5.  GraphQL 模式的工作方式可能是个问题

当然，这些情况可能并不总是适用于您的项目，但重要的是您意识到它们并考虑其含义。

[![](../Images/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

### REST 可以做很多 GraphQL 能做的事情

GraphQL 是 REST 开发 API 的替代方案，而不是替代品。

GraphQL 的主要特性是能够发送一个查询，只指定您需要的信息，并准确地获得这些信息。

但是您也可以使用 REST 来实现这一点，从在 URL 中传递您想要使用的字段的名称(自己实现解析和返回逻辑):

```
GET /books/1492030716?fields=title,pageCount 
```

而且实现起来也不难。很多语言都有[很多 JSON API 库](http://jsonapi.org/implementations/)。

您想要在 REST 中使用模式和强类型的好处吗？

使用 [JSON 模式](http://json-schema.org/)。

也有许多[库实现并支持](http://json-schema.org/implementations.html)这个规范。

你想在 REST APIs 中使用查询语言吗？

试试 [OData](https://www.odata.org/) 。

关键是有有效的替代方案。尤其是对于小型应用程序，使用 GraphQL 可能有些过头了。

当然，在有些情况下，实现这些库会很复杂，在这些情况下，使用 GraphQL 可能会更好，它本身就支持所有这些特性。

但是 GraphQL 也会让事情变得更复杂。

### GraphQL 会让一些任务变得更加复杂

不建议在简单的应用程序中使用 GraphQL(例如，每次都以相同的方式使用几个字段的应用程序)，因为它会增加复杂性，原因如下:

*   类型
*   问题
*   变异体
*   下决心者
*   高阶分量

这从维护的角度来看是不好的。

但是，即使 GraphQL 的使用是合理的，也可能存在一些复杂性。

错误处理和文件上传就是两个例子。

在 REST 中，检查响应状态是了解请求是否成功执行、是否存在服务器错误或者是否没有找到资源的唯一方法。

但是在 GraphQL 中，当错误发生时，您会得到类似于下面的内容:

```
{
  "data": null,
  "errors": [
    {
      "message": "Validation error...",
      "locations": [
        {
          "line": 5,
          "column": 6
        }
      ]
    }
  ]
} 
```

您必须解析这条消息才能知道是否有错误，不同的错误可能会有稍微不同的格式或添加一些自定义字段。

一些库，比如 Apollo client，帮助处理错误，但是不像在 REST API 中那么容易。

关于文件上传，这个特性不是 GraphQL 规范的一部分，所以实现就交给你了。一些选项包括:

*   使用 Base64 编码，这将使请求更大，编码/解码成本更高
*   为此，请使用单独的服务器/API
*   使用像 [apollo-upload-server](https://github.com/jaydenseric/apollo-upload-server) 这样的库，它实现了 [GraphQL 多部分请求规范](https://github.com/jaydenseric/graphql-multipart-request-spec)

第三个选项可能是最值得推荐的。然而，这意味着添加另一个依赖项来管理您的项目，并且它可能不适用于所有编程语言。

### 您将无法通过 GraphQL 使用网络缓存

我想强调 web 部分，即网络级别的缓存，因为你当然可以用 [Apollo 客户端](https://www.apollographql.com/client)的[内存缓存实现](https://www.apollographql.com/docs/react/advanced/caching.html)来实现数据库级别或客户端级别的缓存。

但是，在 HTTP 级别实现的存储请求内容的缓存(例如使用反向代理)可以减少服务器的流量，或者将频繁访问的信息保存在靠近客户端的位置(如内容交付网络)。

由于 REST API 提供了许多端点，您可以轻松地配置 web 缓存来匹配某些 URL 模式、HTTP 方法或特定资源。

在 GraphQL 中，只有一个端点(大多数情况下是 HTTP POST 端点)发送所有查询。因为每个查询都可能不同，所以使用这种类型的缓存比较困难。

减少 web 服务器流量的一个选择是使用带有 [persistgraphql](https://github.com/apollographql/persistgraphql) 的[持久化查询](https://blog.apollographql.com/persisted-graphql-queries-with-apollo-client-119fd7e6bba5)。

该工具为 GraphQL 查询分配标识符，生成一个映射查询和标识符的 JSON 文件。有了这个映射，客户机只把标识符和查询的参数发送给服务器，这样它就可以查找了。

然而，这又增加了一层复杂性，而且只能部分解决问题。

### graph QL 查询的性能问题

作为 API 的查询语言，GraphQL 赋予了客户执行查询的能力，以获得他们所需要的信息。

但是，如果客户机发送一个查询，要求大量的字段和资源，该怎么办呢？类似于“*给我发评论的用户信息*”:

```
author(id: '1234') {
  id
  name
  books {
    id
    title
    reviews {
      text
      date
      user {
        id
        name
      }
    }
  }
} 
```

您可以让您的用户运行他们想要的任何查询。GraphQL API 必须精心设计，而不仅仅是将其放在 REST API 或数据库之上。

对于复杂的查询，REST API 可能更容易设计，因为您可以有多个端点来满足特定的需求，并且对于每个端点，您都可以微调特定的查询来以有效的方式检索数据。

这可能有点争议，因为多个网络调用也会花费大量时间，但是如果您不小心的话，几个大的查询就会让您的服务器崩溃。

在 GraphQL API 中，有像 [Dataloader](https://github.com/facebook/dataloader) 这样的工具允许您批处理和缓存数据库调用，但在某些情况下，即使这样也不够，唯一的解决方案是通过计算最大执行成本或查询深度来阻止查询。所有这些解决方案都取决于您正在使用的库。

### GraphQL 模式可能有问题

通过允许定义模式，GraphQL 给了你很多好处，比如自动验证和 T2 自省。

但是模式是静态的，客户端将得到的响应取决于模式定义和它们所做的查询。

例如，您不能拥有比模式或查询中指定的深度更深的深度，您可以在运行时修改的模式，或[动态类型定义](https://github.com/absinthe-graphql/absinthe/issues/439)。

GraphQL 规范并没有涵盖这样的动态特性，所以您必须依赖于 hacky 解决方案或定制的服务器实现。

像 GraphQL Ruby 这样的库有一些机制来通过编程动态定义你的模式。这可能会解决一些需求。

另一方面，我见过有人创建非常通用的模式来尝试解决更复杂的需求:

```
type Query {
  request(query: String!): Response
}

type Response {
  body: String
} 
```

这是以牺牲 GraphQL 的大部分好处为代价的。

在这些情况下，还是坚持休息比较好。

### 结论

GraphQL 是一个强大的工具，有很多理由选择 GraphQL 而不是 REST。但是不要忘记为正确的工作选择正确的工具。

我在这里提出的观点可能并不总是适用，但值得考虑一下，看看是否可以解决。仍然有 REST 是有效方法的用例。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](../Images/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[你不应该使用 GraphQL 的 5 个理由](https://blog.logrocket.com/5-reasons-you-shouldnt-be-using-graphql-61c7846e7ed3/)首先出现在[博客](https://blog.logrocket.com)上。