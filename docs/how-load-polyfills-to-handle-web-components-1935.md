# 如何加载 polyfills 来处理 Web 组件？

> 原文：<https://dev.to/grandemayta/how-load-polyfills-to-handle-web-components-1935>

在本文中，我将向您解释我是如何处理最新的 ES6 和 Web 组件特性的。如果你开始使用 Web 组件，你应该知道 Chrome、Firefox、Safari 和微软都很好地支持它们[今年宣布](https://blogs.windows.com/msedgedev/2015/07/15/microsoft-edge-and-web-components/)他们正在努力支持它们。
因此您不需要静态装载多孔填料，从而增加最终包装的重量。

由于这个原因，我创建了一个特定的包，以便在浏览器不支持时在运行时加载 polyfills。我按类型列出了最常用的功能。你可以在下面找到代码: