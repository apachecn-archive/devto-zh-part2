# 引入缺失的部分:使优步肋骨可用的代码生成工具。

> 原文：<https://dev.to/ivanmisuno/bringing-in-the-missing-part-a-code-generation-tool-that-makes-ubers-ribs-usable-4n80>

作为优步在阿姆斯特丹的工程团队的一员，我有幸从一开始就使用优步的肋骨框架和相应的编码模式。有一件事使得采用基于协议的方法并在基于 RIBs 的应用程序中轻松编写单元测试成为可能:一个代码生成工具，它从程序的源代码中提取协议定义并为协议生成模拟类。优步在 2017 年开源了 [RIBs 框架](https://github.com/uber/RIBs)，但没有附带代码生成工具。在我参与的几个项目中，我看到了采用 RIBs 架构模式的巨大好处，但是如果没有一个强大的模仿工具，这种采用将是有问题的。在本文中，我将介绍代码生成模板，它足够强大，可以覆盖复杂应用程序中的典型用例，该应用程序完全采用了 RIBs 架构，并为 RxSwift 类型提供了一些有用的快捷方式。

# 介绍[swift-sourcery-templates(Beta)](https://github.com/ivanmisuno/swift-sourcery-templates)

[swift-sourcery-templates](https://github.com/ivanmisuno/swift-sourcery-templates) 库包含两个代码生成模板(与 [Sourcery](https://github.com/krzysztofzablocki/Sourcery) 引擎一起使用):

1.  `Mocks.swifttemplate`——生成高级协议模拟类，可用作[测试替身](https://martinfowler.com/bliki/TestDouble.html)，代替单元测试的对象依赖；
2.  `TypeErase.swifttemplate` —生成高级[类型的擦除](https://www.bignerdranch.com/blog/breaking-down-type-erasures-in-swift/)。

这两个模板都支持带有关联类型和泛型函数的协议(带有泛型类型的约束)，
为原始类型提供合理的默认值，并支持高级类型和用例，这些高级类型和用例通常出现在使用 [RxSwift](https://github.com/ReactiveX/RxSwift) 的项目
中，尤其是用户的 [RIBs](https://github.com/uber/RIBs) 框架中。

# 样本项目

除了模板文档，我还计划发布教程文章。同时，我正在开发一个伴随项目的[，请随时查看并尝试代码生成模板。](https://github.com/ivanmisuno/Tutorial_RIBs_CodeGeneration)

感谢任何反馈！

谢了。