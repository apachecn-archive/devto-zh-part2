# 是时候抛弃模式名后缀了？

> 原文：<https://dev.to/bertilmuth/time-to-abandon-pattern-name-suffixes-56f9>

Kevlin Henney 的一次演讲启发了我，在我正在做的一个项目中，我删除了类中的`Exception`后缀。

在被捕获的检查异常或未处理的运行时异常的上下文中，很明显这是您正在处理的异常。

此外，在那个项目中，所有的异常都在一个`exception`包中。很容易猜到发生了什么。

这让我很好奇。将模式名附加到类名中是一种非常常见的做法。`FooFactory`。`BarController`。

找一些更简洁的，会说话的名字不是更有意义吗？

[![Image](../Images/f48d3597616f0e0cf4e23fbed4a77751.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hRJdPfY2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.redditmedia.com/tHbmWDAlprYm0RDgHX1EGf7Yhx8KNy20n0HEv8vjD_w.png%3Fs%3De902918450214ca15f33bcf0f5e609e0)