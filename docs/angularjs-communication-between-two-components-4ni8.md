# 两个组件之间的角度通信

> 原文：<https://dev.to/nikolaof/angularjs-communication-between-two-components-4ni8>

你好。

我正在使用 angularJS 编写一个 SPA，到目前为止，我已经为这个应用程序创建了两个不同的组件，一个叫做`loginModal`，另一个叫做`listFiles`。

在 html 中，我称它们为

```
<login-modal></login-modal>
<list-files></list-files> 
```

Enter fullscreen mode Exit fullscreen mode

在初始化`loginModal`组件时，我检查是否存储了有效的 JWT 令牌，如果没有，我请求登录。如果登录成功，模式关闭，JWT 令牌被存储。除了 JWT，还从服务器检索定义目录路径(对于特定用户)的字符串。

现在，在成功登录后，我想要实现的是以某种方式触发 listFiles 组件中的一个方法，使用之前获取的目录字符串调用 API。

这样的事情有可能吗？有人能给我提供一些关于如何做这件事的信息资源吗？

此外，欢迎任何其他想法。

谢谢你。