# 回来后的其他问题:是还是不是？

> 原文：<https://dev.to/dmerejkowsky/else-after-return-yea-or-nay-ckb>

*最初发表于[我的博客](https://dmerej.info/blog/post/else-after-return-yea-or-nay/)。*

# 简介

如你所知，我在我的大多数 Python 项目中使用了 [pylint](https://www.pylint.org/) 。 <sup id="fnref1">[1](#fn1)</sup>

几周前，我升级了 pylint，出现了新的警告。当您有一个相当大的代码库时，这种情况往往会发生:新的检查会一直添加到 pylint 中，所以新的警告肯定会出现。

这里有一个触发新警告的代码的简单例子:

```
def foo():
    if bar:
        return baz
    else:
        return qux 
```

Enter fullscreen mode Exit fullscreen mode

```
$ pylint foo.py
...
Unnecessary "else" after "return" (no-else-return) 
```

Enter fullscreen mode Exit fullscreen mode

事实上，如果`bar`为真，第一次返回后的代码将永远不会执行，所以不需要`else`。

换句话说，代码应该这样写:

```
def foo():
    if bar:
        return baz
    return qux 
```

Enter fullscreen mode Exit fullscreen mode

嗯，代码比较短。但是是不是*更好*？

# 问题

仔细想想，关于代码是第一种形式的*更好*(姑且称之为*显式 else* )还是第二种形式的*隐式 else* )的问题很难回答，因为你对`foo`函数的*含义*或者`bar`、`baz`和`qux`变量一无所知。

所以让我们试着想出更好的例子。

# 守护条款

有时你会发现代码是这样写的:

```
def try_something():
    if precondition():
         result = compute_something()
         return result
    else:
        display_error()
        return None 
```

Enter fullscreen mode Exit fullscreen mode

换句话说，你在尝试做一些事情，但只有在条件为真的情况下才有可能。如果条件为假，您需要显示一个错误。

这里的`else`是显式的。

带有隐式`else`的版本如下:

```
def try_something():
    if precondition():
         result = compute_something()
         return result
    display_error()
    return None 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，还不太清楚哪个版本更好。

注意还有第三种方法来编写相同的代码，用`if not precondition()`代替:

```
# Implicit else, inverted condition def try_something():
    if not precondition():
        display_error()
        return None

    result = compute_something()
    return result 
```

Enter fullscreen mode Exit fullscreen mode

现在，看看当我们添加几个前提条件时会发生什么

```
# Explicit else def try_something():
    if precondition_one():
        if precondition_two():
            result = compute_something()
            return result
        else:
            display_error_two()
            return
    else:
        display_error_one() 
```

Enter fullscreen mode Exit fullscreen mode

```
# Implicit else, inverted condition def try_something():

    if not precondition_one():
        display_error_one()
        return

    if not precondition_two():
        display_error_two()
        return

    result = compute_something()
    return result 
```

Enter fullscreen mode Exit fullscreen mode

我希望你会同意第二个版本更好。

少了一级缩进，*显示*错误的那一行正好在*检查*错误的那一行之后。

这里为*隐式 else* 清除 win。

# 对称条件

我们再举一个例子。

假设您正在编写一个脚本，它将检查作为一组 HTML 页面编写的文档中的所有链接。

你已经得到了所有可能页面的列表，然后你需要检查两个*内部链接*(有一个`href`看起来像`../other-page`)和*外部链接*(有一个`href`看起来像`http://example.com`)。

让我们来看看这两种变体:

```
# Implicit else def check_link(link) -> bool:
    if is_internal_link(link):
        return check_internal_link(link)
    return check_external_link(link)

# Explicit else def check_link(link) -> bool:
    if is_internal_link(link):
        return check_internal_link(link)
    else:
        return check_external_link(link) 
```

Enter fullscreen mode Exit fullscreen mode

这一次，我希望你会同意明确的其他更好。

有两件事要做，从视觉上看，他们在同一水平的缩进。

保持了链接类型和需要完成的检查之间的对称性。

我们可以说，我在最后一段中作为文本描述的算法在第二个版本中用表达更好。

# 结论

Pylint 是一个很好的工具，但是在决定是否要遵循它的重构建议之前要小心。

第二，确保你的代码*易读*并且*揭示你的意图*。简洁不是这里唯一的因素。

最后，小心那些过于抽象的代码示例:)

干杯！

*感谢您阅读到目前为止:)*

我很想听听你的想法，所以请在下面留下你的评论，或者阅读[反馈页面](https://dmerej.info/blog/pages/feedback/)了解更多与我联系的方式。

* * *

1.  我已经在[一些 pylint 技巧](https://dmerej.info/blog/post/some-pylint-tips/)和[我如何 lint 我的 Python](https://dmerej.info/blog/post/how-i-lint/) 中写了关于这个的博客。 [↩](#fnref1)