# 为什么 Pylint 既有用又不可用，以及如何实际使用它

> 原文：<https://dev.to/itamarst/why-pylint-is-both-useful-and-unusable-and-how-you-can-actually-use-it-lkg>

这是一个关于一个工具的故事，它在我们发布代码的前一天发现了一个影响生产的 bug。这也是一个没人用的工具的故事，而且理由很充分。当您完成阅读时，您将会明白为什么这个工具有用，为什么它不可用，以及如何在您的 Python 项目中使用它。

(不是 Python 程序员？同样的问题和解决方案可能也适用于您的生态系统中的工具。)

## 皮林拯救世界

如果你用 Haskell 编码，编译器会支持你。如果你用 Java 编程，编译器通常会助你一臂之力。但是如果你用 Python 或 Ruby 这样的动态语言编码，你就只能靠自己了:你没有编译器来帮你捕捉错误。

下一个最好的东西是 lint 工具，它使用启发式方法来捕捉代码中的 bug。Pylint 就是这样一个工具，下面是我开始使用它的方法。

有一天在工作中，我们意识到我们的构建已经连续失败了几天，而且不是通常的间歇性失败。经过几天的调查，我的同事汤姆·普林斯发现了这个问题。Python 代码是这样的:

```
for volume in get_volumes():
    do_something(volume)

for volme in get_other_volumes():
    do_something_else(volume) 
```

Enter fullscreen mode Exit fullscreen mode

注意第二个 for 循环中的拼写错误。结合 Python 从块中泄漏变量的事实，第一个 for 循环的最后一个 volume 值用于第二个循环的每次迭代。

为了看看我们是否能在将来防止这些问题，我尝试了 Pylint，重新引入了这个 bug...的确，它抓住了问题所在。然后，我查看输出的其余部分，看看还发现了什么。

它发现的是一个严重的错误。这是我几天前写的代码，这个 bug 完全破坏了我们第二天就要发布给用户的一个重要特性。这是一个大大简化的 bug 最小重现器:

```
list_of_printers = []
for i in [1, 2, 3]:
    def printer():
        print(i)
    list_of_printers.append(printer)

for func in list_of_printers:
    func() 
```

Enter fullscreen mode Exit fullscreen mode

这台复制机的预期效果是打印:

```
1
2
3 
```

Enter fullscreen mode Exit fullscreen mode

但是实际上打印出来的代码是:

```
3
3
3 
```

Enter fullscreen mode Exit fullscreen mode

当你在 Python 中定义一个引用外部作用域中的变量的嵌套函数时，它绑定的不是变量的*值*，而是变量本身。在这种情况下，这意味着 printer()内部的 I 最终总是获得 for 循环中变量 I 的最后一个值。

幸运是，Pylint 在发布之前发现了这个 bug 很棒，对吧？

## 为什么没人用 Pylint

Pylint 很有用，但是很多项目并不使用它。比如我刚才去查了一下，Twisted，Django，Flask，Sphinx 好像都没有用 Pylint。为什么这些大型复杂的 Python 项目不使用一种工具来自动捕捉 bug 呢？

一个问题是它很慢，但这不是真正的问题；您总是可以在 CI 系统上运行它和其他慢速测试。真正的问题是产量。

我的意思是:我在 Twisted 的 checkout 上运行了`pylint`,得到的输出是 28，000 行输出(此时`pylint`崩溃了，但我假设在新版本中已经修复了)。让我再说一遍:28，000 个错误或警告。

那是*可怕的*。

公平地说，Twisted 有一个与 Python 主流不匹配的编码标准，但是大量的噪声也是我在其他项目中的经验。Pylint 有很多有用的错误...而且还有一大堆关于你的代码应该是什么样子的毫无用处的垃圾假设。从根本上说，它对他们一视同仁。警告和错误是有区别的，但实际上有用和无用的东西都属于警告范畴。

例如:

> `W:675, 0: Class has no __init__ method (no-init)`

这不是一个有用的警告。现在想象一下其中的几千个。

## 应该如何使用 Pylint

因此，这里我们有一个潜在有用的工具，但在实践中无法使用。
该怎么办？幸运的是，Pylint 有一些功能可以提供帮助:您可以用 lint 检查的白名单来配置它。

首先，设置 Pylint 不做任何事情:

1.  从 [Pylint docs](https://pylint.readthedocs.io/en/latest/reference_guide/features.html) 中列出你想启用的所有特性，并配置`.pylintrc`将其列入白名单。
2.  把它们都注释掉。

此时，Pylint 将不做任何检查。接下来:

1.  取消小批量检查的注释，并运行`pylint`。
2.  如果产生的错误是真正的问题，修复它们。如果错误完全是垃圾，从配置中删除这些检查。

此时，您已经通过了少量可能有用的检查:您可以运行`pylint`，并且您只会被告知关于*新的*问题。换句话说，你有一个有用的工具。

重复这个过程几次，或者一周一次，每次启用一批新的检查，直到您失去耐心或者用完了要启用的 Pylint 检查。

最终结果会是类似于[这种配置](https://github.com/ClusterHQ/flocker/blob/master/.pylintrc)或者[这种配置](https://github.com/datawire/quark/blob/master/.pylintrc)；这两个项目在 Apache 2.0 许可下都是开源的，所以您可以将它们作为起点。

## 往前走，不掉毛

我对你的挑战是:如果你是一个 Python 程序员，今天就在一个项目上安装 Pylint。进行一些最小限度的检查需要一个小时，有一天它会把你从一个影响生产的错误中拯救出来。如果你不是 Python 程序员，你可能会找到一些与你的语言相当的工具；去安排一下。

如果你是 lint 工具的作者，请尝试使用更好的默认值。抓住 60%的 bug，让 10000 个软件项目使用你的工具，比抓住 70%的 bug，让几乎没有人使用它要好。

> 坏掉的软件，糟糕的工作机会，等等:避免我作为软件工程师 20 多年的错误。加入 2500 名其他程序员，每周学习如何避免新的错误。