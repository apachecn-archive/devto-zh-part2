# 除以零

> 原文：<https://dev.to/drbearhands/extra-fundamentals-dividing-by-zero-1li5>

这篇文章很大程度上依赖于[功能基础](https://dev.to/drbearhands/a-series-on-functional-fundamentals-48mb)系列中的一些知识，但并不是它的重要部分。阅读自担风险！

前几天我在 Elm slack 有一个关于整数除法 <sup>1</sup> 的有趣讨论。除以 0 应该返回什么？这对分裂的类型有什么影响？

我认为除以 0 是不可能的，将 0 作为除法函数的第二个输入应该被认为是错误的。

事实证明，我错了。数学不在乎除以 0。它甚至没有真正定义组织。如果我们将除法定义为乘法的逆运算，我们只剩下一个部分定义，因为 0 没有乘法逆运算。

这意味着除以 0 需要某种特殊的规则，我们可以随意定义。既然我们自己下了定义，没人能证明我们是错的！除以 0 可以得到 0，42，⊥，房子，克苏鲁或者黑洞。

对于我们的类型系统，这意味着返回类型可以是`(Int ∨ a)`，用任何东西替换`a`。

有趣的事情:如果我们将除以 0 定义为返回 0，我们会得到两个好处:一个返回类型仅仅是`Int`，以及一个额外的代数公理。所以说`x/0 = 0`背后是有一定动机的。

### 军规

虽然数字的公理并不关心除以 0 得到什么，但类型系统却关心。还记得[一个类型系统本质上是一个证明](https://dev.to/drbearhands/functional-fundamentals-types-as-propositions-programs-as-proofs-56gh)吗？

**如果我们只使用为数字**提供的公理，我们不能否定被 0 除的结果，但我们也不能*证明*它。象征性地:`⊬ Int/0 = 0`。因此除法不构成`Int → Int → Int`的证明，而只是`Int → IntButNotZero → Int`的证明。

然而，我们可以决定`Int`不仅仅是一个数字，通过给它一个额外的公理，比如`Int/0 = 0`。
这将从根本上改变`Int`的含义，不再与[整数](https://en.wikipedia.org/wiki/Integer)同构。

因此，讨论实际上是关于`Int`应该是什么。虽然我学到了很多，但我的观点一直未变:

*   理论上，我认为我们应该尽可能使用小的构建块。这意味着不给`Int`任何额外的公理。让人们为之做出自己的类型。 <sup>2</sup>
*   在实践中，我期望传递`0`作为除数几乎从来不是有意的(对我来说不是)，所以类型系统应该捕捉它。

虽然除法的返回类型使得阅读某些公式更容易，但我认为这是对编程中公式打印这一更广泛问题的错误解决方案。我们有乳胶是有原因的。我在[早先的帖子](https://dev.to/drbearhands/should-programming-languages-be-made-for-ides-rather-than-humans-3dnf)中提到过这个问题。

不可否认，当前的工具是什么，时间是有限的，`Int`返回类型导致更好的公式的论点是成立的，每种语言必须根据预期用途做出自己的决定。

## 备注:

<sup>1</sup> 浮点有点不同:它们对无限精度的模仿使得 0 浮点值看起来更像一个极限，所以`Float/0=Infinity`很有意义。
<sup>2</sup> 我应该注意到*在理论上*我也不认为`Int`一开始就与函数式编程有任何关系，因为它与 [ALU](https://en.wikipedia.org/wiki/Arithmetic_logic_unit) 的内部工作方式紧密耦合，使其成为一种相当必要的类型。

## 参考文献:

*   [关于除以 0 的数学的原始文章](https://www.hillelwayne.com/post/divide-by-zero/)