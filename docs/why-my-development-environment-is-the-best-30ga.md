# 为什么我的开发环境是最好的

> 原文：<https://dev.to/grahamlyons/why-my-development-environment-is-the-best-30ga>

# 为什么我的开发环境最好

或者更准确地说，现在什么对我有效。

作为软件开发人员，我们每天花很多时间在键盘上打字。很自然，我们会花时间让工作环境变得愉快而富有成效。

本地开发环境通常是非常个性化的，并且根据单个开发人员的口味进行调整和定制。编辑器和 ide 的数量是有限的，但是插件、主题和定制的组合几乎是无限的。在我当地环境的一些最近的演变中，我反抗了个性化的文化。

## 我的工具

我写的几乎所有软件都在运行某种 Linux 风格的服务器上部署和运行。我在一个[虚拟机](https://en.wikipedia.org/wiki/Virtual_machine)上本地运行所有东西，这个虚拟机被设置得尽可能接近生产。多年来，这对于在 bug 进入暂存环境之前就将其捕获，或者在安全的条件下重现生产问题，都具有不可估量的价值。

为了管理和运行虚拟机，我使用了 [VirtualBox](https://www.virtualbox.org/) 和[流浪者](https://www.vagrantup.com/)的组合。一旦我安装了这些，我就(几乎)准备好运行一个虚拟机了。我还使用了[vagger-VB Guest 插件](https://github.com/dotless-de/vagrant-vbguest)，它管理虚拟机中 VirtualBox Guest 插件的安装。这些用于在我的主机和 Linux 虚拟机之间共享一个工作空间目录:`vagrant plugin install vagrant-vbguest`

由流浪者管理的虚拟机的配置可以存储为代码，所以这里有一个我目前使用的主环境 GitHub 上的例子:[https://github.com/grahamlyons/centos-dev](https://github.com/grahamlyons/centos-dev)

要运行它:克隆存储库，启动虚拟机，然后连接到它:

```
git clone git@github.com:grahamlyons/centos-dev.git
cd centos-dev
vagrant up && vagrant ssh -c 'tmux attach || tmux' 
```

Enter fullscreen mode Exit fullscreen mode

`Vagrantfile`中的指令从 CentOS 7 基本图像开始，并且:

*   指定可用于引用虚拟机的固定 IP 地址
*   在虚拟机中挂载一个本地`~/workspace/`目录(在相同的路径下)
*   安装一些基础包，如`tmux`、`vim`、`git`等。
*   安装和设置 Docker
*   将一些本地配置和凭证文件复制到虚拟机中

SSH 连接命令还会让我进入一个现有的 [tmux](https://en.wikipedia.org/wiki/Tmux) 会话，或者启动一个新的会话。Tmux 是一个很好的工具，可以在一个 SSH 会话中创建多个选项卡和窗格。除了随软件包安装在 CentOS 上的配置之外，我没有为它使用任何额外的配置。

### 虚拟机的好处

当我第一次被介绍在虚拟机中工作时，我完全被吸引住了。尽可能接近生产对我来说意义重大，使用合适的包管理器在 Linux 上安装软件比在 OSX(或 Windows——在我的记忆深处)好得多。

在我的例子中，有了共享文件夹，我可以在我的主机操作系统上使用任何我喜欢的编辑器，更改将一直在虚拟机中，随时可以运行。

运行 VM 还不止一次地让我避免了完全毁坏我的机器，最糟糕的一次是意外地以`root`的身份运行`rm -rf /`。总是拥有一台可以用来寻求帮助解决问题的工作机器是非常有用的。如果事情真的回来了，你可以摧毁它，从你已知的良好状态重新开始。

### 虚拟机的弊端

使用虚拟机并不是一个完美的解决方案，在主机和客户虚拟机之间共享的目录中运行代码会带来性能问题。共享目录对于简单的编辑器非常有用，但是对于想要为你运行代码的 IDE 来说，在虚拟机内部运行代码可能很复杂或者不可能。

## 我的编辑

当我开始专业工作时，我使用的第一个编辑器是 [Homesite](https://en.wikipedia.org/wiki/Macromedia_HomeSite) ，它泄露了我的年份。在我再也找不到它之后，我四处寻找其他的东西，看到了一个叫做 [Vim](https://www.vim.org/) 的推荐。我很感兴趣，下载并打开了它。几分钟后，我设法找到了如何戒掉它的方法，并且在一两年内没有再打开它。

### Vim

在投入 Vim 之后，我现在几乎只使用它。在我使用其他东西的地方，我试图为它找到一个 Vim 键绑定插件。Vim 有一个很大的学习曲线，`vimtutor`是一个很大的帮助，但现在我已经熟悉了移动和动作，没有什么能让我更快地操纵文本。

### 插件和配置

我的`.vimrc`文件已经经历了多次迭代，现在大约有 10 行:

```
set expandtab
set shiftwidth=4
set tabstop=4

set modeline
set modelines=5

set nu
set colorcolumn=80

syntax enable

let g:netrw_liststyle=3
if exists("*netrw_gitignore#Hide")
    let g:netrw_list_hide=netrw_gitignore#Hide()
endif 
```

Enter fullscreen mode Exit fullscreen mode

我用空格代替制表符(谁不会呢？);(对于 OSX，它是关闭的)它被设置为从文件顶部读取 Vim 设置([http://vim.wikia.com/wiki/Modeline_magic](http://vim.wikia.com/wiki/Modeline_magic))；行号和语法突出显示已打开；有一个 80 个字符的列来防止我的行变得太长，我已经将目录列表设置为看起来像一棵树。

我在 Vim 中使用的其他东西都是香草味的。仅仅是习惯默认设置就能让我更容易地在不同的机器之间切换，而且我不需要记住太多巧妙的定制和调整，也不需要参考更广泛可用的文档。

## 其他软件

在过去的几年里，我开始在 Docker 容器中运行几乎所有的东西，所以我做的越来越少。几乎所有的东西都可以从 Docker Hub 的映像中获得，一旦它被拉下来，启动起来是如此之快，以至于它变得非常有意义。并排运行 Node、Ruby 或 Python 等不同版本要简单得多。

我仍然使用`yum`来安装像`telnet`或`jq`这样的实用程序包，它们通常会在`Vagrantfile`中进行配置。

## 这个对我来说暂时有效

这就是我的机器目前的设置，它对我来说非常有效。我运行 OSX，大部分时间都在终端上，只有一个标签用于我的虚拟机连接。我在 OSX 和虚拟机上都使用 Vim，对 Git 也是如此。

它工作得很好，但我总是在做改变。Docker 的引入是最近的事，并且变得越来越突出。让我们看看一年后会是什么样子。