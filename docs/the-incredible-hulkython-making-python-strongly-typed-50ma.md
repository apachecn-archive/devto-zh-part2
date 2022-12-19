# 不可思议的绿巨人(ython):让 Python 变得强大(ly 类型化)

> 原文：<https://dev.to/perigk/the-incredible-hulkython-making-python-strongly-typed-50ma>

Python 是一种很棒的动态类型语言，但是很多人认为这是它最大的缺点。

### 但是为什么呢？

即使动态类型语言消除了编写“普通”类型声明的麻烦，并使编写更愉快和更快，这种需求也只是委托给语言的运行时环境。

这意味着，一些本可以被消除的错误，几乎在它们被引入后，它们现在会保持沉默，直到代码被调用，你知道这什么时候会发生，对吗？

[![](../Images/47945037e90ea5e62368aa0f5f5d232b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Lb91ZZd4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/615/1%2AAwcmdb1xwSKXM7aID2TP1A.png)

### 与类型打交道，不考虑自杀:)

从 3.5 版本开始，Python 增加了对类型提示的可选支持，主要是通过[类型化](https://docs.python.org/3/library/typing.html)模块。

看起来，他们想在这两个词上妥协。一方面，喜欢动态类型自由的人可以继续……忽略类型提示。另一方面，喜欢静态类型安全性的人可以从利用新功能中受益。

### 如何使用

使用它的方法，或者至少是开始使用它的方法非常简单明了。更具体地说，如果你熟悉的话，它看起来很像静态类型的 typescript [方式](https://www.typescriptlang.org/docs/handbook/basic-types.html)。这里有一个例子:

```
# Typing is the core module that supports type checking.

# In here we import List, which provided equivalent functionality to

# the list() function or the [] equivalent shorthand

from typing import List

# We define a function, as usually but we add the expected

# type to the args and we add a return type too

def find\_files\_of\_type(type: str, files\_types: List[str]) -> bool:

return (type in files\_types)

files\_types: List[str] = [‘ppt’, ‘vcf’, ‘png’]

type\_to\_search: str = ‘ppt’

print(‘Found files of type {} in list? {}’.format(type\_to\_search,

find\_files\_of\_type(type\_to\_search, files\_types))) 
```

Enter fullscreen mode Exit fullscreen mode

有点尴尬，但还是清楚的，对吧？:)

### 绿巨人落入的陷阱

你可能已经注意到我在上面几行提到了“可选”这个词。因此，在撰写本文时，还没有强制类型检查。

你可以给你的变量添加任何不相关的类型，对它们做最无效、不相关和“变态”的操作，但是 python 不会眨一下眼睛。

如果你想**执行**类型检查，你应该使用类型检查器(duh？)，像伟大的 [mypy](http://mypy-lang.org/examples.html)

当然，大多数 ide 都有一些类型检查的功能。这里的是 Pycharm 的相关文档。

### 以为我愿意看到未来

*   在语言的核心中集成类型检查机制
*   由于上述原因，更无缝的类型提示。例如，如果类型检查是打开的，那么我就不应该使用类列表或元组来做这件事。[]和()人手应该足够了

### 结论

感谢您阅读这篇文章。这绝不是 python 强大功能的扩展指南，而是引导更多研究的入门书。

如果您现在开始一个 python 3.5+的新项目，我强烈建议您尝试一下类型检查。我很乐意看到你对这个功能的建议和想法，所以请随意发表评论。

*最初发表于*[*peri GK . github . io*](http://perigk.github.io)*。*

* * *