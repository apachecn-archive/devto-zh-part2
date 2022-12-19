# ⌛️The 在围棋中错过了第一次

> 原文：<https://dev.to/alirezabashiri/the-missing-strftime-in-go-51ld>

昨晚我在阅读时间包文档，但我找不到`strftime`的实现，所以我做了一点研究，我确实发现在 Go 时间包中没有`strftime`这样的东西。

[![wat](img/3336650bf4ddb67b0edd881d5a989a07.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--e-m9FVQY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/XHjWQoj.jpg)

简而言之，即使在第三方包中我也找不到任何接近于`strftime`的东西，于是我决定不用魔法用`cgo`来实现它🎉，它支持`strftime`手册页中的所有标准指令。

[https://github.com/cooldrip/cstrftime](https://github.com/cooldrip/cstrftime)