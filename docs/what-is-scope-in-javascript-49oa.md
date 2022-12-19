# Javascript 中的作用域是什么？

> 原文：<https://dev.to/kartik2406/what-is-scope-in-javascript-49oa>

[![Scope](img/2aa9648a577155985ac2982ab333e460.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sO3Yn21p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2wukbiqmde4hu7lbdeve.jpg)

在编程时，我们经常处理数据，我们需要存储和操作数据以获得有用的结果。在这篇文章中，我们将学习 Javascript 中的变量，什么是范围，以及它如何影响你的变量。

# JS 中的 Scope 是什么？

范围是可访问声明变量的区域。

JS 中有三个级别的作用域:

*   **全局**:变量在整个程序中可用
*   **函数级**:变量仅在函数中可用
*   **块级**:变量只在声明的代码块中可用。if、for 等语句是代码块的例子。您可以不使用这些语句来创建代码块，只需在{}内编写代码即可。拥有块级变量非常有用，因为您可以在其他代码块中重用变量名，而不必担心。

Javascript 有 3 个不同的关键字，允许你声明(创建)变量。
他们是 **var，let，const** 。这些关键字决定了变量的范围。

如果在函数中声明， **var** 关键字创建一个具有**函数级**作用域的变量。**让**、 **const** 关键字创建一个具有**块级**范围的变量。这样你就不会不小心覆盖了一些全局值。关键字 **const** 有另一个特性，使用它你可以创建用于存储消息、数学常量等的常量。

> 请注意，如果您使用这些关键字中的任何一个在函数顶部创建一个变量，它将是全局的，因为它的作用域是整个文件

不使用 var，let const 关键字也可以创建变量。当你在**严格模式** *(在后面的文章中会有更多的介绍)*中这样做的时候，它会抛出一个错误，但是在正常模式下，它会创建一个全局变量，这将导致将来的错误。

## 该用什么？

为了避免无意的错误，你应该总是使用 **let** 来表示你想要变异的变量，使用 **const** 来表示常量。

## 如何创建变量？

创建(声明)变量的语法是 *= value* 。在 let 和 var 的情况下，在声明时赋值不是强制性的，但是对于 const，如果你不赋值，它会抛出一个错误。

也有一些规则管理你的变量的命名，检查这个[链接](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/First_steps/Variables#An_aside_on_variable_naming_rules)获得更多信息。