# 使用阿波罗预言处理和记录 GraphQL 错误

> 原文：<https://dev.to/theglenn/handling-and-documenting-graphql-errors-using-apollo-prophecy-5h27>

*你会失败……成功*

*结账[原中等岗位](https://hackernoon.com/handling-and-documenting-graphql-errors-using-apollo-prophecy-apollo-7a32de3547a1)T3】*

所有计算机程序都有错误；它们可能很难维护，但是正确处理它们无疑是构建应用程序最关键的部分。

在客户端/服务器架构的环境中，我们需要服务器输出**格式良好的**和**易于识别的错误**，客户端可以**无缝读取、处理**和**处理**，以便**成功失败**。

[![**E.g. a successful failure**](img/a09634a15f8f0dbef900bbc6d2c9393b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---xfaLHib--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AH4fjHIsJZblHY6YgiHMoTg.jpeg)

GraphQL 驱动的 API 没有**例外**(有意双关😏)到这个规律。以下是 GraphQL 规范的最新草案*(孙，2018 . 6 . 10)*关于错误输出应该如何格式化的内容。

> 如果操作遇到任何错误，响应映射必须包含一个带有关键字`errors`的条目。
> 每个错误都必须包含一个带有关键字`message`的条目，该条目带有错误的字符串描述，旨在为开发人员理解和纠正错误提供指导。
> GraphQL 服务可能会通过关键字`extensions`提供额外的错误条目。如果设置了这个条目，它的值必须是 map。这个条目是为实现者保留的，他们可以以他们认为合适的方式向错误添加附加信息，并且对其内容没有附加限制。

考虑到这一点，典型的错误对象应该是这样的:

```
 ...  "errors":  [  {  "message":  "Only Prophets can do this",  "locations":  [  ...  ],  "path":  [  ...  ],  "extensions":  {  "code":  "NOT_A_PROPHET",  "timestamp":  "Thu Jun 21 17:03:00 UTC 2018"  }  }  ]  ... 
```

Enter fullscreen mode Exit fullscreen mode

请记住，我们希望错误输出是“**格式良好的**和**易于识别的**”，这意味着它应该至少包含一个可以被计算机无缝处理的字段。

要考虑的第一个候选项是`message`，一个*“针对开发人员的错误的字符串描述[…]”。*因为它被格式化为人类可读，所以它可能是一个包含不想要的字符 *(%，à，$，,@，空格等)*的有表现力的长字符串，因此不理想。

根据规范，`extensions`应该是`errors`的任何附加条目的专用空间。在这里，它让我们能够附加一个代码密钥，提供一个**机器可读的**数据，可以被**无缝读取、处理**和**处理**。

```
 if (error.extensions.code === "NOT_A_PROPHET") {
      // Do Something
    } 
```

Enter fullscreen mode Exit fullscreen mode

## 向前移动🏇

我们刚刚看到了关于如何在 GraphQL API 的上下文中输出错误的指南。有了它，我们应该能够:

*   在我们的解析器中抛出并输出**符合规范的**和**可识别的**错误——多亏了`extensions`。

*   客户端识别并处理错误到**失败成功**。

然而，该规范并没有为 API 错误文档、重试或故障处理等问题指定指导原则，这意味着有无数种方法可以为此目的适当地安排我们的代码库。

没有明确的约定让我建造了 [**【阿波罗预言】**](https://github.com/theGlenn/apollo-prophecy) 。

## 异教之道

首先，让我们举例说明没有 [**阿波罗预言**](https://github.com/theGlenn/apollo-prophecy) 维护错误会是什么样子。为此，我们将使用 **Apollo Server** ，这是一个优秀的、符合规范的、功能齐全的、维护良好的 nodeJS 的 GraphQL 服务器实现。

因为我们使用的是 [Apollo 服务器](https://github.com/apollographql/apollo-server)，所以我们可以使用构造函数`ApolloError(message, code)`:使用这个构造函数抛出的错误会产生一个符合规范的 JSON 输出，如上图所示。

```
 throw new ApolloError("Only Prophets can do this", "NOT_A_PROPHET"); 
```

Enter fullscreen mode Exit fullscreen mode

为了让我们更容易存储错误，我们可以按照以下方式组织我们的服务器端代码: