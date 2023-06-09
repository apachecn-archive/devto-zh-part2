# 系统 Python 不适合你

> 原文：<https://dev.to/k4ml/system-python-is-not-for-you-e4g>

如果你已经做了一些 python 开发或者刚刚开始，你会得到的一个建议是永远不要乱用系统 python——你的发行版附带的 python 版本。例如在基于 Ubuntu 或 Debian 的系统上，python 是由`sudo apt-get install python`安装的。起初我并不真正理解这是什么意思，为什么你不能使用已经免费的东西？

这个问题的答案是因为 python 的那个版本实际上是指发行版本身。大多数发行版会在某些地方使用 python，也许是一些配置脚本。在 Ubuntu 中，很多他们自己的应用程序都是用 python 写的。如果我们不通过标准的软件包管理器升级一些库，有可能会破坏这些应用程序。这就是他们提供的 python 的用途。是他们自己用的，不是我们用的。一旦我意识到这一点，我有一种感觉，发行版应该把他们需要的 python 和用户使用的 python 分开发布。

~~出于开发目的，我总是运行`virtualenv $HOME`在我的主目录中设置 virtualenv 环境。在 Ubuntu 下，`$HOME/bin`在`$PATH`下，所以`which python`总是指向`/home/kamal/bin/python`而不是`/usr/bin/python`。这允许我自由地使用 easy_install，它会把它安装到我的`$HOME/lib/python2.5`而不是系统范围的 lib 目录。对于像开发 django 项目这样的特殊事情，我更喜欢使用 [Buildout](http://www.buildout.org/) 来进一步隔离环境。稍后会有更多关于 [Buildout](http://www.buildout.org/) 的内容。~~

要‘获得’你自己的 python，你可以使用提供多个 Python 解释器的工具，比如 Conda、Pyenv 或者 [Nix](https://ariya.io/2016/06/isolated-development-environment-using-nix) package manager。

有一个例外。如果你对你的操作系统自带的 Python 版本很满意，并且只关心你安装的包，`pip`有一个`--user`标志，把所有的包安装到你的`$HOME`目录中。只要确保你的`$PATH`中的`$HOME/.local/bin`和`$HOME/.local/lib/python3.x/site-packages`首先出现在`sys.path`中，你就拥有了一个与系统 python 隔离的环境。事实上，这是我目前的设置，因为除了系统 python 之外，我不需要太多花哨的 python。