# 实用类型:什么是类型？

> 原文：<https://dev.to/stereobooster/pragmatic-types-what-are-types-45jn>

这篇文章的目的是给你一个推理类型的框架(在编程中)，我不会试图给出一个详尽的、数学上完全正确的类型定义。

另外，一些数学家认为类型没有单一的定义，这很好。

## 定义

类型是项目的集合，通常具有该类型允许的一些公共属性、结构和操作。

我故意用“集合”这个词来代替“集合”，因为集合在数学中有确切的含义。

例如

```
Cars: 🚙, 🚌, 🚜.
Fruits: 🍋, 🍐, 🍓. 
```

Enter fullscreen mode Exit fullscreen mode

此外，我们可以为这些类型定义操作

```
To drive <a car>
To eat <a fruit> 
```

Enter fullscreen mode Exit fullscreen mode

所以我们可以这样做

```
To drive 🚙
To eat 🍋 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我们混淆事物会发生什么

```
To drive 🍋 
```

Enter fullscreen mode Exit fullscreen mode

驱果是什么意思？这个操作的结果是什么？

```
TypeError: you can not drive fruits. Duh! 
```

Enter fullscreen mode Exit fullscreen mode

结果就是扯淡。同样，你可以说这是一个未定义的值，就像被零除的结果在数学上是未定义的一样。人类不知道答案。

如你所见，类型和类型错误不是计算机特有的。类型因为人类而存在。人类喜欢发现模式，根据属性对对象进行分组，然后对整个组做出结论。

现在让我们看看我们关于类型的想法在计算机世界中是否成立。

```
Cars: 🚙, 🚌, 🚜.     → Number
Fruits: 🍋, 🍐, 🍓.   → String

To drive 🚙            → To multiply numbers
To eat 🍋              → To concatenate strings 
```

Enter fullscreen mode Exit fullscreen mode

[流](https://flow.org/try/#0EQQ2AICpwRiA)

```
"a" * 1
Cannot perform arithmetic operation because string [1] is not a number. 
```

Enter fullscreen mode Exit fullscreen mode

[打字稿](http://www.typescriptlang.org/play/#src=%22a%22%20*%201)

```
"a" * 1
The left-hand side of an arithmetic operation must be of type 'any', 'number' or an enum type. 
```

Enter fullscreen mode Exit fullscreen mode

[原因](https://reasonml.github.io/en/try.html?reason=EQQ2AICpwRiA)

```
"a" * 1
Line 1, 8: This expression has type string but an expression was expected of type int 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript

```
"a" * 1
NaN 
```

Enter fullscreen mode Exit fullscreen mode

NaN 代表的不是一个数字。【IEEE(电气和电子工程师协会)就是这样称呼算术运算的无意义值的。

## 楠又如何处理错误

从机器的角度来看，有两种方法可以处理错误:

1.  引发异常。CPU 将停止当前指令的执行，并跳转到错误处理功能

2.  返回表示错误的特殊值。CPU 将继续执行当前指令

这个特殊的无意义值很棘手，因为你不能用它做任何事情

```
nonsense + 1 = ? (nonsense)
nonsense * 1 = ? (nonsense)
nonsense / 1 = ? (nonsense) 
```

Enter fullscreen mode Exit fullscreen mode

一旦你在计算过程中得到一个值，它就会在计算结束时显现出来。这也叫做有毒价值💀。一旦它进入系统，一切都会中毒。

这些值很难调试，因为错误的结果可以在远离错误发生的地方发现，并且没有留下任何痕迹。这就是为什么它是非常不鼓励使用它。

## 什么是类型检查？

答案很简单——这是当你检查给定的东西是否是集合的成员时，为了防止无意义的错误，比如对错误的类型值应用操作。

### 类型检查"由系统执行"

```
undefined()
VM180:1 Uncaught TypeError: undefined is not a function
    at <anonymous>:1:1 
```

Enter fullscreen mode Exit fullscreen mode

### 类型检查"由开发人员执行"

```
if (typeof x === "undefined") {} 
```

Enter fullscreen mode Exit fullscreen mode

### 动态类型检查或运行时类型检查

```
undefined()
VM180:1 Uncaught TypeError: undefined is not a function
    at <anonymous>:1:1 
```

Enter fullscreen mode Exit fullscreen mode

### 静态类型检查或运行前类型检查

```
// @flow
undefined()
   ^ Cannot call `undefined` because undefined [1] is not a function. 
```

Enter fullscreen mode Exit fullscreen mode

[这篇文章是](https://dev.to/t/pragmatictypes)系列文章的一部分。在[推特](https://twitter.com/stereobooster)和 [github](https://github.com/stereobooster) 上关注我。