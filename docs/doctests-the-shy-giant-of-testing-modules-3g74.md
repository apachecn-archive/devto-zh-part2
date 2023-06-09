# 测试模块的害羞巨人

> 原文：<https://dev.to/perigk/doctests-the-shy-giant-of-testing-modules-3g74>

[![](img/b244aad9072a75b0f1fbc47b64125eb6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rK_651r9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/568/1%2AeMH1zFW_YagZqorGErpcVg.jpeg) 

<figcaption>奈德·博罗米尔·史塔克同意</figcaption>

你用 python 吗，连洗衣服都用？你是否发现单元测试很无聊，但仍然不得不做，因为你在自动化测试中发现了价值？那么这篇文章就送给你了。

### 想法

我相信你经常使用 python 控制台。让我们假设您正在编写一些类似下面这样的内联函数，来试验一些东西:

```
$ python
Python 3.6.4 |Anaconda custom (64-bit)| (default, Jan 16 2018, 18:10:19) 
[GCC 7.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> def addme(a):
...     return a + a
... 
>>> addme(2)
4
>>> addme(1.9)
3.8
>>> addme(0)
0
>>> 
```

因此，您已经编写了一个内联函数，并对它进行了几次测试，以进行一些基本的验证。如果 python 可以在运行时读取上面的输出并为您进行推理，会怎么样？这就是 doctests 背后的想法，我的朋友。

### 当真？

[![](img/eff60d333ed25da6aa6c93aad6787b5e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HYsUCLXx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/236/1%2A9UAvMlcfo2ZROWRPa3HODA.jpeg)T3】冷静下来的帕特里克

总的来说，python 不时地引入了许多直观的、令人心碎的特性，这些特性现在已经成为一些主要语言的主流。为什么这个也不行？

### doctests 的好处

好处有很多，但不限于:

*   它们写起来简单得可笑。我的意思是，你甚至可以把它外包给你的研究土壤原子解剖的表弟，因为他欠你的，因为你修好了他的电脑。
*   除非你觉得复制粘贴很难，否则他们更乐于写作。不过，这一次您必须熟悉终端环境。
*   没有必要打开另一个文件(即使你可以把你的应用程序的所有文档测试放到一个文件中)来读取任何函数的测试代码，因为它们就在每个函数的签名下面。
*   它们可以用 doctest 模块执行，即使不了解一点 python 也可以阅读。

### 单元测试呢？

单元测试仍然提供了巨大的价值和先进的测试能力。虽然 doctests 在验证简单和纯粹的函数方面很棒，但当您必须进行复杂的验证时，它们就不太好了。例如，如果在测试过程中调用了一系列命令。

### 嘲讽呢？

Doctests 可以优雅地处理嘲讽，它有一个很棒的库叫做 [Minimock](https://pypi.org/project/MiniMock/) 。我鼓励你尝试一下，并让我知道你的想法。

尽管我喜欢这个计划，但我更喜欢我的测试有单独的角色。我不希望我的 doctests 负载过重，而且一刀切。但这真的是一个品味的问题，如果你不同意并没有错，如果是这样的话，我很高兴听到你的理由。

### 一个工作实例

光说不练，还是写点代码吧。下面是一个工作示例，使用上面提示的功能。

```
addme(a):
    """
    This is a docstring, usually to explain the use of a function. Please do not confuse it with doctests. They both mean to provide forms of documentation, but doctests are executable too.
    >>> addme(4)
    8
    >>> addme('a')
    'aa'
    >>> addme(set())
    Traceback (most recent call last):
        ...
    TypeError: unsupported operand type(s) for +: 'set' and 'set'
    """
    return a + a

if __name__ == '__main__':
    import doctest
    doctest.testmod()
    print(addme(1)) 
```

这里有几点值得注意:

*   doctests 需要存在于 docstring 中。在那里，只需“复制-粘贴”python 控制台快速测试的输出。
*   如果您需要模拟一个异常情况，您只需要添加异常消息的第一行和最后一行。您是否期望硬编码路径或执行任何奇怪的逻辑来获得文件的完整路径并完整地打印出来？拜托，python 不会这么做的:)
*   您需要 doctest 库来运行测试。虽然鼻子不需要它。
*   您可以像往常一样用 python mydoctests.py 运行上面的代码

### 我需要我的测试作为 CI/CD/CT 循环的一部分运行。我有什么好处？

我不是来让你失望的，是吗？:)

除了单元测试之外， [nose](http://nose.readthedocs.io/en/latest/) 测试运行器还支持运行所有的文档测试。只需添加带有 doctest 的标志，就可以开始了。

### 好东西，何去何从？

*   阅读 python [文档](https://docs.python.org/3.6/library/doctest.html)获得更多的抽搐和案例。
*   阅读这本神奇的[书](https://amzn.to/2sjvKoP)，尤其是第四章，它涵盖了文档测试。
*   检查其他语言的实现。例如，[节点的那个](https://github.com/davidchambers/doctest)
*   [Doctests 和 nose](http://nose.readthedocs.io/en/latest/plugins/doctests.html)

感谢您阅读这篇文章，我希望您喜欢它，并且这篇文章引发了您编写更多更少痛苦的测试的兴趣。请用下面的按钮传播爱，如果是的话。您也可以随意添加自己对 doctests 的想法和体验。

*最初发布于*[https://peri GK . github . io](https://perigk.github.io)