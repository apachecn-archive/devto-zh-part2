# 探戈 _ 推特-克隆

> 原文：<https://dev.to/usefulsomebody/tangotwitter-clone-36el>

[![TANGO](img/14ac94bb361324a6d4c1117f6440e7c6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--22NhBEGY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2azfz91cp1tz5vqewk8z.png)

# 我造了什么

一个类似 twitter 的单页应用程序(SPA ),由 Vue.js、GraphQL 和 Pusher 组成，在 Laravel 上实现

## 演示链接

工程住在[这里](http://projectpage.feedia.co)

**模拟账户**

*   电子邮件:[asapabedi@gmail.com](mailto:asapabedi@gmail.com)
*   密码:自由落体

## 链接到代码

托管在 [GITHUB](https://github.com/asapabedi/TANGO_Twitter-clone) 上的源代码

# 我是如何建造的

我首先使用 Laravel 构建了一个 GraphQL API，然后使用 Vue.js、用于前端的 Vue 路由器和用于实时活动的 Pusher。

# 附加信息

使用 Vue.js、Vuex 和 Pusher，我构建了一个 SPA 应用程序，我允许 auth 部分由 laravel 开箱即用身份验证处理，因此一旦用户登录，它将加载单页应用程序。虽然，它不是 twitter 的完美复制品，但它有一些关键特征。在这个应用程序中，用户可以发送推文和回复，关注和取消关注用户，所有这些都是通过推送渠道完成的。