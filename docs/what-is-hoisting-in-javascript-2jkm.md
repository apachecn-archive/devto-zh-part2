# 什么是吊装😰😰在 JavaScript 中

> 原文：<https://dev.to/sait/what-is-hoisting-in-javascript-2jkm>

**吊装不是将函数和变量移动到顶部的事情。
实际上，从技术上讲，他们并没有在任何地方移动。**

事情发生在函数声明的时候，完整的函数
在内存中。

对于变量声明，JavaScript 引擎在创建时用值“undefined”初始化。

例如
[![hoisting](../Images/5596a6e153e0e82517c39f46fe0855a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EJzwehYZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/twbpr35szbfthd2si62h.gif)

你注意到上图中的一件事了吗？JavaScript 已经用值' undefined '初始化了。

每当 JavaScript 引擎运行第 6 行时，就会将未定义更新为“提升”。

对于函数，完整的函数被添加到内存空间中。

这就是为什么我们可以在文件的任何地方调用函数，但不能调用变量。对于变量，我们只在它被声明后才使用。

## 有没有检查过 let 和 var 关键字的提升？

如果您试图在声明 javascript 引擎遇到错误之前访问变量，请使用“let”关键字。

[![hoisting](../Images/7a847c28e256d062c1ab46d42c0a371e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UkSuyTRW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yf0z2gqviit8ouds4gz0.png)

对于“var ”,它没有显示任何错误。
[![hoisting](../Images/05677668b16c2264a5a40937a78bdeb8.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--Qlz6Hu4o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4wjm6pvndlnh1o2q3399.png)

希望你们喜欢这些。

# 查看[我的 JavaScript 书](https://dev.to/saigowthamr/learn-javascript-with-examples---book-35k7)