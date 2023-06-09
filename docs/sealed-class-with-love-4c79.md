# 用爱封住(阶级)

> 原文：<https://dev.to/hector6872/sealed-class-with-love-4c79>

## 我们新的最佳旅行伴侣❤️

在 Kotlin 为我们带来的众多特性中，有**密封类**可以改善和方便我们的编程生活。这些，结合其他特性，比如[智能](https://kotlinlang.org/docs/reference/typecasts.html#smart-casts) [造型](https://try.kotlinlang.org/#/Examples/Basic%20syntax%20walk-through/is-checks%20and%20smart%20casts/is-checks%20and%20smart%20casts.kt)和[当](https://kotlinlang.org/docs/reference/control-flow.html#when-expression) [表达式](https://try.kotlinlang.org/#/Examples/Basic%20syntax%20walk-through/Use%20when/Use%20when.kt)时，将导致**一种新的和更安全的方式来建模我们的应用**。

### 什么是密封类？

[![](img/8b878ba49fc7a6438be6fb548027ec61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kKnBZ2Ny--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/hrules6872/Writings/master/5-sealed-class/art/1.png)“密封类用于表示受限类的等级结构……-[心灵爆炸/心灵爆炸](https://www.youtube.com/watch?v=9CS7j5I6aOc)

**密封类被称为“类固醇上的枚举类型”** : [它们都是](https://en.wikipedia.org/wiki/Algebraic_data_type)受限层次结构，它们都代表一个集合，它们都是类型，它们都可以包含关联数据，但是…第一个:

*   不能被直接实例化，因为它们是`abstract`(因此，[它们可以有抽象和非抽象的方法和字段](https://gist.github.com/hrules6872/b69c89d5b070eb0eb2ecc809f921bd54))
*   可以有子类，这些子类可以有多个实例，这些实例可以包含它们自己的状态
*   **这种关联数据的类型可能不同**(见下面的例子)