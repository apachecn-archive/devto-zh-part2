# 虚拟 DOM

> 原文：<https://dev.to/vjnvisakh/virtual-dom-1oi0>

所以为什么反应这么快。他们说魔法是因为虚拟世界。

虚拟 DOM 是一个编程概念，系统通过它在内存中存储 DOM 的副本。

每当 VDOM 发生变化时，都会与旧的 VDOM 进行比较，只有这些变化会被提交到实际的 DOM 中。

这也叫“diffing”。

如果我们不使用 React，浏览器需要花费大量的时间和 API 来更新 DOM。但是使用 React 更新 VDOM 更容易，因为它是一个 JS 对象。这个对象以这样一种方式进行了优化，它对浏览器 DOM 进行最少的调用来更新它。