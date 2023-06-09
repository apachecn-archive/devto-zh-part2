# 我学习 Swift 的第一周。

> 原文：<https://dev.to/s_awdesh/my-first-week-of-learning-swift-hne>

### 1。)声明常量和变量。

`Variables`

[![var](img/38256663bfd4e2c3660beb656c209fba.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZEb4PRpM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j30gzph8h08vnshfg6ib.png)

`Constants`:一旦声明，它们的值就不能改变。当试图更新该值时，Swift 将抛出编译时错误。

[![let](img/421aab91c85903b187b56322c4912414.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W2Ro6AvN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0czj40r38b68uvy85pwa.png)

### 2。)声明一个函数

[![func](img/a6f03592a10a90038f20b6da36a13bfb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F5JrdVDn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a7xk2qy0dg795fmbwmkp.png)

Java 的常规函数的语法如下所示

[![method](img/149f8f24ce6fb329f220e83f539059cc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xbondZLU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z1lkguxfj9wxwvxrjy8n.png)

### 3。)类函数

和 python 一样，swift 也有类函数。类函数被绑定到一个类，而不是一个类的实例。老实说，当我第一次在源代码中遇到语法中的`function`后跟`class`时，我有点迷惑。

[![class functions](img/fb3543d9c8310de5f29c7fd5953ce54d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--K1K17Tz9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1oafqmyxd1wy5ik98wpp.png)

Python 的类函数语法

[![python class](img/fdc2e37beda1e846a9107f9d4cc37a06.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XFgRlffr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bitdtez1x7g1k6ckwgrd.png)

### 4。)使用下划线`_`。

在我使用 Swift 的短暂经历中，我见过在函数调用中使用`_`。`_`用于定义函数内部未命名的参数。Swift 为每个参数使用一个名称，所以当我们调用一个函数时，我们需要提供参数的名称。

使用`_`可以完全绕过名称的传递。让我们看一个例子:

[![underscore](img/3bba14e1c078cef0d9ddff4b1dd6833b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0kfdmyIs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e8huvrtk19shv64kr2ut.png)

一般来说，当`_`在前面时我们可以忽略值。`_`的另一个用例如下所示。

[![return](img/1ae0e27dd9e0f3325d0b7a9d4c04b60b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SfYou0zK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zv8817iurxim6bchrzc1.png)

### 5。)类型别名

顾名思义，类型别名允许为现有类型提供别名。
[![alias](img/2c89f05e7aa477870cfade2b4bcab776.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--tdjR9aYU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ev86b2bi4o2fiocq7lbg.png)

这当然不是使用类型别名的最佳用例。更好的用例是在函数中别名闭包。swift 中的函数和闭包有一个参数类型和一个返回类型，这两个类型可以有别名。

[![alias](img/9752d89abc15d0e70f38cf38e35f1bc9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mD4r_Yse--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8x93lvorzyajh6pjmhg6.png)

### 6。)闭包

根据苹果文件

> 闭包是自包含的功能块，可以在代码中传递和使用。

[![closures](img/d5291d9c5111eb4e44964d6a99e2ab7d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GJTw9yXz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cnn5n4jrh34up45e1kcx.png)

### [7](#7-raw-endraw-and-raw-endraw-)。)`?`和`!`。

`?`和`!`都用于 swift 中的选项。使用可选变量可以使变量有值或为零。

`?`用于变量类型后，表明该变量是可选的。

[![Optional](img/92a6b64825a4b51205a75e031594c7a4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WtbDSd0C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4p134d6dmh4go5oh31v7.png)

`!`用来解开可选的值所以要使用它。

[![unwrap](img/589f25609875d841935a2a74dcb026a4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LtQfmYIP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/io9a2pikn4vs0xorvqmu.png)

我们可以在展开可选之前检查非零值，以消除运行时错误。

[![force-unwrap](img/6968a6856640bae210c368ae11bb483c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Xbc7Mdn2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0lortmj1krhaeqz5riia.png)

正如 Nuno Vieira 在评论部分指出的，强制解包不是一个好的实践，而是我们可以使用的(如果..let)即所谓的**可选绑定**方法。

[![optional-binding](img/a5d25827df8eddec702bf45f126b0ed7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UQnMH5gt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xg7v2d9w42otdxjwkjaq.png)

### 8。)字典和数组

[![array](img/7a2a1314001619ad177e793b12f08f94.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X_CTbmJe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cd6wxoa03b3r5ty6qza3.png)

这就是这篇文章的内容。敬请关注此类文章。

### 参考文献

*   苹果官方 swift 教程-:[https://docs . swift . org/swift-book/guided tour/guided tour . html](https://docs.swift.org/swift-book/GuidedTour/GuidedTour.html)
*   关于可选方案的精彩阅读-:[https://stack overflow . com/questions/24003642/what-a-optional-value-in-swift](https://stackoverflow.com/questions/24003642/what-is-an-optional-value-in-swift)

* * *

如果你想让我写一个特定的话题，请随意在评论区发表。

你可以在下面给我买杯咖啡来支持我的工作。💚💚💚💚💚💚！！
[![Buy me a ko-fi](img/2c34722d2ff9357df105fc9e9ec792b6.png)T3】](https://ko-fi.com/F1F0J8U1)