# 转到 Objective-C 桥梁工程！

> 原文：<https://dev.to/progrium/go-to-objective-c-bridge-works-5kl>

昨晚晚些时候，我取得了突破，最终让 Go 目标-C 桥工作。这只是显示了一个“Hello world”窗口，所以看起来好像就这么多了。但是最困难的工作是让桥代码对任何类型的方法调用都有效。

[![img](img/b32cac130393cc65b5c89c7b9ff37ba1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vAG2--Bz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qyb140ytgq7zpnr27u09.png)

这意味着任何苹果 API 都可以使用。由于苹果对他们的每一个 API 都有模式，我可以为它们生成包装器，所以感觉就像 native Go 一样。