# 限制 JavaScript 中的并发操作

> 原文：<https://dev.to/maciejcieslar/limiting-concurrent-operations-in-javascript-3258>

通常，执行我们代码的机器资源有限。一次做所有的事情不仅会造成伤害，还会让我们的进程停滞不前，让它完全停止响应。

当我们想要抓取 100 个网站时，我们应该抓取，例如，一次抓取 5 个，以不占用所有可用的带宽。只要一个网站被抓取，下一个就准备好了。

一般来说，为了获得更好的性能和节省资源，所有“繁重”的操作都应该及时安排，而不是一次全部执行。

## 实现

如果你熟悉我之前关于[实现承诺](https://www.maciejcieslar.com/implementing-promises-in-javascript/)的帖子，那么你会注意到很多相似之处。