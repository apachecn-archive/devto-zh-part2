# Lua 如何避免分号

> 原文：<https://dev.to/17cupsofcoffee/how-lua-banished-the-semicolons-225o>

我目前工作之外最喜欢的项目是开发一种叫做 [Ein](https://github.com/17cupsofcoffee/ein) 的小编程语言。我在开发之初就决定不希望 Ein 有分号，所以在过去的一周里，我花了相当多的时间研究其他语言是如何做到这一点的。

Lua 对这个问题的解决方案(在我看来)相当不错，所以我想我应该写一写这个问题，希望其他人会像我一样对此感兴趣😄

## 问题

首先，一些背景——为什么去掉分号很棘手？难道我们不能把它们从我们的语言语法中删除，然后一笔勾销吗？

这个问题的答案可以用一段伪代码来概括:

```
let x = 1 // Does the statement end here?
- 1       // Or does it end here? 
```

Enter fullscreen mode Exit fullscreen mode

我们语言的解析器如何决定这应该是一个语句(`let x = 1 - 1`)还是两个语句(`let x = 1`后跟`-1`)？在解析器看来，它们都是完全有效的！

## (势)解

语言有几种方法试图解决这个问题。有些语言中的空格很重要，比如 Python。其他的，比如 Go，试图根据一组规则在幕后为你插入分号。

不过就我个人而言，我并不喜欢这些解决方案。由于我不太理解的原因，让空白有意义让我很恼火，自动插入分号感觉像是过于信任编译器去“猜测”我想让语句在哪里结束。

## Lua 是怎么做到的

Lua 的语法并不含糊，即使你去掉了所有的分号和漂亮的格式，它实现这一点的主要方式是去掉了一个我们很多人认为理所当然的特性- *表达式-语句*。

在大多数语言中，在使用语句(一段有副作用的代码，如变量声明)的地方使用表达式(一段可以求值以获得值的代码，如将两个数字相加)是完全有效的。

Lua 对此采取了更强硬的立场——程序是一系列语句，一些语句可能包含表达式(比如一个`if`的条件),但是表达式*不*被允许用作语句。

让我们回到我们最初的例子，翻译成 Lua:

```
local x = 1 -- Does the statement end here?
- 1         -- Or does it end here? 
```

Enter fullscreen mode Exit fullscreen mode

在 Lua 中，变量声明是一个语句，但是`-1`是一个表达式——因此，解释这段代码的唯一有效方式是`local x = 1 - 1`！

## 有什么蹊跷？

啊，是的，总是有一个难题，在这种情况下，这是一个相当明显的难题:如果我想让运行一个表达式来计算它的副作用呢？

例如，很多时候你想使用一个函数的返回值，但有时你只想运行它。Lua 通过对规则做一个例外来迎合这种情况，允许函数调用在语句和表达式位置使用。

然而，这是 Lua 变通规则的唯一地方之一。在某些语言中，您可以使用逻辑 AND/OR 操作符的简短循环行为作为简短而友好的控制流语句:

```
// JavaScript
isActive && doSomething(); 
```

Enter fullscreen mode Exit fullscreen mode

Lua 中的等价是无效的，除非你[将结果赋给一个临时变量:](http://lua-users.org/wiki/ExpressionsAsStatements)

```
local _ = isActive and doSomething()
-- _ has no special meaning - just a common Lua 
-- naming convention for throwing away variables! 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

感谢您的阅读！我希望我没有让你厌烦到对$x 单词的分号的漫谈！❤️

如果你对这类事情感兴趣，我推荐你看一下参考手册中的 Lua 的[完整语法——它非常简短，非常简洁，里面有很多非常有趣的设计决策，我有兴趣深入研究。](http://www.lua.org/manual/5.3/manual.html#9)