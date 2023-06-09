# const 与 let

> 原文：<https://dev.to/zmiles17/const-vs-let-154a>

如果你像我一样是 JavaScript 和编码的新手，那么你可能想知道为什么人们使用 const 或 let。我有科学/数学背景，常数对我来说意味着常数不会变化。在 JavaScript 中，const 意味着被赋值的变量不能被重新赋值，它只存在于声明它的代码块或词法范围中。

[![Attempt of logging a const outside of its lexical scope](img/6bbe6fc3d781342a721582fb82f7a316.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wASgaRBE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e90wzs2gvda9ktgtihak.png)

在上面的例子中，我在创建 sum 的函数之外记录了 sum，它返回了一个错误，因为 sum 只在函数内部定义。

[![Logging a const within its scope](img/8d3fe5b17e6359250cab6b68bd91ab12.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jdlRggXl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u0xc0gb10x2konumqpua.png)

为了验证我的解释，我在变量的词法范围内提供了一个日志。Let 以类似的方式工作，除了 let 可以被重新分配，这使得它对于循环和数学目的很有用。

[![](img/874c25705936277b2435ad6a87d34030.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--z8Ctbzm2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gvhnxdj8qv5r6ouig45a.png)

所以我做了一个基本的加法函数，和前面的例子很像，除了这个函数接受一个数字数组。这就是 let 成为强制性的地方。如果我把变量 sum 变成一个常量，那么代码会返回一个错误，因为我们试图重新赋值。同样的规则也适用于 i = 0 的循环。如果我设置 const i = 0，那么它永远不会等于零以外的任何值。当代码试图增加常量变量时，控制台会返回一个错误，提示“常量变量赋值”。

这就是 const 和 let 的区别。这是我的第二篇博文，所以让我知道你喜欢或不喜欢什么，我会在我的下一篇博文中努力改进。