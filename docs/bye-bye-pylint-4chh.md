# 拜拜皮林特

> 原文：<https://dev.to/dmerejkowsky/bye-bye-pylint-4chh>

*最初发表于[我的博客](https://dmerej.info/blog/post/bye-bye-pylint/)。*

我使用 pylint 已经快十年了。

快进十年后，我已经决定不再使用它。

原因如下。

# 简介

先说个例子。考虑以下明显不正确的代码:

```
def foo():
    ...

if __name__ == " __main__"
    foo(1, 2, 3) 
```

Enter fullscreen mode Exit fullscreen mode

下面是运行 pylint 时的输出结果:

```
$ pylint foo.py
foo.py:4: [E1121(too-many-function-args),]
  Too many positional arguments for function call 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们看看我在使用 pylint 时遇到的几个问题。

# 痛点

## 初始设置

pylint 的初始设置总是有点痛苦。然而，如果你遵循一些建议，你就能度过难关。

## 漏报

pylint 的一个常见问题是假阴性的数量。也就是说，当 pylint 认为有问题但代码完全正常时。

例如，每当我有一个主要包含数据的类时，我喜欢使用 [attrs](http://www.attrs.org/en/stable/overview.html) 库，比如:

```
import attr

@attr.s
class Foo:
    bar = attr.ib()
    baz = attr.ib() 
```

Enter fullscreen mode Exit fullscreen mode

这几行代码给了我一个很好的人可读的`__repr__`、一套完整的比较方法、明智的构造函数(以及其他东西)，并且没有任何模板。

但是当我在这个文件上运行`pylint`时，我得到:

```
foo.py:3: [R0903(too-few-public-methods), Foo] Too few public methods (0/2) 
```

Enter fullscreen mode Exit fullscreen mode

嗯，要求你声明的每个类至少有两个公共方法是完全可以的。大多数时候，当你有一个只有一个公共方法的类时，最好用一个函数来代替，就像这样:

```
# What you wrote: class Greeter
    def __init__ (self, name="world"):
        self._name = name

    def greet(self):
    print("Hello", self.name)

# What you should have written instead: def greet(name="world"):
    print("Hello" , name) 
```

Enter fullscreen mode Exit fullscreen mode

但是这里 pylint 不知道所有由`attr`动态添加的好方法，并且错误地认为我们的设计是错误的。

因此，如果您在 CI 期间运行 pylint，并且在发现任何错误时构建失败，那么您必须插入一个特殊格式的注释来在本地禁用该警告:

```
import attr

# pylint: disable=too-few-public-methods @attr.s
class Foo:
  ... 
```

Enter fullscreen mode Exit fullscreen mode

这很快就会过时，尤其是因为每次升级 pylint 时，都会添加一组新的检查。有时他们会在您的代码中发现新的问题，但是您仍然必须检查每一个新的错误，以检查它是误报还是真正的问题。

但到目前为止，我已经设法克服了这些痛点。那么是什么改变了呢？

# 翻页

发生了两件事:

首先，我已经[开始使用 mypy](https://dmerej.info/blog/post/giving-mypy-a-go/) 和一个“真实”类型的系统 <sup id="fnref1">[1](#fn1)</sup> 。

我发现 mypy 可以捕捉到许多 pylint 会捕捉到的错误，甚至更多。

此外，由于 mypy 使用类型注释，因此它比 pylint 更快、更精确(因为它不需要“猜测”任何东西)。

最后但同样重要的是，mypy 还被设计成逐渐使用*，只有当*确定*有问题时才发出错误。*

 *其次，我决定将我的一个项目移植到 Python3.7。我不得不将 pylint 从 1.9 升级到 2.1(因为旧的 pylint 版本不支持 Python3.7)，我得到了 18 个新的 pylint 错误，其中只有*一个*是实际相关的。

就在这时，我决定后退一步。

# 类别

正如我们在这些例子中看到的，pylint 错误消息包含一个错误的简称(如`too-many-function-args`)和一个以字母为前缀的数字 ID(`E1121`)。

每个字母对应一个 pylint *类别*。

以下是完整的列表:

*   ( *F* )atal(有东西阻止了 pylint 正常运行)
*   (*E*)error(严重的 bug)
*   警告(没那么严重的问题)
*   ( *I* )nfo(比如无法解析`# pylint: disable`注释的错误)
*   ( *C* )约定(编码风格)
*   ( *R* )efactoring(可以用更清晰或更 Pythonic 化的方式编写的代码)

请注意，*致命的*和*信息*类别只有在我们试图理解为什么 pylint 不按它应该的方式运行时才有用。

# 棉绒的兴起

我意识到我可以为几乎所有的 pylint 类别使用其他 linters(不仅仅是 mypy)。

*   一些*错误*消息也可以被 [pyflakes](https://pypi.org/project/pyflakes/) 捕获，这种方法速度很快，并且很少产生误报。
*   *约定*类别也可以由 [pycodestyle](https://pycodestyle.readthedocs.io/en/latest/) 照顾。
*   一些*重构*警告(但不是全部)也可以被 [mccabe](https://pypi.org/project/mccabe/) 捕获，它测量代码复杂性。

到目前为止，我已经在 pylint 的*附加*中使用了所有这些 linters，正如在[我如何 lint 我的 Python](https://dmerej.info/blog/post/how-i-lint/) 中所解释的

但是如果我完全停止使用 pylint 呢？

我所丢失的将是一些*重构*消息，但是我假设它们中的大部分会在代码审查期间被发现。作为交换，我可以摆脱所有这些嘈杂的评论。(其中 34 行大约有 5000 行代码)

这就是我停止使用 pylint 并将其从 CI 脚本中删除的原因。我向 pylint 的作者和维护者道歉:这些年来你们确实做了很好的工作，但是我现在认为是时候继续前进，使用新的更好的工具了。

# 接下来是什么

这并不是我永无止境地寻找工具来帮助我编写更好的 Python 代码的故事的结尾。你可以在 [Hello falke8](https://dmerej.info/blog/post/hello-flake8/) 中读到故事的其余部分。

*感谢您阅读到目前为止:)*

我很想听听你的想法，所以请在下面留下你的评论，或者阅读[反馈页面](https://dmerej.info/blog/pages/feedback/)了解更多与我联系的方式。

* * *

1.  顺便说一下，在 *[的最后，我给了 mypy 一次机会](https://dmerej.info/blog/post/giving-mypy-a-go/)* ，我说我很好奇 mypy 是否会在[的大规模重构](https://github.com/SuperTanker/tbump/pull/24/commits/7aecba923feda081360d36892f8716045d0b1bd0)中提供帮助。是的，比我希望的还要好！ [↩](#fnref1)*