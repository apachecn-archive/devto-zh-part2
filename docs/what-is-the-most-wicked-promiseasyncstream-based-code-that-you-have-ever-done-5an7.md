# 你做过的最邪恶的(promise | async)/基于流的代码是什么？

> 原文：<https://dev.to/tunaxor/what-is-the-most-wicked-promiseasyncstream-based-code-that-you-have-ever-done-5an7>

我记得做过一个简单的流实现(我猜，我从来没有要求其他人进行代码审查),它使用 mongodb 游标将数千个注册表处理到另一个数据库中
,基本上我混合了流、eventemmiters(流的继承)、一些生成器和基于 promise 的代码。