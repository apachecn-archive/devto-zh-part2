# 你好 flake8

> 原文：<https://dev.to/dmerejkowsky/hello-flake8-5d40>

*最初发表于[我的博客](https://dmerej.info/blog/post/hello-flake8/)。*

# 简介

在我的上一篇文章中，我解释了为什么我决定不再使用 pylint。

简而言之，大多数 pylint 警告现在都被其他 linters 捕获了，剩下就是一些警告。

在另一篇[文章](https://dmerej.info/blog/post/how-i-lint/#putting-it-all-together)中，我简单地提到了 [flake8](http://flake8.pycqa.org/en/latest/) ，说我更喜欢一个简单的 bash 脚本来驱动各种 linters 的执行。

我担心 flake8 不包括 pylint(当然现在我不再关心了)。此外，我不喜欢 flake8 为它运行的所有 linters 强制一个特定的版本。

唉，我(又)错了。

# flake8 牛逼

首先，我认为 flake8 只结合了 pyflakes 和 pycodestyle 棉绒，没有其他的 <sup id="fnref1">[1](#fn1)</sup> 。嗯，默认情况下也包括[麦凯布](https://pypi.org/project/mccabe/)。

此外，正如在 [FAQ](http://flake8.pycqa.org/en/latest/faq.html#why-does-flake8-use-ranges-for-its-dependencies) 中所解释的，以这种方式冻结棉绒的版本有显著的优势。

以下是我忽略的一些其他功能:

*   您可以使用`setup.cfg`文件来配置 flake8 知道的所有 linters
*   所有棉绒的输出是一致的
*   只需一个`# noqa`注释，就可以在任何一行上禁用对任何 linter 的检查

# 意外之喜

上次升级 pylint 时，我只收到一个新警告。这是这样的一行:

```
my_set = set([elem for elem in my_list if some_condition(elem)]) 
```

Enter fullscreen mode Exit fullscreen mode

这里的目的是从满足给定条件的元素列表中构建一个唯一的集合。

pylint 发出以下警告:

```
R1718: Consider using a set comprehension (consider-using-set-comprehension) 
```

Enter fullscreen mode Exit fullscreen mode

的确，代码也可以这样写，用一个*集合理解*代替:

```
my_set = {elem for elem in my_list if some_condition(elem)} 
```

Enter fullscreen mode Exit fullscreen mode

优势:

*   代码更短
*   我们不再构建一个列表(在方括号内)只是为了在之后立即抛出它
*   解释器不必查找`set()`函数，所以代码更快

嗯，已经有一个名为 [flake8-comprehension](https://pypi.org/project/flake8-comprehensions/) 的 flake8 插件处理这类问题。

其实，[有一堆 flake8 插件](https://pypi.org/search/?q=flake8-)可用！

另外，添加一个新的 flake8 插件就像运行`pipenv install --dev <plugin name>`一样简单，其他什么都不用改变:)

# 前途一片光明

it Amar Turner-Trauring<sup id="fnref2">[2](#fn2)</sup>，在 dev.to 上的[评论区](https://dev.to/codewithoutrules/comment/4ib7)举了一个有趣的例子:

```
# Note: I've taken the liberty of making the code a bit less abstract def greet(prefix, name):
    print(prefix, name)

greeters = list()
prefixes = ["Hi", "Hello", "Howdy"]
for prefix in prefixes:
    greeters.append(lambda x: greet(prefix, x))

for greeter in greeters:
    greeter("world") 
```

Enter fullscreen mode Exit fullscreen mode

您可能认为下面的代码会打印出来:

```
Hi world
Hello world
Howdy world 
```

Enter fullscreen mode Exit fullscreen mode

而是打印:

```
Howdy world
Howdy world
Howdy world 
```

Enter fullscreen mode Exit fullscreen mode

这与 Python 中闭包的工作方式有关，这个 bug 确实被 pylint:
捕捉到了

```
$ pylint example.py
 W0640: Cell variable `prefix` defined in loop (cell-var-from-loop) 
```

Enter fullscreen mode Exit fullscreen mode

我没有找到一个 flake8 插件可以立即捕获这个 bug，但我找到了 [flake8-bugbear](https://github.com/PyCQA/flake8-bugbear) ，一个“找到可能的 bug 和设计问题”的插件。

这个插件写得很好，经过了很好的测试，并且易于贡献。

我已经尝试过将一些 pylint 警告移植到 flake8-bugbear 中，到目前为止，这比我想象的要容易得多。<sup id="fnref3">T33</sup>

因此，下次我发现一个可能通过检查 AST 被捕获的 bug(这是 flake8-bugbear 和 pylint 都做的)，我知道如何编写或贡献一个 flake8 插件，以便在 CI 期间自动捕获它。

因此，我可以慢慢地为 pylint 构建一个完整的替代品，只有我关心的警告，没有配置问题和误报。

确实前途光明！

*感谢您阅读到目前为止:)*

我很想听听你有什么要说的，所以请随时在下面留下你的评论，或者查看我的联系页面了解更多与我联系的方式。

* * *

1.  这个项目的名字有些误导。 [↩](#fnref1)

2.  如果你还没有，你一定要看看他的博客，订阅 T2 软件小丑的邮件列表。 [↩](#fnref2)

3.  请注意，拉请求实际上没有被合并。实际上，在查看 flake8 插件的完整列表之前，我先盲目地在 flake8 *中添加了一个新的检查。pull 请求被关闭，因为我添加的检查已经在`flake8-comprehensions`插件中。有时候你不得不撒谎来编造一个更好的故事……[↩](#fnref3)*