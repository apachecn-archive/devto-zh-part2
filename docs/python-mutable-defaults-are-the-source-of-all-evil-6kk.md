# Python 可变缺省值是所有罪恶的根源

> 原文：<https://dev.to/florimondmanca/python-mutable-defaults-are-the-source-of-all-evil-6kk>

今天，我想分享一个关于 Python 开发人员仍然容易犯的一个常见错误的快速挽救生命的建议。

## TL；速度三角形定位法(dead reckoning)

不要在 Python 中使用可变的默认参数，除非你有非常好的理由这样做。

为什么？因为它会导致各种**讨厌而可怕的 bug**，让你**头疼**和**浪费大家的时间**。

相反，默认为`None`，并在函数内部分配可变值。

## 故事时间

在我早期的开发旅程中，这个错误困扰了我几次。

*   当我刚开始用 Python 做真正的项目时(可能是 5-6 年前)，我记得偶然发现了一个非常奇怪的 bug。当一个函数被多次调用时，列表会变得比预期的要大，这将奇怪地导致错误。
*   上大学时，我们被分配了一个算法开发项目，老师发给我们一个程序，对我们的代码进行单元测试。在某一点上，我绝对相信我的代码是正确的，但是测试一直失败。

在第一种情况下，年轻的开发人员学习如何调试我变得很生气，因为他不知道为什么列表变得太大，他花了**小时**试图解决这个问题。

第二种情况，其他同学也遇到了问题，指导老师也毫无头绪，于是**大家的时间都浪费了**。

在这两种情况下，如果我们知道这个常见的错误，就可以节省时间和精力。

## 我们做错了什么？

事实证明，在这两种情况下，**一个空列表被用作函数**的默认参数。是的，就这样。类似于:

```
def compute_patterns(inputs=[]):
    inputs.append('some stuff')
    patterns = ['a list based on'] + inputs
    return patterns 
```

Enter fullscreen mode Exit fullscreen mode

自己尝试一下:如果你多次运行这个函数，你会得到不同的结果！

```
>>> compute_patterns()
['a list based on', 'some stuff']  # Expected >>> compute_patterns()
['a list based on', 'some stuff', 'some stuff']  # Woops! 
```

Enter fullscreen mode Exit fullscreen mode

虽然看起来不怎么样， **`inputs=[]`却是造成这个烂摊子的**的淘气鬼。

## 问题

在 Python 中，当在函数中传递一个可变值作为默认参数时，**默认参数会在该值发生变化的任何时候发生变化**。

这里，“可变值”指的是任何东西，比如一个列表、一个字典甚至一个类实例。

## 到底发生了什么？

要明白为什么上述事实会成为一个问题并不容易。

下面用另一个例子来详细说明发生了什么:

```
def append(element, seq=[]):
    seq.append(element)
    return seq 
```

Enter fullscreen mode Exit fullscreen mode

```
>>> append(1)  # seq is assigned to [] [1]  # This returns a reference the *same* list as the default for `seq` >>> append(2)  # => `seq` is now given [1] as a default! [1, 2]  # WTFs and headaches start here… 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，Python“保留”了默认值，并以某种方式将其与函数联系起来。既然在函数内部变异了，默认也变异了。最后，**我们每次调用**函数都用不同的默认值——咄！

## 解

长话短说，解决办法很简单。

**使用`None`作为默认值，并在函数内部分配可变值。**

因此，请这样做:

```
 # 👇 def append(element, seq=None):
    if seq is None:  # 👍
        seq = []
    seq.append(element)
    return seq 
```

Enter fullscreen mode Exit fullscreen mode

```
>>> append(1)  # `seq` is assigned to [] [1]
>>> append(2)  # `seq` is assigned to [] again! [2]  # Yay! 
```

Enter fullscreen mode Exit fullscreen mode

这实际上是 Python 中非常常见的模式；我发现自己在每个项目中都要写几十遍。

这是如此普遍，有一个 Python 增强建议(PEP)目前正在工作中( [PEP 505 -无意识操作符](https://www.python.org/dev/peps/pep-0505/#syntax-and-semantics))，它将允许简化上述代码，只需编写:

```
def append(element, seq=None):
    seq ??= []  # ✨
    seq.append(element)
    return seq 
```

Enter fullscreen mode Exit fullscreen mode

PEP 仍然是一个草案，但我只是想提一下，因为我真的希望无意识操作符很快成为 Python 中的一个东西！🔥

## 吸取教训

这就对了。希望您再也不会看到自己在 Python 代码中使用可变缺省值(除非您想扰乱每个人的神经)。

如果你看到其他人在使用它们，传播这个提示，也可以拯救他们的生命。🙏

## 保持联系！

如果你喜欢这篇文章，你可以在 Twitter 上找到我，获取更新、公告和新闻。🐤