# Cloudflare 的服务人员

> 原文：<https://dev.to/kayis/service-workers-at-cloudflare-2g66>

[*Flickr 上克里斯多夫的封面图片*](https://www.flickr.com/photos/christopherf/)

Cloudflare 有一个新产品，叫做 [Workers](https://blog.cloudflare.com/cloudflare-workers-unleashed/) ，他们允许你在*Edge*上运行 JavaScript 代码，这基本上是*云*的 outta rim。酷的是，它基于一个标准:[服务人员](https://developer.mozilla.org/de/docs/Web/API/Service_Worker_API)！

## 为什么

有时你想用你的 Web 应用程序做一些不直接属于它的事情。比如修改请求或响应或缓存。有时你想这样做*和*阻止客户端访问这些修改代码。

## 什么

服务人员基本上是透明的。例如，它可以截取一个`fetch`事件并以某种方式修改它。设置不同的标题，从缓存或不同的服务器获取一些内容，等等。

但是它们在客户端上运行。

Cloudflare 工作人员在 Cloudflare 基础架构的边缘运行，就在您的客户端和服务器之间，但他们拥有与服务人员相同的 API。

这意味着，你可以编写一个在你的 dev 客户机上运行的服务工作器，但是不能在你的 users 客户机上运行，因为你在工作器和应用程序之间有一个清晰的接口。

这可用于编写静态前端并通过 Worker 路由请求，例如添加通常需要设置整个服务器的 API 秘密。由于它运行在*边缘*上，代码在地理上靠近客户端而不是服务器，这减少了延迟。

## 结论

Cloudflare Workers 似乎是 AWS Lambda 等解决方案的一个不错的替代方案。虽然 [Lambda@Edge](https://docs.aws.amazon.com/lambda/latest/dg/lambda-edge.html) 是一个类似的产品，但工人们坚持服务工人标准，这可能会带来将其移植到另一个提供商或在需要时在客户端和边缘之间移动代码的好处。

总而言之，它们赋予前端更多的能力，允许更少的基础架构。