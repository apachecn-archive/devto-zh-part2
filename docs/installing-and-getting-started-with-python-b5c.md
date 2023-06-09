# Python 安装和入门

> 原文：<https://dev.to/errietta/installing-and-getting-started-with-python-b5c>

我喜欢尝试和学习新事物。我以前从来没有研究过 Python，因为它的语法让我感到厌烦，我的语言背景是类似 C 的语法。然而，我最终说服自己至少尝试一下，并且已经开始开发一个可以部署到 AWS 的简单应用程序。

当然，使用任何新工具的第一步都是进行设置，有时并不像预期的那样简单。一开始我遇到了一点麻烦，所以我想我应该把我的经验分享给那些想要快速入门的人。

## Pyenv

首先我想说的是，我会强烈建议使用 Pyenv。我总是建议使用编程语言的版本管理器，因为它们不仅允许您安装多个版本并能够在它们之间切换，而且它们还将模块安装的默认路径更改为您的用户目录，这意味着您不需要使用`sudo`来安装依赖项——这对我来说是一个很大的优势。

### 依赖关系

安装 pyenv 的第一步是安装用于构建 python 的 dependnencies。这些在你的操作系统上有所不同，但是在[Pyenv github](https://github.com/pyenv/pyenv/wiki/common-build-problems)上有一个指南。

在我的例子中，我使用 Ubuntu，所以我必须运行下面的命令:

```
sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
xz-utils tk-dev libffi-dev 
```

Enter fullscreen mode Exit fullscreen mode

### 安装

一旦解决了这个问题，就该安装 Pyenv 本身了。再一次，按照你的操作系统的 pyenv 的 github 页面上的说明。

我使用了自动安装程序，这是最简单的方法，但是除了上面的依赖项之外，它还需要安装`git`。

安装完成后，您需要将以下内容添加到您的`~/.bash_profile`或`~/.bashrc`或同等文件中，并重新启动您的终端会话:

```
# Load pyenv automatically by adding
# the following to ~/.bash_profile:

export PATH="/home/errietta/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)" 
```

Enter fullscreen mode Exit fullscreen mode

如果成功了，运行`pyenv`会显示一个帮助界面！

## 用 pyenv 安装 Python

现在您已经有了 pyenv，您可以轻松地安装一个或多个版本的 Python。因为我想在无服务器的情况下使用它，所以我需要获得 2.7 版本。*或 3.6。*.

运行`pyenv install 2.7.8`或`pyenv install 3.6.6`

如果一切顺利，应该只需要 5-10 分钟。如果没有，输出应该说明问题是什么-[github 上的常见构建问题](https://github.com/pyenv/pyenv/wiki/common-build-problems)页面有一些更多的信息来修复问题，但一般来说，如果您已经安装了所有的依赖项，它应该就可以工作了。

它应该说“已将 Python-3.6.6 安装到/home/errietta/。pyenv/versions/3.6.6 "完成后。

## 你好世界

现在你可以编写你的第一个 python 代码了。首先，在你的项目的目录里面，你应该运行`pyenv local 3.6.6`(或者你安装的任何一个版本)，这样 pyenv 就知道你的项目使用哪个版本的 python。

现在你可以创建你的代码文件，比如说`hello.py` :

```
print("Hello world") 
```

Enter fullscreen mode Exit fullscreen mode

并运行它:

```
python hello.py 
```

Enter fullscreen mode Exit fullscreen mode

恭喜，成功了！

## 模块

考虑下面的树:

```
.
|-- hello.py
`-- util
    `-- math.py 
```

Enter fullscreen mode Exit fullscreen mode

以及每个文件中的以下代码:

hello.py:

```
from util.math import add

print("Hello world")
print(add(2, 3)) 
```

Enter fullscreen mode Exit fullscreen mode

util/math.py

```
def add(a,b):
  return a+b 
```

Enter fullscreen mode Exit fullscreen mode

这可能行得通，但是如果你用【lint 你的代码(或者如果你的 IDE 为你做了这些——Hi VS code！)，您会注意到它会抱怨:

`hello.py:1:0: E0611: No name 'math' in module 'util' (no-name-in-module)`

在这种情况下，你需要做的是创建一个没有内容的`__init__.py`。这告诉 python 您的目录包含 python 模块。它还可以执行初始化代码，但是在这种情况下，您可以将它留空。

这应该会让 pylint 和/或您的 IDE 感到高兴:)

## 下一步

*   [Python 教程](https://docs.python.org/3/tutorial/)
*   [AWS &使用 Python 的无服务器](https://dev.to/dangolant/creating-serverless-functions-with-python-and-aws-lambda-dli)