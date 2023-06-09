# 开始感觉像一个合适的 UI 框架

> 原文：<https://dev.to/progrium/starting-to-feel-like-a-proper-ui-framework-2i0a>

首先，昨天我给 vtemplate 添加了插槽支持。我将在演示中展示。今天早上，我开始将它推向一个真正的 web UI 框架的方向。我想完成的主要事情是让您在 Go 组件旁边的文件中定义 HTML 模板。这还包括设置资产编译，因为它们需要从 WebAssembly 二进制文件中访问。

我使用了 [vfsgen](https://github.com/shurcooL/vfsgen) ,它让你定义一个`http.FileSystem`变量，然后用它生成一个替换文件，这个替换文件给你一个返回相同数据的变量，但是来自内存中的压缩数据。这需要做一些实验来得到正确的结果，但是我喜欢我最终得到的系统。

我还想简化 Go 组件如何引用它们的模板数据，所以我创建了一个助手，它查看调用它的文件名，然后查找具有相同文件名但扩展名为`.html`的模板。

渲染帮助器仍然对指令和赋值器进行硬编码，但至少从注册表中查找组件和自定义元素。你只需在`init()`中注册你的组件。

最后，我更新了开发服务器，因为现在它需要观察 HTML 文件。我更新了引导页面的 HTML，只重新加载 WebAssembly 实例，而不是整个页面。这给了它更多的热重新加载的感觉，即使它仍然在重新加载整个应用程序。

接下来呢？我想开发服务器可能需要做更多的工作，但是我可能已经准备好用它来构建一个真正的应用程序了。

[https://www.youtube.com/embed/w2shFCUdnos](https://www.youtube.com/embed/w2shFCUdnos)