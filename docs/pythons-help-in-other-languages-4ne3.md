# Python 的其他语言的“help()”。

> 原文：<https://dev.to/_bigblind/pythons-help-in-other-languages-4ne3>

在 Python 的交互式解释器中，我可以这样做:

```
>>> help(reduce) 
```

Enter fullscreen mode Exit fullscreen mode

它会弹出这样一个帮助页面:

```
Help on built-in function reduce in module __builtin__:

reduce(...)
    reduce(function, sequence[, initial]) -> value

    Apply a function of two arguments cumulatively to the items of a sequence,
    from left to right, so as to reduce the sequence to a single value.
    For example, reduce(lambda x, y: x+y, [1, 2, 3, 4, 5]) calculates
    ((((1+2)+3)+4)+5).  If initial is present, it is placed before the items
    of the sequence in the calculation, and serves as a default when the
    sequence is empty. 
```

Enter fullscreen mode Exit fullscreen mode

我也可以对非内置函数这样做。函数体开始处的三重引号字符串定义了一个“文档字符串”。

```
>>> def handshakes(n):
...     """Returns the number o handshakess that occur in a group of n people if everyone shakes everyone's hand."""
...     return (n*(n-1))/2
... 
>>> help(handshakes) 
```

Enter fullscreen mode Exit fullscreen mode

我得到这个帮助页面:

```
Help on function handshakes in module __main__:

handshakes(n)
    Returns the number o handshakes that occur in a group of n people if everyone shakes everyone's hand. 
```

Enter fullscreen mode Exit fullscreen mode

这使得交互式地探索新的库变得非常容易。我经常发现自己希望更多的语言有这个特性，尤其是 JavaScript 或探索 web APIs。

想法？