# Golang 模式-第 1 部分

> 原文：<https://dev.to/napicella/golang-patterns-5a64>

大家好！今天的目标是描述和记录一系列 golang 模式。

正如你从我以前的帖子中所知( [go concurrency](https://dev.to/napicellatwit/go-for-java-developers-or-is-the-java-concurrency-that-bad-6ff) 和 [go lambdas](https://dev.to/napicellatwit/go-for-java-developers-lambda-functions-to-the-rescue-655) )，我最近进入了 Go-land，并很快发现我从以前的经验中了解到的常见习语并不完全适用于 golang。

嗯，我发现有一些模式是如此普遍，以我的拙见，应该作为任何 golang 初学者指南的一部分。
例子中的所有代码都在 [github](https://github.com/napicella/go-patterns) 上。

## 组常数

标题说明了一切，我们希望在同一个名称空间中对常见的常量进行分组。