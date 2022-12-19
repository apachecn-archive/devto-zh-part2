# GraphQL 和 Rails 简介

> 原文：<https://dev.to/cohere/a-brief-introduction-to-graphql-and-rails-250i>

在过去的 9 个月里，我们将 GraphQL 和 Rails 作为 API 平台应用于客户端项目。这些项目中的大多数都依赖于用户界面和数据模型的快速迭代，因为我们遵循产品市场适应性的信号。Rails 和 ActiveRecord 继续提供令人难以置信的灵活的数据管理和持久性技术，GraphQL 作为一个用于剔除虚假数据、推出新字段和以一种与持久层分离的安全方式取消现有字段的[接缝](https://www.informit.com/articles/article.aspx?p=359417&seqNum=3)的适用性很好地补充了这一点。

也就是说，一般来说，GraphQL，特别是 Rails 中的 GraphQL，在上车之前有一些值得考虑的注意事项:

*   虽然市场营销会告诉你 GraphQL 背后的概念很简单，但 GraphQL 相当复杂。GraphQL 是一个分布式数据库，由四个主要部分组成:强类型数据模型、持久化和检索层、传输层和客户端(其中很多都有缓存！).独立理解这些事情中的每一件都很困难，但是它们之间的相互作用和微妙的紧张关系将复杂性带到了一个更高的层次。

*   GraphQL 在设计上明显没有 REST T1 或 T2 HATEOS T3 那么固执己见。这迫使我们做出更多关于如何设计我们的 API 以及如何做出改变的决定，而不是依赖 [json:api spec](https://jsonapi.org) 。

*   GraphQL 生态系统仍然感觉稍微偏左，处于技术成熟度生命周期的盈亏平衡点。该社区充满活力，不断发展。此外，他们还投资工具，以极快的速度提高生活质量(这是福也是祸)。

话虽如此，如果你选择加入 Github、T2、Shopify 和其他在 Rails 应用上构建 GraphQL APIs 的行列，这里有一些建议可以帮助你减轻负担。

1.  加入 [GraphQL 松弛](https://graphql-slack.herokuapp.com/)。是的，我们都对 Slack Overflow 有点不知所措，但那里的社区既友好又乐于助人。此外，这里也是 [GraphQL Ruby 库](http://graphql-ruby.org/)的开发人员出没的地方。与纯粹依靠自己的经验相比，直接接触使用 Ruby 和 GraphQL 的人让我提升了很多。
2.  看一看 [GraphQL Rails 示例](https://github.com/wecohere/graphql-rails-example)，它总结了我在过去 6 个月中收集的关于实现和测试 Rails GraphQL 服务器的一些经验。是的，特性测试使用的是 [cucumber-js](https://github.com/cucumber/cucumber-js) 而不是 Ruby。这是因为我们 99%的工作都是在以 [Apollo](https://www.apollographql.com/) 为客户端的 react-native 和 vue 应用程序中完成的，使用你的客户端应用程序使用的相同堆栈进行功能测试对于发现和隔离奇怪的角落情况非常有用。
3.  通读 Shopify 的人编写的这篇优秀的 GraphQL APIs 设计指南。
4.  接受这一点，找到 GraphQL 的精髓需要时间，无论是作为一种设计理念还是 Rails 实现的特定习惯用法。我发现我的学习是逐步进行的，停滞期最终导致概念上的突破。
5.  拥抱服务对象。突变可以很快变得复杂，引发它们自己的事件，并一次进行多个数据更改。虽然我的变异甚至一些查询都是从方法开始的，但是一旦它们到达十几行代码，就很快被提取为可以在 rspec 级别测试的对象。

总的来说，我对 Rails 和 GraphQL 应用作为 API 驱动的
平台的核心表现很满意。