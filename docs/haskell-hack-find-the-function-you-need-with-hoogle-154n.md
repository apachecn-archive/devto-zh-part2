# haskell hack:用 google 找到你需要的功能。

> 原文：<https://dev.to/antonrich/haskell-hack-find-the-function-you-need-with-hoogle-154n>

今天我在解决一个关于 Codewars 的问题。不知何故，我忘记了一个非常简单的函数，它可以检查一个元素是否是一个列表的成员。

[![Alt searching for a Haskell function](img/2d1358e9ddc549979fbb7722913f2d62.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Yb-YQVoc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vj2vlzmkrr9kxfi6kvjq.png)

这当然让我想到了 stackoverflow。你可以在那里找到一个有趣的黑客，是由[在 nNn](https://stackoverflow.com/users/243581/atnnn) 发布的:

首先找到你需要的函数类型。

“检查是否”意味着返回真或假，一个布尔值。

所以这个函数接受一个 Int，一个 Int 的列表(又名[Int])并返回 Bool:

Int -> [Int] -> Bool

现在问[谷歌](https://www.haskell.org/hoogle/?hoogle=Int+-%3E+%5BInt%5D+-%3E+Bool)。

元素:eq a =-a->

Hoogle 是一个非常有用的工具。可以和 ghci 整合。

* * *

这种在 Hoogle 上使用类型签名的技巧真是太棒了。这使得 Haskell 更加直观。

你使用什么样的 Haskell hacks？

附言:如果我记得函数“elem ”,我可能很长时间都不会发现这个问题。