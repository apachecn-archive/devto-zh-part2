# Gopei 解释道

> 原文：<https://dev.to/geosoft1/gopei-explained-3p7g>

基本上安装 Go 工作环境就是从[https://golang.org](//golang.org)下载编译器，用 PATH 和 GOPATH(这里很多人卡住了)做一些事情，安装一个 IDE。有很多教程，但让我们看看一个智能工具，可以使安装更容易。

我们谈论 Unix/Linux 世界，所以确保你有一台安装了`bash`、`curl`和`git`的 Mac 或 Linux 发行版。如果你还没有 github.com 的[账户，那就给自己建一个吧。我通常在 Ubuntu 上使用它，但 Gopei 应该可以在任何有`bash`的机器上工作。](https://github.com)

我们将使用 [Gopei (Go 编程环境安装程序)](https://github.com/geosoft1/tools) toolbox，它是整个 Go 环境的安装程序和定制程序，为新用户带来更好的体验。Gopei 安装 Go 编译器，LiteIDE(一个为 Go 语言设计的非常精简快速的 IDE)设置路径，GOPATH 和 link 都在一起。此外，还附带了一些用于 github 和部署在云端的工具。

Gopei 选项在 project [wiki](https://github.com/geosoft1/tools/wiki) 页面上有很好的记录。

首先从本地`Downloads`文件夹的[这里](https://github.com/geosoft1/tools/archive/master.zip)下载 Gopei 工具箱，解压后执行:

```
Downloads/tools-master/gopei -h

Golang Programming Environment Installer
Usage: gopei [options]
Options:
-c        enable classroom mode (Linux only)
-g        enable git suppport
-h        show this help message and exit
-k        show key fingerprint
-s        server mode, install golang only
-u        uninstall
-U        uninstall including .gitconfig file and .ssh folder
-q [name] set theme (eg. -q webstorm)
-x [name] set color scheme (eg. -x sublime)
-v        version 
```

Enter fullscreen mode Exit fullscreen mode

基本上，您需要进入下载文件夹(但不是强制的)并运行:

```
./gopei 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
./gopei -g 
```

Enter fullscreen mode Exit fullscreen mode

`-g`是给 github 的。如果您想使用 github 存储库，请使用它。

在一些系统上安装之后，你会发现一个带有一个漂亮的 gopher for IDE 的启动图标。在 Gnome 系统上，你需要在 dash 中搜索启动器。在 Mac 上，你可以在 dock bar 上找到最初的 IDE 图标。

如果您想使用其他 IDE(如 VSCode、Atom、Sublime 或其他),请考虑使用以下命令:

```
./gopei -sg 
```

Enter fullscreen mode Exit fullscreen mode

`-s`用于服务器模式(无默认 IDE)。现在你可以安装你最喜欢的 IDE 和 Go 编译器，而且 Gopei 工具箱(像[云](https://github.com/geosoft1/tools/wiki/Cloud-tool)工具)也可以从你的 IDE 或者终端获得。

另一个有趣的模式，对学习的人有用，就是课堂模式。使用`-c`标志关闭后，您在 IDE 中所做的任何设置都将丢失。

假设现在你有很多安装，你想在 github 键列表中找到你的本地键。这是一个困难的工作，因为 github 不允许你给每个键一个名字。因此，您可以使用下面的命令来查看本地机器的 ssh 密钥指纹。

```
./gopei -k 
```

Enter fullscreen mode Exit fullscreen mode

正如您已经看到的，您有两个卸载选项。最主要的区别是`-U`也删除了`.ssh`文件夹。如果你只想升级工具，用`-u`卸载。

Gopei 版本 2 中添加的另一个新的好选项是关于为大屏幕设计的主题设置。使用`-q`和`-x`标志设置安装主题。

```
./gopei -g -q webstorm -x sublime 
```

Enter fullscreen mode Exit fullscreen mode

希望你发现这个工具有用。享受吧。