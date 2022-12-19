# 用 Python 制作一个递归匿名阶乘函数。

> 原文：<https://dev.to/vladimirfomene/make-a-recursive-anonymous-factorial-function-in-python-4dl3>

我目前正在伯克利大学修[计算机程序的结构和解释](https://inst.eecs.berkeley.edu/~cs61a/sp18/)。这是一个来自家庭作业的好问题。你可能想亲自尝试一下。

*Q6:匿名阶乘*
递归阶乘函数可以通过使用条件表达式写成单个表达式。

```
>>> fact = lambda n: 1 if n == 1 else mul(n, fact(sub(n, 1)))
>>> fact(5)
120 
```

Enter fullscreen mode Exit fullscreen mode

然而，这个实现依赖于这样一个事实(没有双关的意思)，即*事实*有一个名字，我们在*事实*的主体中引用了这个名字。为了编写一个递归函数，我们总是用一个 *def* 或者赋值语句来给它命名，这样我们就可以在它自己的主体中引用这个函数。在这个问题中，你的工作是递归地定义事实，而不用给它一个名字！

编写一个表达式，仅使用调用表达式、条件表达式和 lambda 表达式(没有赋值或 def 语句)来计算 *n* 阶乘。特别注意，不允许在返回表达式中使用 *make_anonymous_factorial* 。来自*操作器*模块的 *sub* 和 *mul* 函数是解决该问题所需的唯一内置函数:

```
from operator import sub, mul

def make_anonymous_factorial():
"""Return the value of an expression that computes factorial.

>>> make_anonymous_factorial()(5)
120
>>> from construct_check import check
>>> check(HW_SOURCE_FILE, 'make_anonymous_factorial', ['Assign', 'AugAssign', 'FunctionDef', 'Recursion'])
True
"""
return 'YOUR_EXPRESSION_HERE' 
```

Enter fullscreen mode Exit fullscreen mode