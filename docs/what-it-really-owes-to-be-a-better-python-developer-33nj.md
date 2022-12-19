# 成为一名更好的 Python 开发人员的真正原因

> 原文：<https://dev.to/thisisrgaurav/what-it-really-owes-to-be-a-better-python-developer-33nj>

每当你学习一门新的编程语言时，在掌握了核心概念后，当你准备进入 Python 的高级和特定领域时，每个人都会想到这个问题。

最困难的事情是决定你什么时候应该继续学习 python，或者仍然需要练习这门语言的核心。核心并不包括理解语法和让你的程序运行并得到想要的输出。它涉及何时使用特定的数据结构，该数据结构的什么操作导致什么效果，以及如何用给定的数据结构或操作或内置库有效且高效地实现期望的输出。如果你能回答几个基本问题，那么你就准备好进入高级概念了。不要相信那些喜欢贬低的人，而不是激励你给予新的开始。

注意:这些问题只是一些概念的列表，根据我的经验，这些概念足以构建真正的东西。
问题清单

### Q1:Python 的可变数据结构和不可变数据结构有什么区别，Python 的哪个数据结构是可变的，哪个是不可变的？

你能回答第一个很酷的问题吗，现在也回答其他的。如果你能回答这些问题，那么你就准备好继续前进了，伙计…

### Q2:下面的输出是什么

```
>>> my_list = [1,4,5]
>>> my_new_list = my_list
>>> my_new_list[1] = 69
>>> print(my_list) 
```

我相信你也能回答这个问题，并且不会对结果感到惊讶。如果你不是，那么你需要花更多的时间练习。
Q3。当你能够理解列表理解的概念时。

考虑以下片段:

```
 >>> matrix = [[25,2,5],[23,421,42],[42,2,1],[0,32,6]]
>>> new_matrix = [[row[i] for rows in matrix] for i in range(3)]
>>> print (new_matrix) 
```

### Q4:什么时候使用 args 和 kwargs 使你的函数或方法灵活？

### Q5:为什么要使用 decorator，如何有效地编写 decorator？

### q6:@ static method 和@classmethod 有什么区别，什么时候用哪个函数？

虽然这两个装饰器看起来是一样的，但是有一个很大的区别，那就是当你需要输出那个类的对象或者必须在那个方法中处理那个类的对象时，使用 classmethod。

### 问题 7:在什么情况下应该使用发电机，以及如何有效地使用它们？

### Q8:你一定知道如何用正则表达式玩游戏吧？

因为在 python 的高级领域，你可能会在很多情况下使用它们，比如验证电话号码，验证模式。或者搜索由许多页面组成的某种数据。

### 问题 Python 省略号中最奇怪的事…(可选)

### Q10:先从模块导入，后从模块导入有什么区别

### Q11:这是你必须能够做的最重要的部分，即调试你的代码，以防任何错误或异常。

如果你能回答或完成所有问题，那么你就准备好进入 Python 的高级部分了，不需要担心忘记概念，让谷歌来做吧。那么，有了这么多足够的知识之后，下一步该做什么呢？你可以通过阅读 [![Why Python Programming?](../Images/587968c1a59d35e66cc57a773458e7bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zChUwp9E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev.to/thisisrgaurav/why-python-3o39) 来阅读 python 让你选择的各种字段

如果我遗漏了什么，你可以通过评论补充，或者你可以在 [![What it really owes to be a better Python Developer](../Images/9d2b07fb8294296491902d5ea79430b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Rokeci83--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tekraze.com/2018/08/what-it-owes-to-be-better-python-dev/) 阅读类似的帖子