# AdonisJS(第 2 部分)的内幕——服务提供商

> 原文：<https://dev.to/patrickodacre/a-look-under-the-hood-of-adonisjs-part-2---service-providers--5efj>

> 订阅我的 YouTube 频道获取更多关于使用 VueJS 和 AdonisJS 的教程——一个用于 NodeJS 的 MVC 框架！

跟随我探索 AdonisJS 中的服务提供者。

服务提供者是我们将新对象的指令添加到 IoC 容器中的方式。

我们将一起讨论文档的每个部分，并浏览源代码，看看它们实际上是如何工作的。

我们将看看 **ViewProvider** 和 **AppProvider** ，以及 **Config** 类，以及它如何帮助我们管理我们的对象配置。

[https://www.youtube.com/embed/8ZIiraVxtvQ](https://www.youtube.com/embed/8ZIiraVxtvQ)

* * *

在本系列的第 1 部分中，我们深入研究了 IoC 容器。如果你没看那个视频，一定要先[看](https://dev.to/patrickodacre/a-look-under-the-hood-of-adonisjs-part-1---the-ioc-container--3b0n)！

在这些视频结束时，您将会对 IoC 容器和服务提供商的工作方式以及他们的帮助有所了解。