# 编程语言应该是为 ide 而不是人类做的吗？

> 原文：<https://dev.to/drbearhands/should-programming-languages-be-made-for-ides-rather-than-humans-3dnf>

这是我已经思考了一段时间的事情。为什么我们要为人类而不是 IDE 创建编程语言，让后者以人类可读的格式来表示程序？

如果你是做数学的，文本很烂。

`c = sqrt(pow(a, 2) + pow(b, 2))`

只是不像
[![pythagoras](../Images/9f3a1b0b998a2978fb83bcbc9866b5ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C0pa2lGv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wikimedia.org/api/rest_v1/media/math/render/svg/5fd521cee81d583ce94bf6710984cc2a9eb7c3da) 那样清晰可辨

对于非原语，如语言中通过引用传递结构的向量，情况会变得更糟。如果你想避免昂贵的内存分配，你只能使用:

`add(c, pow(temp1, a, 2), pow(temp2, b, 2))`

这些只是最简单的公式。我在做图形引擎开发时写的着色器代码调试起来是一场噩梦，因为它表现得太迟钝了。

然后是变量命名。再举一个数学上的例子:`P(A|B)`有一个精确和容易理解的意思，加上它的简洁和容易识别。不幸的是，大多数编程语言不允许我们给变量命名`P(A|B)`。类似地，camelCase vs snake_case 是一场完全围绕变量名中不能使用空格的辩论。

另一个问题，[前阵子贴的](https://dev.to/gregorys100/which-is-the-best-ide-for-a-smartphone-58cg):我们如何在智能手机上编码？虚拟键盘对代码来说不是很好，基于图形的解决方案可能有用，但是我很确定没有多少人喜欢在他们的桌面上画图形。

制表符和空格是另一个完全基于代码是文本这一假设的争论。同时，这两种方法都是非常过时的校准工具。

在每种情况下，关键问题是格式和语义之间存在紧密耦合。也就是说，我们将代码显示为纯文本，这就是它*是*的原因。

连字和自定义操作符提供了一些帮助，但最终都是附加的解决方案，无法解决核心问题。

那么，如果我们把我们的文件从纯文本变成更丰富、更有结构的文件会怎么样呢？通过以这种方式去除格式和语义的耦合，我们也可以在不同的系统上使用非常不同的格式(例如图形编辑器)，但是修改相同的底层语义。

你怎么想呢?