# 周五爆炸#41

> 原文：<https://dev.to/horia141/friday-blast-41-519k>

[概率单位回归(2018)](https://www.johndcook.com/blog/2018/05/05/probit-regression/) -这是更著名的[逻辑回归](https://en.wikipedia.org/wiki/Logistic_regression)的替代方案。它们都是广义线性模型，区别在于*链接函数*。对于前者，它是所谓的*概率单位*函数，而对于后者，它是*逻辑*函数。概率单位函数是标准正态分布的 CDF 的倒数。它看起来非常像众所周知的逻辑连接功能/神经激活功能，并且在实践中这两种方法基本上是相同的。但是有时候用一个比用另一个更容易分析。

[决策树学习介绍(2018)](https://heartbeat.fritz.ai/introduction-to-decision-tree-learning-cd604f85e236) -涵盖模型的样子以及学习算法的草图(本例中为 ID3)。

[甲骨文计划放弃危险的 Java 序列化(2018)](https://www.infoworld.com/article/3275924/java/oracle-plans-to-dump-risky-java-serialization.html)——我最近从 Java 世界听到了很多好东西，似乎这种语言的发展速度比以往任何时候都快。放弃连载是 T2 的一个大动作，看着它结束会很有趣。如果我没有在此期间搬到其他牧场。

[我们不想要的头套(2018)](https://www.fastly.com/blog/headers-we-dont-want)——Fastly 对他们遇到的常见头套做的分析。原来有很多受欢迎的，但在其他方面无用的。作者选择了虚荣心，不赞成的标准，调试和普通的老误用头特别关注。这里有一个 VCL -法斯特丽的请求转换小语言的例子。他很整洁。

Malloc 从不失败(2012) -是的，它会失败，但在 Linux 中，你会在有机会做出反应之前被内核杀死。因此，在大多数情况下，尝试处理这种特殊的失败案例仍然没有意义。有趣的是，即使它成功了，你也不能保证有内存——就在你开始使用它的时候。所以你可以分配比你实际使用的更多的内存——只有当你*使用*它的时候才会崩溃。