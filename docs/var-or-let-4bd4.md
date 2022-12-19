# Var 还是 Let？

> 原文：<https://dev.to/wmahathre/var-or-let-4bd4>

Let 与 var 非常相似，但两者之间存在一些关键差异。对于那些还在努力理解 var 和 let 的人来说，这篇文章将提供两者之间差异的快速总结。

Var 使用一个函数作用域，它有一个提升。提升允许变量在声明的范围之外的更广的范围内可用，但这增加了代码出错的可能性。如果你有另一个同名的变量，事情会变得混乱，这就是为什么使用更少的变量和更小的范围是一个很好的解决方案。

[![Alt text of image](../Images/db45c486d957ce0fb351ef03c8528b80.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X3dLgqRs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rlfdimtudgfub65yvcay.png)

在这个例子中，您可以看到提升过程，其中顶部的声明和变量在整个范围内被调用。问题是 I 被声明了两次，这使得代码混乱。

Let 有一个所谓的块作用域，它只在花括号内声明。这意味着声明的变量被限制在作用域内，而不像 var 那样，变量是全局定义的，不考虑块的作用域。这有利于限制在块范围之外声明它时可能发生的任何错误。

[![Alt text of image](../Images/6b782e8263b101721dd315f057c1a1ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s2OnTkB6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4y48pc8xde7gs3fh97u1.png)
[![Alt text of image](../Images/d787e975268a2bc01a4e73ba566ad86f.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--SRZIAykW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/np3c0uf4dx608dg5ht2d.png)

正如你在这个例子中看到的，我们尝试控制台记录变量，如果它给出一个错误消息，因为它在块范围之外。

[![Alt text of image](../Images/c034b8ab6deeffd7020c5a6797aa2360.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3MwG5VOH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8dhs9hg598u8vkfo04ot.png)
[![Alt text of image](../Images/ee81abd43328e288ffa5fb6d1b1322e1.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--3wiRt4pF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gty871cjz7la5kyxnij3.png)

现在，当我们做同样的事情，但是用 var 代替 let，我们得到一个 I 值，100。现在，即使这样做是可行的，但如果其他人正在编辑代码，并在后来使用了变量 I，那么就会变得混乱。

请记住，尽管您可以使用 var 在全局范围内访问变量，但最好使用 let 来限制范围，以减少您可能面临的错误数量。