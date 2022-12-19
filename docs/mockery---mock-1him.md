# Mockery:部分对偶(mock '和)

> 原文：<https://dev.to/phpprofi/mockery---mock-1him>

在处理旧代码时，我经常遇到这样一个类，它扩展了一个大的基本抽象类，而这个类的方法调用了那个做非常多事情的大的基本抽象类的方法。我自己过去也写过这样的类和方法。活到老，学到老。

这个代码最大的问题之一是它相当难测试。基类中的方法可以返回其他对象，附带影响代码的其他部分(或调用服务)，进行 http 查询...

* * *

http://phpprofi.ru/blogs/post/99 的читать

* * *

https://robertbasic.com/blog/mockery-partial-mocks/