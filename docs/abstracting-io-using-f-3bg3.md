# 使用 F#抽象 IO

> 原文：<https://dev.to/jamesnovino/abstracting-io-using-f-3bg3>

在这篇文章中，我将讨论在 F#中统一 IO 访问模式的方法。统一使我们能够抽象底层基础设施实现，同时使我们能够为所有 IO 模式提供一致的接口。共享访问方法实现了对重试、指标、日志等内容的一致包装。

[阅读更多](https://medium.com/jettech/abstracting-io-using-f-dc841519610e)