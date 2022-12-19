# Python 中的类型检查

> 原文：<https://dev.to/on_stash/type-checking-in-python-1mcn>

类型检查函数参数是 Python 中一种常见的函数参数完整性检查。

## “类型检查函数参数”是什么意思？

假设你有一个函数`add`，它有两个参数`a`和`b`。有一个隐含的理解是`a`和`b`必须是整数(类型`int`)或者是浮点(类型`float`)。

```
def add(a, b):
    return a + b 
```

`add(1, 2)`给出输出`3`,这是我们想要的函数。
但是`add([2], [3])`似乎在返回`[2, 3]`，一个串接的列表。
还有`add((2,), (3,))`似乎是返回`(2, 3)`，一个串接的元组。

为了仅对`int`和`float`参数进行操作，我们可以添加`type`或`isinstance`检查。查看这篇 [StackOverflow 帖子](https://stackoverflow.com/questions/1549801/what-are-the-differences-between-type-and-isinstance)，了解`type`和`isinstance`检查的区别。

```
valid_add_types = (int, float)
def add(a, b):
    if isinstance(a, valid_add_types) and isinstance(b, valid_add_types):
        return a + b
    return None 
```

`add(1, 2)`给出输出`3`,这是我们想要的函数。
`add([2], [3])`和`add((2,), (3,))`返回`None`，因为它们既不是整数也不是浮点数。

优点:

*   它可以帮助开发人员一眼就明白函数需要什么类型的参数
*   对现有和新开发人员有用
*   Bug 修复和特性增加没那么痛苦

缺点:

*   对于小型代码库来说，这是易于管理的
*   手动类型检查使大型代码库变得复杂

在讨论如何改善 Python 开发人员的开发体验(DX)的过程中，我的导师 Sriram 和我想到了一个用于类型检查函数参数的类型检查装饰器的想法。

介绍 [`typy_checker`](https://github.com/onstash/typy_checker) ，一个易于使用的库，用于对你的函数进行类型检查而不会失去理智。

下面是我们如何在例子中使用`typy_checker`。

```
from typy_checker import type_checker

valid_add_types = (int, float)
@type_checker(a=valid_add_types, b=valid_add_types)
def add(a, b):
    return a + b 
```

调用`add([2], [3])`将导致以下错误-

```
SystemError: add(): Invalid type for variable 'a'. Expected int / float but got list. 
```

从我的经验来看，使用`typy_checker`会产生更干净的代码库。我已经在两个代码库中使用了它，目前它们正在生产中运行。