# Javascript 是编译语言吗？

> 原文：<https://dev.to/genta/is-javascript-a-compiled-language-20mf>

# 真的是这样...

为什么人们仍然把 JS 看作是一种*动态的*或*解释的*语言？

关于 JS 的*编译*有很多误解，而且现在，随着网络上大量信息的出现，大多数人仍然对此争论不休，仍然不知道 JS 在*运行期*阶段之前到底是如何工作的。

### Javascript 是一种编译语言...

尽管 JS 的*编译*以不同的方式工作，但如果与其他编译语言相比，它仍然是**遵循一些规则**来反映*编译*的过程

第一...我们不得不引用维基百科的话:

> 编译器是一种计算机软件，它将以一种编程语言(源语言)编写的计算机代码转换成另一种编程语言(目标语言)。

我们都知道，计算机不会说 Java、JS 或 Python，不管我们用的是哪种语言，我们总是在用 T2 把我们的代码翻译成机器能理解的东西...但这不是目前最重要的事情。

重要的是...这种翻译叫做*代码生成*，它的输入是**抽象语法树** (AST)，它是关于一些*嵌套元素*的，这些元素代表了程序的结构。这个树的构造发生在编译的*解析*阶段。

当然，我们必须提供一些东西来创建这个 *AST* ...我们做到了...我们提供了一个*令牌数组*，来自之前的编译 *lexing* 阶段。

`let dog = labrador;`

将*令牌化*成这样

`let,dog,=,labrador,;`

我们代码的这个*分裂*版本，对语言来说意味着什么，并创建信息流来生成 *AST* 。
我们现在有一个`variableDeclaration`和一个`assignment`等等...在我们的*树*里。

我已经**不是**非常具体，因为这篇文章是关于这样一个事实:在 JS 中**所有这一切，正在发生**。

是的。

Javascript 遵循所有这些*编译*阶段，顺序如下:

1.  乐星
2.  从语法上分析
3.  代码生成

JS *编译*不会让它在不同的平台或类似的东西上工作...但是**它正在发生**

这不是你应该知道的通用的东西...这可以**完全改变**你对许多 JS 行为的看法。

简单的例子是**词法范围**和**提升**。

JS 中的变量声明发生在 *lexing* 阶段，而赋值发生在运行时的*，这就是为什么**提升**发生在一个更加技术化和正确的角度。在 JS 中，作用域是在它的*词法*阶段定义的，这就是为什么 JS 有了**词法作用域**的定义。*

那么**闭包**呢？更复杂...但是仍然会因为*范围引用*和*词法范围*而发生一些事情。

所以，各位，JS 每次都会被快速编译...引擎中包含了许多优化，使其在性能上没有任何附带问题成为可能，如果你没有意识到这一点，你可以打破这一点。

<center>**Have fun, looking for more info!**</center>