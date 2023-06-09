# 命令行生产率提示:在终端中获得帮助

> 原文：<https://dev.to/rrampage/getting-help-in-the-terminal-4cmo>

对于初学者来说，命令行通常是一个令人望而生畏的地方。除了闪烁的光标和盯着你的不友好的黑屏，什么都没有，绝望开始了。

如果你被困住了，可以做以下几件事:

### 1。如何找到特定任务的程序

很多时候，你想做一个特定的任务，但是你不记得做这个任务的程序的名字。许多 shell 实用程序最初不容易记住，因为它们有两个字母的神秘名称。

不用担心，有一个叫 **`apropos`** 的命令帮你解决！

例如，您希望**显示一个行号为**的文件。您知道有一个程序，但忘记了它的名称。你只需输入`apropos -a line number`就可以得到程序列表。我们使用`-a`标志，这样`apropos`将只返回那些描述中带有 **`line`和`number`** 的程序。

在我的电脑上，它给出了以下输出:

```
apropos -a line number
# Output:
addr2line (1)        - convert addresses into file names and line numbers.
nl (1)               - number lines of files
x86_64-linux-gnu-addr2line (1) - convert addresses into file names and line numbers. 
```

Enter fullscreen mode Exit fullscreen mode

有 3 个程序与编号行有关。让我们来看看这些程序都做些什么。

### 2。找出一个程序做什么

让我们来看看我们的第一位候选人`addr2line`。**在命令后键入`--help`是发现程序做什么**的一种方式。当你用`--help`调用命令行程序时，它会打印一个小的帮助信息，这是一个惯例。有些程序还允许用`-h`进行呼叫。

```
addr2line --help
# Output:
Usage: addr2line [option(s)] [addr(s)]
 Convert addresses into line number/file name pairs.
 If no addresses are specified on the command line, they will be read from stdin
.... 
```

Enter fullscreen mode Exit fullscreen mode

好的。这个程序用`addresses`做了一些事情，而不是我们正在寻找的。继续我们列表中的下一个:`nl`

```
nl --help
# Output:
Usage: nl [OPTION]... [FILE]...
Write each FILE to standard output, with line numbers added.
... 
```

Enter fullscreen mode Exit fullscreen mode

看来我们找到我们的节目了。让我们试一试。这是我们的文件`foo.txt`

```
cat foo.txt
# Output:
one
two
three

four
five 
```

Enter fullscreen mode Exit fullscreen mode

```
nl foo.txt
# Output:
     1  one
     2  two
     3  three

     4  four
     5  five 
```

Enter fullscreen mode Exit fullscreen mode

它工作正常，但为什么它没有空行编号？

### 3。获取程序的详细信息

`--help`消息非常简洁。如果我们想详细了解一个程序做什么，我们需要查阅手册或`man`页。这些页面非常详细地记录了一个程序做什么，所有可能的选项和参数。键入`man nl`并查看信息...

哇哦。终端屏幕上满是信息！。您可以使用箭头键上下导航，并按`q`退出。

我们可以在输出中看到以下内容:

```
.
.

-b, --body-numbering=STYLE
              use STYLE for numbering body lines
.
.
STYLE is one of:

       a      number all lines

       t      number only nonempty lines

       n      number no lines 
```

Enter fullscreen mode Exit fullscreen mode

所以， **`nl -b a foo.txt`** 将编号**的所有**行放到 foo.txt 中这就是我们想要的。

```
nl -b a foo.txt
# Output:
     1  one
     2  two
     3  three
     4
     5  four
     6  five 
```

Enter fullscreen mode Exit fullscreen mode

这似乎解决了我们的问题。

还有一个`info`命令，它给出了更详细的使用信息。可以用 **`info nl`** 试试看。

### 4。获取使用程序的示例

但是，如果我们只是想快速让我们的命令工作，而不必阅读一大堆文本呢？

原来，你可以安装一个叫做 **`tldr`** 的实用程序。如果您使用`node`或`python`，您可以分别使用`npm install -g tldr`或`pip install tldr`进行安装。 [tldr 页面](https://tldr.sh/)列出了其他安装选项。安装后，只需在您的终端
中试用`tldr nl`

```
tldr nl
# Output:
nl A utility for numbering lines, either from a file, or from standard input.

  - Number non-blank lines in a file:
    nl file

....

  - Number all lines including blank lines:
    nl -b a file 
```

Enter fullscreen mode Exit fullscreen mode

那里！我们可以用通俗易懂的描述来看这个例子。

`tldr`是社区驱动！人们提供各种命令的例子。如果您喜欢的命令没有条目，您可以提交一个[拉请求](https://github.com/tldr-pages/tldr)。

### 重述

概括来说，我们可以:

*   使用 **`apropos`** 找出执行特定任务的可能程序
*   使用 **`$program --help`** 或 **`man $program`** 获取 **`$program`** 的帮助信息
*   使用 **`tldr $program`** 获取示例

### PS:如果我们不能为我们的任务找到程序怎么办

在第一节中，我们假设我们能够为我们的任务找到程序。但是很多时候，我们可能没有安装。在这种情况下，我们可以搜索我们发行版的软件包管理器，比如用`apt-get`搜索 Ubuntu，用`yum`或`dnf`搜索 CentOS / Fedora。

在 Ubuntu 中，我们可以使用`apt-cache search $KEYWORD`搜索存储库中所有可用的包。