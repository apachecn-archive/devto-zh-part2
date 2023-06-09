# Swift pro 日积月累:带有一般 where 子句的扩展

> 原文：<https://dev.to/eleazar0425/swift-pro-tip-of-the-day-extension-with-generic-where-clause-1e2e>

受几天前我的上一篇文章的成功的启发，我正在用一种更好的方法写一篇续篇，在这篇文章中，我可以介绍一些和计算属性一样有趣的 Swift 特性: ***扩展和泛型*** ，此外，你会注意到这两者是如何融合成一个宏伟的组合的。首先，让我们快速定义这两个特性是什么:

## 扩展:

> 扩展向现有的类、结构、枚举或协议类型添加新功能。这包括扩展您无权访问源代码的类型的能力(称为追溯建模)。

Swift 中的扩展可以:

*   添加计算实例属性和计算类型属性
*   定义实例方法和类型方法
*   提供新的初始化器
*   定义下标
*   定义和使用新的嵌套类型
*   使现有类型符合协议

[来源:Swift 编程语言](https://docs.swift.org/swift-book/LanguageGuide/Extensions.html)

最常见的示例可能是使用扩展使 ViewController 适应协议: