# 使用基于令牌的 REST API 认证客户端应用程序的一般流程是什么？

> 原文：<https://dev.to/jjjjcccjjf/what-is-the-usual-flow-in-authenticating-a-client-application-with-a-token-based-rest-api--30i>

大家好。我现在真的卡住了，无法进步。我很难理解使用基于令牌的 api 认证来认证客户端应用程序的正确流程。

以下是我目前掌握的情况:

客户端 app 提供登录屏幕->
客户端 app 向/api/auth route 发送/POST 请求- >
API 检查用户是否存在，返回访问令牌并刷新令牌- >
客户端 app 将访问令牌保存为 http-only cookie 或 localStorage - >
...卡住了(不知道如何处理刷新令牌)

我的思路停留在这一部分。

如果我在 API 端有用户集合，那么我应该如何存储我的刷新令牌？我知道有人建议把它保存在数据库或安全的地方。

但是这不会让我有重复的用户表之类的东西吗？

我真的不知道该怎么进行，而且我想我之前也没有看过关于这个**流**的优秀的原始资料。

如果你有一个教程来帮助我，或任何建议，我会很高兴地接受。我卡住了。😢

谢了。希望 dev.to 能帮帮我。