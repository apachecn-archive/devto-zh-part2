# 如果在前端使用单页面应用，如何在微服务中保护后端 API？

> 原文：<https://dev.to/djviolin/how-do-you-protecting-your-backend-api-in-your-microservice-if-you-use-a-single-page-application-on-the-frontend-2ie1>

我目前正在做一个宠物项目，我打算学习一个“简单”的微服务架构，这意味着没有通用渲染(或者我认为它会很简单)。

我有一个用 Go 写的后端 REST API，它代理一个第三方 API。后来，它将被 Postgres 数据库所取代。现在在`127.0.0.1:8080/api/v1`下提供内容。

我有一个用 Go for SPA 编写的静态文件服务器，它服务于用 Vue.js 编写的单页面应用程序，由`vue-cli`生成。该文件服务器在`127.0.0.1:8081`下提供内容。这个 vue 应用程序使用`axios`从 API 服务器查询数据，影响 AJAX 请求。

这是我遇到的第一个问题:如果我没有将 API 服务器上的静态文件服务器的地址列入白名单，那么服务器就不会显示任何 CORS 头，比如:

```
Access-Control-Allow-Origin: http://127.0.0.1:8081
# or
Access-Control-Allow-Origin: * 
```

Enter fullscreen mode Exit fullscreen mode

浏览器忽略了该请求。

* * *

另一个问题(嗯，不是真正的问题)是我不得不删除`axios`中的相对 URL(因为它们不再在同一个服务器上了)，这意味着替换:

```
return this.$http.get(`/api/v1/${this.id}/`, { 
```

Enter fullscreen mode Exit fullscreen mode

同:

```
return this.$http.get(`//127.0.0.1:8080/api/v1/${this.id}/`, { 
```

Enter fullscreen mode Exit fullscreen mode

在 vue app 里。

我做错了什么吗？我应该如何从另一台服务器的 SPA 向 API 服务器发出请求？

* * *

我如何保护我的后端 API 不被人无限制地使用？

我想我一定要实现一个限速器。我计划使用 HAProxy 进行负载平衡，这可能是它应该限制那些想要执行 DDOS 攻击或使用数据库作为其网站免费喷泉的用户的请求的地方。

但是没有其他方法限制 API 从浏览器栏访问它，如果他们输入 API 服务器的地址？GraphQL 呢？同样的情况？

* * *

这个网站将不会有任何身份验证，用户管理，或在开始时限制访问。但是如果我将来尝试实现这样的特性，我如何安全地实现用户认证，而不是搬起石头砸自己的脚呢？如果我完全想放弃服务器端或通用渲染，那么做这件事的最佳实践是什么？

我想拥有一个 SPA 和一个 API 服务器，这也将是网络和移动应用的良好开端。