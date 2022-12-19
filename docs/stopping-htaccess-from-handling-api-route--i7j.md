# 停下来。处理 API 路由的 htaccess

> 原文：<https://dev.to/ashokcodes/stopping-htaccess-from-handling-api-route--i7j>

我将一个网站的所有 http 请求路由到 index.php 文件，然后在那里以 MVC 方式处理它。但是，如果请求是由`/api`路线发出的，那么另一个文件 Api.php 应该处理它。我该怎么做呢？

[![Image](../Images/0bc23212139560d82498c491a44fad63.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JG0BRd6A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hmazkrpysg4flhuy66z1.png)

^:这是我尝试过的，但它只是给了我一个 500 错误

此外，任何关于这方面的最佳做法的见解，不胜感激。我正在尝试编写一个 PHP MVC 框架，所以我也想知道这是否是进行路由的正确方法