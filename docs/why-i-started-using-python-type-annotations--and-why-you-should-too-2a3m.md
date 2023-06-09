# 为什么我开始使用 Python 类型注释，为什么你也应该这样做

> 原文：<https://dev.to/florimondmanca/why-i-started-using-python-type-annotations--and-why-you-should-too-2a3m>

当 Python 3.6 在 2016 年 12 月发布时，我对它提供的一些新功能感到兴奋。其中， [f 弦](https://www.python.org/dev/peps/pep-0498/?)对我来说是一个相当大的亮点(如果你还没有使用它们，今天就开始吧！).

在我升级我的堆栈使用 Python 3.6 后的一段时间，我听说了类型注释，这要感谢 Dan Bader 的一个视频。他解释了什么是类型注释，以及为什么他发现它们很有用。

虽然它们是在 Python 3.5 中引入的(早在 2015 年)，但我几个月前才开始使用类型注释。

因为类型注释帮助我和我的同事写出更可读、更优雅、甚至更好的代码，我现在在我所有的项目中使用类型注释。

我想现在也是分享我的经验的好时机！

## Python 中的静态打字？！不，不适合我

当我第一次听说类型注释时，我并不相信。我认为类型注释是 Python 语言本身的某种黑客行为。

在使用动态类型语言的同时指定类型的想法对我来说似乎很奇怪，因为我多年来一直在 Python 的动态特性方面做得很好。

当时，我对编写类似于
的代码非常满意

```
def set_pos(self, pos):
    self.x = pos[0]
    self.y = pos[1] 
```

Enter fullscreen mode Exit fullscreen mode

`pos`应该包含什么？嗯，这是显而易见的——只要看一下代码，你就能立即判断出它应该是一个包含两个数字的元组(*什么样的数字？整数？彩车？*)。

我还了解到 Python 的运行时实际上根本没有使用类型注释。他们完全被*忽略了*。所以我想——如果对我写的代码的执行没有影响又有什么用呢？

我真的不明白为什么要把静态类型引入 Python。

## 直到你开始和其他开发者一起工作

2018 年 3 月，我开始在 [ComplyAdvantage](https://complyadvantage.com) 做软件工程实习生。我很幸运，这家初创公司对待实习生的信任和责任与对待正式员工一样。因此，从那时起，我就一直致力于富有挑战性的真实项目。

事实上，这是我作为开发人员的第一次工作经历。我以前从来不需要阅读其他人的代码，他们也从来不需要阅读我的代码。你猜对了——Python 在 ComplyAdvantage 风靡一时。

我读的第一批代码之一是我的导师写的。他是那些有很强 Java 背景的人之一，他们也选择了 Python 作为他们的工作——并且在这方面做得很好。来自静态类型语言的世界，他是 Python 类型注释的强烈支持者，并且在他的代码中使用它们。当我问为什么时，他粗略地对我说:

> 只是大家都比较清楚。类型注释告诉其他人你的代码的输入和输出是什么——他们甚至不需要问。

这句话如此关注其他人的事实让我震惊。

他基本上是说**人们使用类型注释来帮助其他人更容易理解他们的代码。**

## 可读性很重要

想一想。当你从事一个项目时，你写的代码可能对你来说很有意义。你觉得没有必要记录太多。

但是其他人(包括 6 个月后的你)将不得不**阅读你的代码并理解它的全部含义**。我从一位同事那里得知，他们至少需要回答**三个基本问题**:

1.  这段代码将什么作为输入？
2.  它是如何处理输入的？
3.  它的产出是什么？

随着我阅读越来越多的其他人的代码——包括复杂的遗留代码——我意识到类型注释实际上非常有用。他们在帮我以光速回答问题 1/和 3/ **。(问题 2 可以用有意义的函数名来回答。)**

## 让我们玩一个快速游戏

我在下面写了一个函数，它的体被隐藏了。你能告诉我它是做什么的吗？

```
def concat(a, b):
    # ... 
```

Enter fullscreen mode Exit fullscreen mode

以下是我的看法——从函数名来看，我会说`concat()`采用两个列表(*或元组？*)并将它们连接起来返回一个包含`a`和`b`元素的列表。

很明显，对吧？不尽然。

其实还有其他的可能性。例如，如果`concat()`只是简单地连接两个字符串会怎么样？

事情是这样的——**我们并不真正理解`concat()`是做什么的，因为我们无法从上面回答所有三个问题。**我们只能通过“它进行某种连接”来粗略地回答问题 2。

现在，让我们将**类型注释**添加到`concat()` :

```
def concat(a: int, b: int) -> str:
    # ... 
```

Enter fullscreen mode Exit fullscreen mode

啊哈！实际上，我们两次猜测都错了。好像是`concat()`取两个整数，输出一个字符串。

所以我现在会说——它接受两个整数作为输入，将它们转换成字符串表示，将它们连接起来，然后返回结果。

这正是它所做的:

```
def concat(a: int, b: int) -> str:
    return str(a) + str(b) 
```

Enter fullscreen mode Exit fullscreen mode

这个例子向您展示了**知道输入和输出对于理解一段代码**是至关重要的。和类型注释帮助您让您的读者几乎立即知道。

## 曾经有一个变通办法

回想我的经历，我已经知道这一点——在我开始使用类型注释之前——你可能也知道。

我总是喜欢写干净的代码，并尽我所能地记录下来。我认为在所有的函数和类上添加一个 docstring 来解释它们做什么(*功能*)以及它们为什么存在(*目的*)是一个很好的原则。

这是几年前我参与的一个个人项目的实际代码片段:

```
def randrange(a, b, size=1):
    """Return random numbers between a and b.

    Parameters
    ----------
    a : float
        Lower bound.
    b : float
        Upper bound.
    size : int, optional
        Number of numbers to return. Defaults to 1.

    Returns
    -------
    ns : list of float
    """
    ... 
```

Enter fullscreen mode Exit fullscreen mode

*让我们看看…一个描述参数及其类型、输出值及其类型的 docstring】*

哇哦。

从某种意义上说，**我已经在使用类型注释了——通过 docstrings。**

不要误解我的意思:**当组件有很多逻辑时，用 docstrings 记录你的代码是非常棒和有用的**。有一些标准格式(我在上面使用了 [NumPy doc 格式](http://numpydoc.readthedocs.io/en/latest/format.html))很有帮助，因为它们确保了文档约定，也可以被一些 ide 解释。

然而**对于简单的函数**，使用成熟的 docstring 来描述参数和返回值**有时感觉像是一种变通方法**——因为 Python 并没有提供任何类型暗示的概念(*或者我是这么认为的*)。

**类型注释有时可以完全取代文档字符串**,在我看来，这是一种非常干净简单的输入和输出文档化方式。最后，**你的代码对你和你的开发伙伴来说都更容易阅读。**

## 但是等等！还有更多

Python 3.5 中添加了类型注释和类型模块。

这个模块提供了注释各种类型(如列表、字典、函数或生成器)的方法，甚至支持嵌套、泛型和定义自定义类型的能力。

我不会深入讨论`typing`模块的细节，但我只想分享我不久前发现的一些东西:类型注释可以用来生成代码。

我举`namedtuple`的例子。这是来自`collections`模块的一个数据结构——就像我们已经在[Python 中 ChainMap 的实际应用](https://dev.toa-practical-usage-of-chainmap-in-python)中介绍过的 ChainMap 一样。

`namedtuple`所做的是生成一个类，其实例的行为类似元组(它们是不可变的)，但允许通过点符号访问属性。

`namedtuple`的典型用法如下:

```
from collections import namedtuple

Point = namedtuple('Point', 'x y')
point = Point(x=1, y=5)
print(point.x)  # 1 
```

Enter fullscreen mode Exit fullscreen mode

如果我们记得我们说过的记录输入和输出的重要性，这里就缺少了一些东西。**我们不知道`x`和`y`的类型是什么**。

原来，`typing`模块有一个相当于`namedtuple`的模块，叫做`NamedTuple`，它允许你使用类型注释。

让我们用`NamedTuple` :
重新定义`Point`类

```
from typing import NamedTuple

class Point(NamedTuple):
    x: int
    y: int

point = Point(x=4, y=0)
print(point.x)  # 4 
```

Enter fullscreen mode Exit fullscreen mode

我喜欢这个。漂亮、干净、显式的 Python 代码。

注意，`Point`的用法和以前完全一样，除了我们现在受益于更可读的代码——我们的 ide 和编辑器也可以帮助我们检测潜在的类型错误，这要感谢静态检查器，比如 [MyPy](http://mypy-lang.org) (以及它们的各种[集成](https://atom.io/packages/linter-mypy))。

使用类型注释可以做更多令人兴奋的事情，特别是因为它们现在是 Python 语言的核心部分。

例如，Python 3.7 引入了[数据类](https://www.python.org/dev/peps/pep-0557/#id7)，这是一种为简单而高效的数据存储生成类的令人兴奋的新方法。然而，它们完全值得拥有自己的博客帖子，所以我会把它们留到以后。

## 哲学呢？

Python 被设计成一种动态编程语言，我们现在向它引入静态类型。在这一点上，有些人可能会想:

这怎么能符合语言的哲学呢？

Python 核心开发人员刚刚意识到动态类型是一个错误吗？

不完全是。试着在谷歌上搜索`python philosophy`，你会得到以下结果:

[PEP 20-Python 之禅](https://www.python.org/dev/peps/pep-0020/)

Python 的禅推动了整个语言的哲学。

在我看来，**类型注释 100%符合 Python 的哲学**。这里有一些他们完美体现的格言。

### 显性比隐性好。

这基本上是最初发明类型注释的原因。只是比较:

```
def process(data):
   do_stuff(data) 
```

Enter fullscreen mode Exit fullscreen mode

还有:

```
from typing import List, Tuple

def process(data: List[Tuple[int, str]]):
    do_stuff(data) 
```

Enter fullscreen mode Exit fullscreen mode

### 简单胜于复杂。

在简单的情况下，使用类型提示比求助于成熟的文档字符串要简单得多。

### 可读性很重要。

我们已经讨论过这个了。😉

### **应该有一种——最好只有一种——显而易见的方法来做这件事。**

这是通过类型注释的严格(然而简单)语法实现的。如果您想在 Python 中记录和支持静态类型，它们是必不可少的！

最后…

## 现在总比没有好

对我来说，**类型注释是游戏规则的改变者**:

*   他们改进了我写代码的方式。
*   通过提供记录输入和输出的标准方法，它们帮助你和其他人更容易理解和推理代码。
*   它们还支持以更干净、更简洁的方式编写代码的新方法。

如果你还没有使用类型注释:现在就开始吧！有许多[的](https://www.youtube.com/watch?v=QCGwDOk-pIs)和关于他们[的](https://www.youtube.com/channel/UCI0vQvr9aFn27yR6Ej6n5UA)精彩内容可以开始了。

如果你已经在使用——并希望挖掘——类型注释:帮助传播爱！写下来，与你的同事和开发伙伴分享。

类型注释是不可思议的，现在是 Python 语言的核心部分。让我们多使用这些！💻