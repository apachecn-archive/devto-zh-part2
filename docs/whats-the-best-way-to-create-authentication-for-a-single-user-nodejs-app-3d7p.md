# 为单用户 Node.js 应用程序创建身份验证的最佳方式是什么？

> 原文：<https://dev.to/flaviocopes/whats-the-best-way-to-create-authentication-for-a-single-user-nodejs-app-3d7p>

我的应用程序不会有一个以上的用户。

想法:当应用程序第一次加载时，如果是第一次，它会创建一个令牌并发送给客户端(JWT？).它在其配置文件中将`initialized`设置为 true。

那么客户端将使用该令牌。没有其他客户端可以连接。

如果客户端丢失了令牌，除了在应用配置文件中将`initialized`设置回`false`之外，什么都不能做。

你还会用其他策略吗？