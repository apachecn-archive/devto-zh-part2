# 位置关联的两个例子

> 原文：<https://dev.to/trikitrok/two-examples-of-connascence-of-position-42gg>

### 举第一个例子。

正如我们在[上一篇关于同生关系](https://dev.to/trikitrok/about-connascence-17ko)的文章中看到的，当多个组件必须相邻或以特定顺序出现时，就会发生**【位置同生关系(CoP)】****。 **CoP** 是静态同生的最强形式，如下图所示。**

[![alt text](img/84e4bc289f48ec259992a7f7ec42f6f1.png "Connascence forms sorted by descending strength (from Kevin Rutherford's XP Surgery)")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r2h6CPqj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8hqgxh8yoxc759o0gs0i.png)

当我们在方法签名中使用*位置参数*时，会出现一个典型的 **CoP** 的例子，因为参数顺序的任何改变都会强制改变使用该方法的所有客户端。