# (aﾠ == 1 && a == 2 && ﾠa == 3)能等于真吗？

> 原文：<https://dev.to/superkarolis/can-a--1--a--2--a--3-equal-true-3bo2>

这里有一个 JavaScript 琐事问题。表达式`(aﾠ == 1 && a == 2 && ﾠa == 3)`能计算出`true`吗？今天我发现可以。运行下面的代码，自己看看。

[https://repl.it/repls/ProperMediocreRepo?lite=true](https://repl.it/repls/ProperMediocreRepo?lite=true)

现在让我们看看下面看似相同的代码。试着运行它。这一次表达式的计算结果不是`true`。那么是什么改变了呢？

[https://repl.it/repls/SufficientKindSolidstatedrive?lite=true](https://repl.it/repls/SufficientKindSolidstatedrive?lite=true)

一旦你弄清楚发生了什么，这种差别实际上非常简单，也就不那么神秘了。显然，有一个 Unicode 字符叫做[半角韩文填充符](https://www.compart.com/en/unicode/U+FFA0)，它是一个韩文符号，实际上是不可见的。所以第一段代码评估为`true`的原因是因为三个变量`a`实际上是三个不同的变量。其中一个添加了半角朝鲜文填充符，另一个保持不变，第三个添加了隐藏字符。

下一次有人问你`(aﾠ == 1 && a == 2 && ﾠa == 3)`是否能等于`true`时，你可以回答他们，是的，确实可以(有一个小小的警告)😀