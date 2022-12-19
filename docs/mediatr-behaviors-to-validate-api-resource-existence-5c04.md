# 验证 api 资源存在的 mediasr 行为

> 原文：<https://dev.to/engberrg/mediatr-behaviors-to-validate-api-resource-existence-5c04>

[![](img/6d44b7c0663c44b2ab05472efc6a8a31.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f3VX5eAt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/475/1%2AUGlyWoUBAwLEzJFPlz8fmQ.png) 

<figcaption>联发科 logo 借用了 https://bit.ly/2N4Jz6S</figcaption>

在构建健壮的 API 时，验证和描述性的错误消息和状态代码非常重要。

在这篇文章中，我将向您展示如何通过添加 MediatR 行为来处理资源存在的验证，从而消除请求处理程序中的重复代码。

[mediator](https://github.com/jbogard/MediatR)是。NET 支持请求/响应、命令、查询、事件等。如果你没看过，就去看看吧。

在 MediatR 中，有一种叫做行为的东西，它允许你为请求建立自己的管道。我将它们用于横切关注点，比如日志记录和请求参数的验证。

最近，当一些端点被开发用于微服务时，我在我的请求处理程序中与重复代码作斗争，这是必须的。

为了给你一个例子，让我们从几个 API 端点开始，以及用重复代码实现请求处理程序。

本例中的端点是:

PUT /groups/{id}

帖子/组/{ id }/用户

控制器: