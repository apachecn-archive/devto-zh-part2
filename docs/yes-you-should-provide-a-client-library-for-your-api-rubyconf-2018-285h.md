# 是的，你应该为你的 API 提供一个客户端库！(RubyConf 2018)

> 原文：<https://dev.to/dazuma/yes-you-should-provide-a-client-library-for-your-api-rubyconf-2018-285h>

2018 年 11 月 13 日，我发表了“是的，你应该为你的 API 提供一个客户端库！”在加州洛杉矶的 RubyConf。该讲座讨论了为基于 HTTP 的 API 提供客户端库的好处，以及编写它们的一些技术。

这是我在演讲中提到的或者密切相关的一些资源。欢迎在评论中分享更多内容！

## 谈话资源

*   [会议视频](https://confreaks.tv/videos/rubyconf2018-yes-you-should-provide-a-client-library-for-your-api)
*   [speaker deck 上的幻灯片](https://speakerdeck.com/dazuma/yes-you-should-provide-a-client-library-for-your-api)

## 代码生成框架

*   OpenAPI 是一个开放的标准，对于 REST APIs 来说非常有效。
*   gRPC 是一个高性能的 RPC 框架，最初由 Google 开发，使用 HTTP/2 和协议缓冲区。
*   Apache Thrift 是一个由脸书开发的 RPC 框架。

### 与 OpenAPI 相关的资源

*   [模式优先的 API 设计](https://yos.io/2018/02/11/schema-first-api-design/)是一篇很好的关于 OpenAPI 的文章。
*   [从代码](https://www.blazemeter.com/blog/how-to-generate-openapi-definitions-from-code)生成 OpenAPI
*   [招摇集成库](https://swagger.io/tools/open-source/open-source-integrations/)
*   [Swagger 编辑器](https://swagger.io/tools/swagger-editor/)用于编辑 OpenAPI 规范。

### gRPC 相关资源

*   [协议缓冲区](https://developers.google.com/protocol-buffers/)是 gRPC 使用的结构化数据序列化机制。
*   [HTTP/2: Smarter at Scale](https://www.cncf.io/blog/2018/07/03/http-2-smarter-at-scale/) 是一篇描述作为 gRPC 基础的 HTTP/2 的有用文章。
*   HTTP/2 上的 gRPC:设计一个健壮的高性能协议是另一篇有助于理解 gRPC 的文章。

### 相关资源

*   [讨论 Oracle 和 Azure 如何管理 API 的幻灯片](https://schd.ws/hosted_files/apistrat18/b2/APIStrat-presentation-joe-levy-david-justice.pdf)
*   [GraphQL](https://graphql.org/learn/) 是一个数据模式系统，在某些方面类似，但范围更有限。您可能会发现它与完整的 API 规范框架结合使用非常有用。
*   OpenCensus 是一个用于收集和报告仪器信息的开放框架。

## 其他 API 设计资源

*   [Google 的 API 设计指南](https://cloud.google.com/apis/design/)是公开的，描述了 Google 的 API 设计原则，其中一些是 Google 特有的，但大部分应该广泛适用于一般的 API。

## 感谢

感谢杰夫，他做了大量的初步研究，并在 CodeBEAM 上展示了这个演讲的早期版本，感谢格雷厄姆的评论和提供的建议。感谢谷歌赞助我今年在 RubyConf 的亮相。