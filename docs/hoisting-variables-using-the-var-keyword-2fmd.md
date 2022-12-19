# 使用“var”关键字提升变量

> 原文：<https://dev.to/zmiles17/hoisting-variables-using-the-var-keyword-2fmd>

如果你读了我最近的博文，那么你可能会奇怪我为什么忘记讨论“var”。这是因为“var”被认为是较老的语法，但这并不意味着它已经过时。原来在 JavaScript 引入 ES6 语法之前，“var”是声明变量的唯一方式。大多数代码库可能仍然实现“var ”,我相信理解它做什么和如何工作是很重要的。

“var”的工作方式与“let”类似，只是“let”是块范围的，这意味着它只存在于创建它的代码块中。每当我们使用“var”来声明一个变量时，这个变量就被提升了。你可能会问被吊起来是什么意思？简单来说，提升意味着变量声明发生在执行之前。如果我的解释不清楚，这里有一个例子。

[![](../Images/3b510c01e85f326893bc65c0075c0e9a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9nlX47lC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/392jxfp19gjoenv70ipn.png)

所以我做了一个名为“exampleVar”的函数，它只有一个 for 循环，里面什么也没有，只有一个条件 if/else 语句，它将根据是否定义了“I”来记录控制台。如果你注意到了，我使用了 var 来声明“I ”,这将提升该变量，使其在包含函数内部可用。换句话说，变量“I”在“exampleVar”的顶部被初始化，但是它的值直到 for 循环才被赋值。现在让我们看看如果我用“let”代替“var”会发生什么。

[![](../Images/02f5f780069a4303a71d5b3fedcb5207.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--odPU2a2T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f4dta1tevdsca3kmkpkb.png)

如您所见，除了我如何声明“I”之外，这些函数实际上是相同的。这就是关于在 JavaScript 中使用“var”和提升所需要知道的全部内容。一般来说，避免使用“var”是一种很好的做法，因为与使用“const”或“let”相比，您更容易遇到问题和错误。