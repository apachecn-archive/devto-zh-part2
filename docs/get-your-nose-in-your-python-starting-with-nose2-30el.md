# 关注您的 python(从“nose2”开始)

> 原文：<https://dev.to/malikbenkirane/get-your-nose-in-your-python-starting-with-nose2-30el>

我需要用 python 构建一些测试。这通常可以通过编写一个有很多断言的
模块或者手工做所有的事情来完成，也就是输入一些

```
 python -i totest.py 
```

Enter fullscreen mode Exit fullscreen mode

然后摆弄代码，看看有没有代码
坏了的情况。但是你会同意为你的
代码设计好测试是更好的，例如为了自动化这个过程。这个过程在很久以前就已经被讨论过了，在这之后几乎没有留下什么东西
。此外，这个过程也被扩展到所谓的
持续集成、代码覆盖或者你可能听说过
通过的构建。没错，你总是可以在构建过程中进行测试。Pythonistas
宁愿用`nose2`。

# 安装

您可以使用任何环境(虚拟、用户、全局)来安装`nose2`

```
 pip[3] install [--user] --upgrade nose2 
```

Enter fullscreen mode Exit fullscreen mode

> 对于 Python 2.7 和 pypy，nose2 需要[六](http://pypi.python.org/pypi/six/1.1.0)1.1 版本。

# 这是怎么回事？

> 为了找到测试，`nose2`寻找名字以`test`开头的模块。在
> 这些模块中，`nose2`将加载所有`unittest.TestCase`
> 子类的测试，以及名字以`test`开头的函数。

基本上，你需要编写 python 测试并执行`nose2`来处理
你的测试工作。和

> `nose2`将在起始目录下的每个目录中查找，除非
> 配置修改了包含的路径。在目录中以及在起始目录中找到的任何
> Python 包中以及在
> 起始目录中的任何源目录中，`nose2`将发现测试模块并从
> 它们中加载测试。

就

```
 nose2 
```

Enter fullscreen mode Exit fullscreen mode

很棒吧？

# 命名测试

`nose2`望进去

*   包含`__init__.py`的目录
*   目录确认小写名称包含`test`
*   目录名称为`src`或`lib`

和(无配置)

*   `nose2`将运行所有名称以`test`开头的 python 文件(`.py`扩展名)
*   在测试模块中，`nose2`加载`unittest.TestCase`子类

# 接下来是什么？

*   转到 python `unittest`文档([此处 python3](https://docs.python.org/3/library/unittest.html) ，[此处 python 2](https://docs.python.org/2/library/unittest.html)
*   转到`nose2`(此处[RTD](https://nose2.readthedocs.io/en/latest/usage.html))
*   你会想出办法的

问候，