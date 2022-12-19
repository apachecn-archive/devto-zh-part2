# Elixir 运算符重新定义背后的秘密:+ to -

> 原文：<https://dev.to/blackode/the-secret-behind-elixir-operator-re-definitions--to---30om>

[![Photo by [rawpixel.com](https://unsplash.com/photos/lRssALOk1fU?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)](../Images/a99c95f42f8026d79f5f0a642db54149.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--ecBl1OkT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/13378/1%2AyZWunekTH1rb4DgIGwoPXw.jpeg) *照片由[rawpixel.com](https://unsplash.com/photos/lRssALOk1fU?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)* 

大家好。今天，我们要做一些疯狂的事情。这篇文章只是为了好玩。唯一的目的是向你展示这在仙丹中的可能性。

**+** 的普遍含义是相加，但为了改变，我们把它作为**减法**。

## **如何实现这一点？**

我们可以使用 **def** 构造`(def, defp, defmacro, …)`来重新定义 elixir 中的操作符。

## **唯一的 1 条规则**

唯一的规则是一个**名**的定义。这应该和我们在编码时如何使用操作符是一样的。一般来说，我们用`+`作为`a + b`，所以我们的定义名应该和 **a + b** 一样。

## **让我们做错事**

你所有的**操作符重定义* *应该驻留在* *模块**内，因为众所周知，**定义**不能站在模块外。

检查以下代码，重新定义`+`到`-`

```
defmodule MyWrongOperators do

  def a + b do
    a - b
  end

end 
```

这里没有什么新东西。我们刚刚创建了一个**函数**或**定义**，但是用了一个**的小名字**。

**轰！！**你颠覆了这个世界。我们只是在尽可能地做事情，而且长生不老药允许我们这么做。但是这些在现实世界中是没有关系的。只是为了好玩。

## **如何使用？**

在这里，在长生不老药中，存在着一种把你从使用中拉回来的力量，即使它允许你毫无障碍地创造。

如果您像在常规风格的编码中一样强制使用该模块，它会给您带来一个异常。让我们检查一下。

```
iex> import MyWrongOperators

iex> 1 + 2

** ** (CompileError) iex:4: function +/2 imported from both MyWrongOperators and Kernel, call is ambiguous 
```

[![Compile Error When Using the Module Directly](../Images/f348e9fff86a6d921e2f80b2df666bf6.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--9AXVli86--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3822/1%2AZ6xJha0Y-bHkXSyxgJzDhw.png) *直接使用模块时编译错误*

好吧，是什么力量让你犯了这个错误？你能想一会儿猜一猜吗？

我不想再纠缠你了。默认加载的内核模块。这个内核模块包含一个+/2 函数，它也被加载。因此，当我们导入我们的模块 MyWrongOperators 时，我们再次加载了+/2 函数。现在在我们的范围内，存在两个+/2 定义。所以，编译器在这里混淆了。

## 如何绕过这个？

嗯，我们需要内核模块中除了+/2 以外的所有函数。这可以用来实现，除非在加载模块时。

```
import Kernel, except: [+: 2]
import MyWrongOperators 
```

上面的 LOC 明确告诉不要加载+/2 函数。因此，我们只有一个来自模块 MyWrongOperators 的+/2 定义。因此，编译器现在可以使用+/2 定义而不会产生混淆。

```
iex > import Kernel, except: [+: 2]
 Kernel

iex> import MyWrongOperators
 MyWrongOperators

iex> 1 + 2
-1            # 1 - 2 operation performed here as we defined. 
```

[![The perfect Usage](../Images/68051c6b6d7a40cac7f83493115d67b8.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--Oy_8wnlj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3826/1%2AH00Zk9PqI0DYw1M9XghYew.png) *完美用法*

在上面的屏幕截图中，在 iex(3)行，我们加载了内核模块，但没有让它加载+/2 定义。所以，它工作得很完美。

我们成功地执行了错误的操作。

## 要记住的事情

**问:我可以创建新的操作员吗？**
俺们。不，但是，Elixir 能够解析一组预定义的操作符，比如

*   |||

*   &&&

*   <<<

*   ~>>

*   <

*   ~>

*   <sup><sup>^</sup></sup>

**问:我可以覆盖所有其他运算符吗？**
俺们。是的。你可以。但是，在使用时，不要忘记导入内核模块，将那些覆盖操作符作为例外。

问:重写操作符好吗？
俺们。这当然不好，也不推荐。

**问:为什么不推荐？**
俺们。自定义操作符可能很难阅读，甚至更难理解，因为它们不像函数那样有一个描述性的名称。

## 现场演示

[![[https://asciinema.org/a/151933](https://asciinema.org/a/151933)](../Images/3b3003a77c2a429c94094124f0f27296.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yrMFJKed--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2152/1%2AdLyXfERe8jP4YkkC7k3c6A.png)

***警告***
高度不建议做…

快乐的长编码生活:)