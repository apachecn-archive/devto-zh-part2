# 如何学习一门新的编程语言或框架

> 原文：<https://dev.to/vinibrsl/how-to-learn-a-new-programming-language-or-framework-1af7>

每天都有新的编程语言诞生。镖，去，科特林，仙丹和更多！超越学习曲线可能会很困难，但是有一些提示可以帮助你度过难关。

你需要采取一些步骤来加速学习过程，并提高你正在学习的编程语言或框架的流畅性。

# 掌握语言。然后学习框架。

有些人倾向于学习框架，比如 Ruby on Rails，而不了解这种语言，在这种情况下，就是 Ruby。这是一个很大的问题，特别是当谈到 Rails 时，它把 Ruby 当作 DSL(特定领域语言)来使用。

# 解决编码挑战。

有很多像 LeetCode、HackerRank 和 Project Euler 这样的网站可以帮助你达到流利程度。您将了解更多关于语法、库和逻辑的知识。这也是为面试做准备的好方法。

# 想用多少就用多少 StackOverflow 和代码评审。

当你开始解决编码挑战时，你会发现问题。要解决它们，你可以使用 StackOverflow，一个不需要介绍的网站。

王牌在 Code Review 上，一个 StackExchange 社区，来自 StackOverflow 的同一个网络。关于代码审查的全部要点是让你正在学习的语言的专家对你的代码进行审查。看看[初学者版块](https://codereview.stackexchange.com/questions/tagged/beginner)加入这个牛逼的社区吧！

最近我在那里要求一个代码审查，[如果你想看的话点击这里](https://codereview.stackexchange.com/questions/180567/checking-for-balanced-brackets-in-python)。

# 读取大量代码。

GitHub 对于战胜学习曲线至关重要。在那里你可以找到你正在学习的语言的产品代码。如果你有信心贡献，那就更好了。

# 安装一个林挺工具。

Linters 是代码分析工具，可以标记 bug、惯用错误、风格错误等等。一个林挺工具会标记出错误让你去修复，这就是你学习的时候。对于 Ruby，我使用 RuboCop 作为 linter。

# 惯用代码。

我在学习 Python 的时候，把 C#的知识转移到了 Python 上。这是学习一门新编程语言的好方法，但还不足以达到流利程度，因为你无法学习那门语言的惯用编程方式。

为了说明这一点，请看这段 Python 代码:

```
def matrix_of_floats(matrix_of_anything):
    n = len(matrix_of_anything)
    n_i = len(matrix_of_anything[0])
    new_matrix_of_floats = []
    for i in xrange(0, n):
        row = []
        for j in xrange(0, n_i):
            row.append(float(matrix_of_anything[i][j]))
        new_matrix_of_floats.append(row)
    return new_matrix_of_floats 
```

Enter fullscreen mode Exit fullscreen mode

相对于 Python 的方式(惯用 Python):

```
def matrix_of_floats(matrix_of_anything):
    return [[float(a_ij) for a_ij in a_i] 
            for a_i in matrix_of_anything] 
```

Enter fullscreen mode Exit fullscreen mode

要学习如何习惯性地编码，你必须阅读专业人士写的大量代码。

# 造东西！

这个是最好的。学习编程语言的主要目的是用它来构建一些东西。当我学习 Ruby 的时候，我用 Rails 框架建立了一个博客，一个 todo 应用，一个维基百科的克隆和一个类似烂番茄的网站。

建立一些东西将会发展你学习的方方面面。

# 不放弃。

超越学习曲线真的很难。你需要时间和注意力来达到目的。不要因为你可能比你想象的更近而放弃。如果你被卡住了，寻求帮助。外面有很多资源。

# 绝地提示:`while(true) => study()`

我认识一个程序员学习 C#二十多年。他还在探索 C#和。天天净。这个人是安东尼奥·曼尼埃罗，葡萄牙语 StackOverflow 最有影响力的成员。

事实是:我们永远学不会一门语言的所有东西，而这将让我们每天学得越来越多。

你可以擅长某样东西，成为其中的参考，但总会有更多东西需要学习。

[![](img/5098fd6dbf2ea156ce752cc8f48bdad5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BfjVLtfs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pmtw0yce1znjsksxkwmc.jpeg)

愿原力与你同在！留下你的评论，分享还有什么可以帮助你学习一门新的编程语言。