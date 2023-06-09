# 为什么要关心软件的可维护性？

> 原文：<https://dev.to/vmesel/why-should-you-care-about-software-maintainability-9e>

快速:这篇文章主要是写给那些还不是软件开发人员，或者不把编程作为主要收入来源的人。

嘿，伙计们，你们可能都知道(对于那些不知道的人，我将在这里很快解释一下)我换了工作，现在，我在巴西的一家银行工作(我不会在这里分享它的名字，因为我没有为他们做广告的报酬)。在那里，编码是我职业生涯中的副业，我主要在 Excel (VBA)和其他微软应用程序中进行投资分析。~~投资的东西很好，但是 Excel 很烂。~~

正如我在那里看到的，许多来自投资银行的人正在使用编程语言来比以前更快更好地完成他们的工作，这很好，但问题是:**那些由非软件工程师产生的代码在生产场景中可维护吗？**

关心自己代码质量的软件工程师，让自己的代码思考:错误处理、数据 IO、测试驱动开发、代码可读性、代码复杂度等很多方面，初学者和非专家人士。

## 为什么我要关心软件的可维护性？

如果你的代码只被你一个人用于某项任务，你不应该太在意这些，你的代码主要需要解决你的问题，但是当你在同一个项目/领域/区域/部门/无论什么地方与多人一起工作时，你不会是唯一一个会使用你所编码的东西的人。

出于可维护性的目的，Python 创建了两个 pep，应该作为软件工程的良好实践来遵循。这些 pep 是:

*   [PEP 8 -说明用户应该如何编写代码(主要是风格指南)](https://www.python.org/dev/peps/pep-0008/)
*   [PEP 20 -导入本；或者主要被称为 Python 的 Zen(下面这个小文本)](https://www.python.org/dev/peps/pep-0020/)

```
>>> import this
The Zen of Python, by Tim Peters

Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those! 
```

如上所示，在开发将要使用的新软件时，简单性和可读性很重要。

想象一下，如果你进入一家公司，没有关于代码的文档，没有部署说明，没有开发设置，只有代码。你能捡起它并在别人留下的地方继续工作吗？可能不会，我们都需要更好的代码维护和新系统开发人员的最佳实践！维护一个没有良好实践的大型代码库就像这个迷因:

[![Image result for everything is fine dog meme](img/559ebd052d864b4fa690ee8d1374ab91.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TxYrYi9a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.geek.com/wp-content/uploads/2016/08/this-is-fine-meme-625x350.jpg)

最佳编码实践是团队中所有开发人员都知道并应用的实践，也可以传递给新开发人员。以下是一些可能有用的最佳实践的使用案例:

*   如果写得好的注释(或文档字符串)对你的代码库有帮助，并指导新开发人员进行维护，保留它们并更新那些需要更新的。不要保留枯燥的评论，比如:

```
 # Example of a dull comment

a = 1 + 2 # this line adds 1 with 2 and saves to

# Example of a needed comment

def really_cool_function_that_sums_to_numbers(x, y):
    # This function sums X and Y and returns the result 
```

如果你需要注释多行代码以使其易于理解，这是考虑如何改进的好时机。代码应该易读易懂(PEP 20 课程)

*   函数应该只做一个功能(听起来有些多余，对吗？)就像纯函数一样(来自函数式编程的概念，陈述不会对全局变量和其他函数造成副作用的函数)。

根据您选择的语言，有大量的最佳实践和代码样式指南可以遵循。这篇文章的要点是:记住在不久的将来，其他开发人员会维护你的代码，你可能也会维护它，但不会记住很多东西。让你的代码简单，这样人们就能理解并继续你的工作。

最初发布于: [Vmesel 的博客](http://www.vmesel.com/2018/10/22/care-with-software-maintainability/)