# 关于如何使用 pdb 调试 Python 应用程序的指南

> 原文：<https://dev.to/django_stars/debugging-python-applications-with-pdb-192j>

调试并不是一个新技巧——大多数开发人员都在工作中积极使用它。当然，每个人都有自己的调试方法，但是我见过太多的专家试图使用基本的东西，如打印，而不是实际的调试工具来找出错误。或者，即使他们使用了调试工具，他们也只使用了一小部分功能，而没有深入挖掘好的调试器所提供的大量机会。这可以为这些专家节省大量时间。

*使用打印命令*
调试 Python 如上所述，我们中的一些人使用“打印”来显示揭示代码内部发生了什么的信息。我们中的一些人出于同样的目的使用日志记录器——但是请不要将这与生产代码的日志记录器相混淆，因为我指的是开发人员只在问题搜索期间添加日志记录器，也就是直到开发过程结束。

但事实是，印刷品有很多漏洞。可能，它最大的缺点是你必须修改你的代码，然后重新运行应用程序来查看你写的不同的变量，或者当前的变量类型。那么调试器如何更好呢？

*Python 调试器命令*
本质上，调试器是一种工具，让你可以在某个地方打开应用程序，这样你就可以查看你的变量、调用堆栈或任何你需要查看的东西，设置条件断点，一行一行地遍历源代码，等等。这就像打开你汽车的引擎盖，看看烟是从哪里冒出来的，一部分一部分地搜索，找出问题所在。

如果您正在使用 Python，您不仅可以在调试期间浏览代码，还可以运行在命令行中编写的代码，甚至可以通过更改变量值来影响整个过程。

Python 有一个名为 pdb 的内置调试器。它是一个简单的工具，带有一个命令行界面来完成主要工作。它拥有您需要的所有调试器特性，但是如果您想稍微改进一下，您可以使用 ipdb 来扩展它，ipdb 将为调试器提供 IPython 的特性。

使用 pdb 最简单的方法是在您正在处理的代码中调用它。

导入 pdbpdb.set_trace()

一旦解释器到达这一行，您将在运行程序的终端上收到一个命令提示符。这是一个普通的 Python 提示符，但是添加了一些新命令。

**列表(l)**

list(l)命令将显示 Python 解释器当前所在的代码行。如果您想检查代码的不同区域，这个命令会显示第一行和最后一行的参数。如果您只提供第一行的编号，您将看到您给出的行周围的代码。

**向上(p)和向下(d)**

Up(p)和 down(d)是在调用堆栈中导航所需的两个命令。在这些命令的帮助下，你可以看到谁在调用当前的函数，或者解释器为什么这样做。

**步骤(s)和下一步(n)**

另一对重要的命令 step(s)和 next(n)帮助您逐行继续执行应用程序。这两者之间唯一的区别是 next(n)只会转到当前函数的下一行，即使它有对另一个函数的调用，但是 step(s)在这种情况下会更深入。

**break(b)**

break(b)命令允许您在不更改代码的情况下设置新的断点。这需要更多的解释，所以我将在下面详细说明。

*以下是其他 pdb 命令的简要概述:*
[![pdb commands](img/09fc33dc7850ae6a6ed2ce223f6e4dcb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2tRUe4Va--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://djangostars.com/blog/conteimg/2018/05/Img-1%402x-2-.png)

**Python 提前调试器**
以前，需要修改代码来打印一些东西或者设置断点。然而，通常您需要在第三方包中设置断点。当然，您可以随时从您的虚拟环境中打开库的源代码，并添加对 pdb 的调用。

但是现在您可以从调试器运行应用程序并设置断点，而无需对源代码进行任何更改。要使用调试器执行应用程序，请使用命令 python -m pdb。

让我给你看一个例子。我有一个简单的应用程序来跟踪我的工作时间。在其中，我使用 requests 库发出 HTTP 请求，并且我想中断一个 post 请求。我该怎么做？我用调试器运行我的应用程序，并在请求库中设置一个断点。

```
$ python -m pdb run.py
> /Users/.........................../run.py(1)<module>()
-> from TimeLog.app import run_app
(Pdb) b requests/sessions.py:555
Breakpoint 1 at /..................../lib/python3.6/site-packages/requests/sessions.py:555  
(Pdb) c 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，您不需要输入源文件的完整路径。您只需将任何文件夹的相对路径放入 sys.path 中，就像您将导入到代码中一样。

现在设置断点更容易了。没有必要把它放在你自己代码的某个地方，一步一步地找到你需要的函数，或者找到库源文件并修改它。

但是，如果应用程序执行了许多调用，而您需要一个特定的调用，该怎么办呢？简单。您可以指定断点条件，只有当该条件被视为真时，调试器才会中断应用程序。

在这个例子中，只有当 json 有 time_entry 键时，应用程序才会中断。

```
python -m pdb run.py  
> /Users/...../run.py(1)<module>()
-> from TimeLog.app import run_app
(Pdb) b requests/sessions.py:555, json is not None and 'time_entry' in json
Breakpoint 1 at /Users/....../lib/python3.6/site-packages/requests/sessions.py:555  
(Pdb) c 
```

Enter fullscreen mode Exit fullscreen mode

Django 的调试助手
如果你正在使用 Django web 框架，你可能知道如果在你的设置中将 Debug 设置为 True，那么在任何一个异常上，你都会看到一个特殊的页面，上面有异常类型和消息、回溯、局部变量等信息。

如果您想像这样增强您的调试页面，请安装 django-extensions 并使用 runserver_plus 命令启动您的 django 服务器进行开发。如果你需要设置一个调试器 pin，这里有一个你如何做的例子:

```
WERKZEUG_DEBUG_PIN=1234 ./manage.py runserver_plus 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用 django-extensions 而不是这个默认页面，您将得到一个回溯页面，在这里您可以看到每一行的代码和一个打开的调试器。

调试过程是在 Werkzeug 项目的帮助下进行的，它是 Python 的一个 WSGI 库。

如您所见，使用调试器的方式有很多种。这并不太复杂，但与此同时，它会给你带来很多好处——你不仅会赢得一个优秀开发人员的名声，还会为自己节省大量时间和精力，并学会如何在未来避免不同的错误。当然，如果您使用合适的调试器，产品和您的客户都会受益。所以，这对每个人来说都是双赢的。

作者:VADYM ZAKOVINKO
后端工程师/解决方案架构师

这篇关于调试 python 应用程序的文章最初发布在 Django Stars 博客上。
您还可以访问我们的内容平台[产品部落](https://producttribe.com/)，它是由专业人士为那些参与产品开发和成长过程的人创建的。