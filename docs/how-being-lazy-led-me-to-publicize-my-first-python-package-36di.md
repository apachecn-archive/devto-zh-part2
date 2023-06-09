# 如何懒惰，导致我宣传我的第一个 python 包

> 原文：<https://dev.to/perigk/how-being-lazy-led-me-to-publicize-my-first-python-package-36di>

在软件行业，有一句谚语是这样说的:

> 我想雇用一个懒惰的开发人员，因为他会找到以最短和最简单的方式处理困难问题的方法。

第一次听到这个消息时，我不在现场，但有传言说这些话是比尔·盖茨的。

我不知道盖茨先生是否会雇用我，但我不得不承认，这种懒惰让我时不时地创建有趣且节省时间的库/脚本/工作区。

[![](img/2f42d676ad0c1ca889c24dfa46b69758.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iSdAnTjw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/638/1%2AnMTVWvIzsKtSC2qntRAlqA.jpeg) 

<figcaption>感谢拉里:)</figcaption>

你可以看到我的简介，我是 python 的忠实粉丝。我喜欢这种语言提供的优雅、强大和可扩展性的完美结合。不过，我注意到，在我读过的相当多的文章中，有一种样板代码模式，是用那种语言写的。

这种模式是，当进入一个函数时，1)我们记录这个事件，通常是为了调试的目的，2)很多时候我们需要处理一个潜在的异常，有时是以一种简单的方式，如下所示。

```
import os

try:

os.remove(a_file)

except:

print(‘exception raised’) 
```

和这个美丽星球上所有的*‘懒惰’*程序员一样，我不喜欢样板代码。

我就是这么逼自己的(是不是懒人在逼自己？)找一个*方便的*方案。顺便说一下，它也有一个懒惰的名字: **catslog，**因为几只猫激发了许多懒惰的模因。

#### 技术性的东西

解决方案涉及装饰者的奇妙特性。我太喜欢它了，以至于我不看电影，而是用 decorators(和各种其他 python 特性)阅读代码

[![](img/615d3beabba0ee90ff46e750e49974d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HxZUcUlb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/430/1%2A9dgT7Mg-YLMGW7b1MpIrAA.jpeg) 

<figcaption>对不起，那是一个很糟糕的笑话</figcaption>

更严肃地说，我不会在这里深入讨论 decorators，因为已经有大量优秀的文章，但是如果您不熟悉，我强烈推荐您阅读这些文章。喜欢这个经典的[入门](http://simeonfranklin.com/blog/2012/jul/1/python-decorators-in-12-steps/)。

但是如果你…懒惰(没有双关语的意思)并且不想在这上面花费时间，知道以下就足够了:

> decorator，是一个函数，它扩展了另一个函数(姑且称之为 foo)，用扩展的函数替换原来的 foo 引用。

有点像下面的例子:

```
def foo():
 # Various operations going on

foo = my_decorator(foo) 
```

回到我的包，核心逻辑可以看到下面:

```
from functools import wraps

def catslog(f):
 """Given a function f, run it, log the execution and
 handle any potential exceptions"""
[@wraps](http://twitter.com/wraps)(f)
 def wrapped(*args, **kwargs):
 """The extended version of the function f"""
 try:
 func_name = f.__name__
 print('Executing function {} with args: {} and kwargs: {}'
 .format(func_name, args, kwargs))
 f(*args, **kwargs)
 except Exception as e:
 print("""An error occurred while executing function "{}".
 The error is {}""".format(func_name, str(e)))
 return wrapped 
```

仅此而已。一个真正懒惰的解决方案，为了克服我的懒惰。

长话短说，我们创建了一个函数，这个函数创建了另一个函数，这个函数扩展了我们的原始函数(decorators 在一行中，如上所述)。

该代码可用作:

```
# First import the package
from catslog import catslog

# Then, decorate a target function
@catslog
def cats_me_if_you_can(*args, **kwargs):
 # Moar code 
```

#### 你的生活中没有其他事要做吗，佩里克莱斯？

但你会问，为什么要发表呢？首先，懒的因素(又来了？# oh god why)；我可以毫不费力地将其用于我可能工作的任何开发环境。当然，回报一个如此乐于助人、支持他人且充满顶尖工程师的社区是非常值得的。

#### 这就是所有的乡亲

感谢你到达这篇文章最黑暗的深处。我希望您喜欢阅读它，就像我喜欢创建软件包和撰写本文的过程一样。

我很确定你有一个节省时间的代码片段，你很可能会复制粘贴，这是值得分享的，我强烈鼓励你公布它，即使它不是用 python 写的。

关于该项目的更多细节可以在该项目的 github [页面](https://github.com/PeriGK/catslog)找到。一如既往，欢迎在评论区提问。