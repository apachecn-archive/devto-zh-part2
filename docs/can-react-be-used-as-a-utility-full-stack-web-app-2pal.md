# react 可以作为实用的全栈 web app 吗？

> 原文：<https://dev.to/presto412/can-react-be-used-as-a-utility-full-stack-web-app-2pal>

我只是在想，既然 react 是/已经

*   ES6 兼容
*   NPM 支持
*   请求支援

考虑这样一种情况，我们有一个基于 UI 的接口来访问不同网站中的一些数据，我们使用 APIs 请求来获取数据并将其呈现给前端。react 可以作为独立的前端+后端解决方案用于小任务吗？

既然 react 有自己的路由机制，那么 express 应用程序的处理可以通过 react 本身的导入库来完成吗？假设请求中传输的大小非常小，处理响应是基本的字符串操作/html 解析，并且不需要数据库，客户端的存储就可以了。

为什么我会想到这个？无需维护充当 react 应用程序中间件的服务器。

编辑:对于这样的 app，把代码编译成 react native，每个使用 app 的用户都是自己的服务器。可能吗？

想法？