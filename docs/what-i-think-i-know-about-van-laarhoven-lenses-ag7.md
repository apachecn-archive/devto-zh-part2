# 我对范·拉霍文镜头的了解

> 原文：<https://dev.to/jreina/what-i-think-i-know-about-van-laarhoven-lenses-ag7>

*本文最初发表于[我的 Github 页面网站](http://johnnyreina.com/programming/functional/2018/08/19/van-laarhoven-lenses-v2.html)2018 年 8 月 19 日*

## Mo '功能，Mo '问题

我们总是被告知要避免函数式编程中的变异。这主要是通过使用常量变量以及引用类型(对象、数组等)来实现的。)，使用避免突变的方法和函数。虽然有很多函数非常适合这种数组思想，比如 map、filter 和 reduce，但是这些函数对于对象来说要麻烦得多，并且没有得到广泛使用。我们有 object spread 和静态方法，比如 Object.assign，这非常有用，但也可能导致混乱的语法，除了最简单的对象结构。我在 JavaScript 中见过的大多数例子看起来都是这样的: