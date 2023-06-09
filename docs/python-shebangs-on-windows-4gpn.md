# Windows 上的 Python Shebangs

> 原文：<https://dev.to/rpalo/python-shebangs-on-windows-4gpn>

*封面图片由 Sven[公共领域]提供，来自维基共享资源*

## 野中射棒线

在类似 Unix 的系统中，如果你希望一个文件是可执行的，你可以在顶部添加一行名为**“she bang”**的代码。他们看起来像这样:

```
#!/usr/bin/env bash

echo "Hello, world!" 
```

Enter fullscreen mode Exit fullscreen mode

“Shebang”是“hash bang”的缩写，是英镑/哈希/八位数符号(`#`)的俚语，后面是感叹号/bang ( `!`)。这一行负责告诉计算机将用于运行该文件的程序或命令位于何处。

一旦您的文件有了 shebang，您就可以通过添加“执行权限”来使文件可执行，这可以通过在 Bash shell 中运行以下命令来实现:

```
$ chmod u+x hello.sh 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以通过直接执行来运行程序:

```
$ ./hello.sh
Hello, world! 
```

Enter fullscreen mode Exit fullscreen mode

## 它如何在 Windows 上工作

如果您没有使用 Windows 子系统 Linux 或其他形式的 Bash 移植到 Windows，您可能会选择 PowerShell 作为您的 Shell。而且，我在 shebangs 做 Windows 的时候运气一直不太好。我觉得是因为 Windows 处理哪些程序处理哪些文件后缀的方式。然而，从 Python 3.3 开始，Python for Windows 附带了一个**“Python for Windows Launcher”**，从命令行简单地调用为`py`。

您可以通过不带参数运行 Python 来启动最新版本:

```
$  py 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过指定版本标志来选择您想要的版本。

```
$  py  -2.7 
```

Enter fullscreen mode Exit fullscreen mode

如果你找不到它或者命令不起作用，默认情况下启动程序存在于`C:\WINDOWS\py.exe`中。确保`C:\WINDOWS`在您的路径上，并且 Python 文件使用这个可执行文件作为它们的默认程序。

> 这个*真的*重要。如果您运行 Python 文件的默认程序被设置为特定的 Python 可执行文件，您将会遇到一些奇怪且难以诊断的问题。因此，为了确保设置正确，运行“默认程序”应用程序并确保 Python 文件与`C:\WINDOWS\py.exe`可执行文件相关联，而不是与其他文件相关联。

这个启动器的好处在于，如果它是运行 Python 文件的默认程序，它可以处理几种常见形式的 shebangs。

```
#!/usr/bin/env python
#!/usr/bin/python
#!/usr/local/bin/python
#!python 
```

Enter fullscreen mode Exit fullscreen mode

如果你希望让你的脚本具有可移植性，可以使用以`/usr`开头的脚本。如果`py`遇到这些情况，它将使用默认的 Python。如果您指定了一个版本(major 或 major.minor)，它将使用该版本:

```
#!/usr/bin/env python3
#!/usr/bin/env python2.7 
```

Enter fullscreen mode Exit fullscreen mode

此外，如果您使用这个`/usr/bin/env python`版本(而不是`/usr/bin/python`或`/usr/local/bin/python`)，那么`py`将会在您的`PATH`中进行额外的、预期的 python 命令搜索，就像在类 Unix 系统中一样。

在 Windows 上并没有真正的“执行权限”(如果我错了，请纠正我)，但是一旦在脚本的顶部有了这几行中的一行，您就可以像在 shell 中运行可执行文件一样运行它。

```
#!/usr/bin/env python3
# Inside hello.py 
print("Hi buddy!") 
```

Enter fullscreen mode Exit fullscreen mode

```
$  ./hello.py  Hi  buddy! 
```

Enter fullscreen mode Exit fullscreen mode

还有一件事可以让你在使用 Windows 时不那么想家。

在[Python 文档](https://docs.python.org/3/using/windows.html)中有更多关于在 Windows 上使用 Python 的信息。如果你在使用 windows，并且喜欢寻找让你的生活更简单的方法，我建议你看一看。

感谢阅读！

* * *

*原帖 [`assert_not magic?`](https://assertnotmagic.com/2018/07/01/windows-python-shebangs/)*