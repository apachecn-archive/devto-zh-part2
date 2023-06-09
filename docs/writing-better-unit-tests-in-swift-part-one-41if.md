# 用 Swift 编写更好的单元测试:第一部分

> 原文：<https://dev.to/clue/writing-better-unit-tests-in-swift-part-one-41if>

*使用工厂方法生成测试数据*

[![](img/cbe0334af0023f7abd2da6b6e06a2715.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yjFnpFHj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AOBYnnEGuTxwoFPsGjgvlUg%402x.png)

### 问题

看，我们都是朋友，所以我觉得我可以完全诚实:在我的职业生涯中，我写过一些非常糟糕的单元测试。20 行怪物，带有多个模仿和断言以及异步期望。这种事情你会在标题为“如何解决之前在这里工作的白痴留下的烂摊子”的书中看到。在我写完单元测试之后，我还必须维护那些代码，而且说得越少越好。因此，可以说，我现在把编写“好的”单元测试作为优先事项。

在我们开始之前，我将定义我认为的“好的”单元测试。如果我们同意一个单元测试(或者，真的，任何测试)是由一些*设置*，我们正在测试的*动作*，以及关于那个动作的效果的*断言*组成的，那么我会说，简单地说，一个“好的”单元测试是使这三个组成部分都清楚的测试。

(旁白:这可能与你对“好的”单元测试的定义不同，这是我们都必须接受的。)

在接下来的几篇博客文章中，我将向你展示我们在 Clue 所做的一些事情，以确保我们总是试图写出“好的”单元测试。在这篇文章中，我们将看看一个简单的技巧，我们可以用它来最小化单元测试中的*设置*部分，同时保持清晰。

假设我们想在一个简单的 Swift 结构上对等式方法进行单元测试。

```
struct User: Equatable {
 let name: String
 let email: String
 let needsVerification: Bool

static func ==(lhs: User, rhs: User) -\> Bool {
 return lhs.name == rhs.name
 && lhs.email == rhs.email
 && lhs.needsVerification == rhs.needsVerification
 }
} 
```

有(可数)无限多种不同的参数组合可以传递给这个方法进行测试。显然，我们无法对所有的案例进行测试，所以我们必须挑选一些案例来代表结果的一般趋势。对这个方法进行单元测试的一个有效方法(实际上，我通常会使用测试驱动开发来编写这样一个方法)是从两个用户的所有属性都相同的情况开始，然后测试当每个属性不同时会发生什么。这给了我们一套这样的测试:

```
func test\_equals\_allPropertiesMatch\_isTrue() {
 let sut = User(name: "", email: "", needsVerification: false)
 let other = User(name: "", email: "", needsVerification: false)
 XCTAssertEqual(sut, other)
 XCTAssertEqual(other, sut)
}

func test\_equals\_nameDiffers\_isFalse() {
 let sut = User(name: "Jo", email: "", needsVerification: false)
 let other = User(name: "", email: "", needsVerification: false)
 XCTAssertNotEqual(sut, other)
 XCTAssertNotEqual(other, sut)
}

func test\_equals\_emailDiffers\_isFalse() {
 let sut = User(name: "", email: "A", needsVerification: false)
 let other = User(name: "", email: "", needsVerifiation: false)
 XCTAssertNotEqual(sut, other)
 XCTAssertNotEqual(other, sut)
}

func test\_equals\_needsVerificationDiffers\_isFalse() {
 let sut = User(name: "", email: "", needsVerification: true)
 let other = User(name: "", email: "", needsVerification: false)
 XCTAssertNotEqual(sut, other)
 XCTAssertNotEqual(other, sut)
} 
```

(sut 代表“测试中的主题”,由于某种原因，它是我唯一能接受的缩写变量名。)

无论从哪方面来看，这些单元测试都不差——事实上，它们基本上是好的——但是它们也不是“好的”为什么不呢？

让我们看看我们的两个用户的设置。每次创建它们时，我们都必须传递 init 方法期望的所有属性。这是真的，即使在测试的环境中，我们实际上并不关心它们是什么。例如，在第一个测试中，我们所关心的是属性匹配——它们的值可以是按身高升序排列的辣妹的名字，或者是我最喜欢的柏林咖啡店(Bonanza & Five Elephant btw ),只要它们是相同的，对测试没有影响。类似地，在其他三个测试中，我们只关心一个特定的属性不同。

在这些测试中包含我们不关心的信息会产生噪音，使测试更难理解。对于这些小例子来说，这可能没问题，但是当你试图找出为什么一个更复杂的单元测试在你删除一个发布之前一个小时就失败了，你会非常感谢你尽可能地删除了不必要的干扰。

如果我们的测试只包含相关的信息，那岂不是太棒了，太棒了，太棒了，太棒了，还有其他最高级的东西？当然，如果不可能的话，这一切都是徒劳的。

### 解

所以我们要做的是:我们要扩展测试目标中的用户类型，以便添加一个方法，让我们用我们关心的数据配置用户，同时对其他属性使用合理的默认值。我倾向于称这个方法为 create，因为我认为它读起来很好。不管你怎么称呼它，这个方法应该是这样的:

```
extension User {
 static func create(
 name: String = "",
 email: String = "",
 needsVerification: Bool = false
 ) -\> User {
 return User(
 name: name,
 email: email,
 needsVerification: needsVerification
 )
 }
} 
```

(别担心，我也不喜欢你分隔代码的方式。)

这些 create 方法非常简单——它们接受与类型的 init 方法相同的参数，但给每个参数一个默认值。根据经验，我倾向于对字符串参数使用""，对数字参数使用 0，对布尔值使用 false，等等。无论您决定使用什么，真正重要的事情是尝试在您编写的不同 create 实现之间保持一致。

(冷静地说:一旦你的一些类型有了 create 方法，你也可以开始使用不带任何参数的 create 调用结果作为默认值。因此，如果您有 Account(user: user ),那么在 Account.create 中，您可以将 User 参数的默认值设置为 User.create()。)

现在，结合 Swift 的类型推断能力，我们可以像这样重写上面的测试:

```
var sut, other: User!

func test\_equals\_allPropertiesMatch\_isTrue() {
 (sut, other) = (.create(), .create())
 XCTAssertEqual(sut, other)
 XCTAssertEqual(other, sut)
}

func test\_equals\_nameDiffers\_isFalse() {
 (sut, other) = (.create(name: "Jo"), .create())
 XCTAssertNotEqual(sut, other)
 XCTAssertNotEqual(other, sut)
}

func test\_equals\_emailDiffers\_isFalse() {
 (sut, other) = (.create(email: "A"), .create())
 XCTAssertNotEqual(sut, other)
 XCTAssertNotEqual(other, sut)
}

func test\_equals\_needsVerificationDiffers\_isFalse() {
 (sut, other) = (.create(needsVerification: true), .create())
 XCTAssertNotEqual(sut, other)
 XCTAssertNotEqual(other, sut)
} 
```

很不错，对吧？现在每个测试只包含与特定测试用例实际相关的信息。这是好事。

总之，这是对用 Swift 编写好的工厂方法的简单介绍。很明显，这只是改进单元测试的一个小方法，但是你必须从某个地方开始，对吗？现在开始，编写单元测试。✅

(感谢[乔什·贺德](http://joshuaheald.com)。当这种编写工厂方法的模式最初出现时，我们正在配对。)

* * *