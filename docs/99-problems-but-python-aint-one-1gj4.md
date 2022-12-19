# 99 个问题，但 Python 不是一个！

> 原文：<https://dev.to/grokcode/99-problems-but-python-aint-one-1gj4>

让我们从头开始，定义代码最终如何执行的不同方法。一般来说，当使用编程语言时，有两种可能的方法:编译或解释。哦，还有第三种方法，它是一种混合方法，介于前面提到的两种方法之间。让我们来看看:

*   **编译:**当编译过程完成后，所做的是从整个代码中得到的可执行代码，所以从所有源代码中得到的是一个可以原样使用的，并且具有机器所需要的相同特性的可执行代码。(比如 Java，通过编译它可以在 JVM 上运行)负责这项工作的人是编译器(废话！他当然是！)在下图中，我们可以更深入地看到整个过程...

[![](../Images/bbdd7cf49062a90cf24018287a453605.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cpLmNLMl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sc72gsefcq5ikadyim4z.png)

正如你所看到的，它把所有的东西都放在那里，然后生成可执行文件进行处理。

*   **解释:**另一方面，通过解释，我们可以说是从每个语句中获得了每个指令的“翻译”，所以这是一个逐句完成的工作，也就是说，一个指令接一个指令地被解释器解释(又来了，当然那是他的名字！)可以用作解释示例的语言是 shell 脚本，例如 bash，或者 Javascript、PHP 和 oh！也是本文中最重要的家伙:Python。

这个过程可以在这里看到更好的解释:

[![](../Images/20474722f9df9ae859a9f01fa60d3927.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--00f8Y2jT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r8w9rylky72s9t9pnrin.png)

如您所见，一条条语句(简称 stm)被处理并立即执行。

好的，但是应该有第三个，对吗？总是有介于两者之间的东西...是的，还有第三种方法，它结合了两种方法，用于中间语言，我们可以在下图中看到它:

[![](../Images/3d5cda97ad851eb5e39f376fd62d9878.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NboaHil2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gs7xlod8gjg2lrywpaee.png)

在最后一种方法中，编译是通过一种中间语言完成的，然后使用执行器，执行器只不过是解释过程中使用的解释器，我们可以说这是一种中间方法(有一些来自编译的特性，一些来自解释)

好的，但是回到为什么 Python 如此酷的想法？我的意思是，它被广泛应用于生物信息学、数据科学等领域，原因有很多，比如:

*   可读性:它相当有序，这有助于人们更容易阅读代码(甚至为了让你的代码工作，你需要在使用它之前缩进所有的内容！)

*   代码重用:Python 中有许多模块可用于不同的目的，这就是一些人称之为“包含电池”的特性

*   高性能:它非常接近 C 语言，但是使用其他支持高效计算的库，比如 NumPy，可以大大提高性能。

*   **简单**:同样，这一条也与列表中的第一条相关，它的可读性和简约性使它更容易专注于你的问题，而不是思考其他低级语言问题。(例如超出内存或垃圾收集...在 Python 中谁会想到这个？)

*   **简单易学**:Python 的另一点是，学习曲线相当低，你会学得很快！事实上，我认为这实际上是为生物信息学领域考虑的，我有生物工程领域的同事，他们不习惯一直编码，但这种语言使入门更容易！

*   免费开源:还有一个是**它是免费的！所以你不必担心许可证之类的事情，即使有这么多不同的 ide，比如 Spyder 和包管理器，比如 Anaconda。(Spyder 的另一个比**不自由的** ide PyCharm 更酷的特性是，对于 PyCharm，你无法在 IDE 中获得最酷的帮助，而使用 Spyder，你可以在 IDE 中搜索代码的文档，这有多酷？**

*   **解读**:这个更像是之前我们谈到的性能，但也差不多了..因为它是解释的，所以执行速度非常快，但是当然这也取决于你处理的数据量！

太好了，现在你有了所有这些想法，我希望这能让你对这门语言有所了解，如果你正在考虑学习这门语言，我完全推荐你！

如果你有女孩问题，我为你感到难过，儿子，我有 99 个问题，但 Python 不是其中之一！