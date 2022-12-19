# 在 Ubuntu 18.04 上将 Oni 配置为 C / C++ IDE

> 原文：<https://dev.to/preciouschicken/configuring-oni-as-a-c-c-ide-on-ubuntu-18-04-437j>

更新日期:12 月 19 日:之前 [Oni](https://www.onivim.io/) 是可以免费下载的，从那以后项目方向似乎有了变化。该 IDE 现在被称为 Onivim 2，并且有预购费。不知道你是否还能安装我下面用的版本，但是这篇文章在其他环境下可能会有用。

* * *

K 的 C 编程语言第二版并不容易读懂。为了驯服它，我想我可以用 [Oni](https://www.onivim.io/) 来做一些繁重的工作，这是一个用 [Neovim](https://neovim.io/) 作为后端的 IDE。它看起来很漂亮，而且是开箱即用的。然而，我花了一年的时间才做到林挺得了 C，所以下面是我的挣扎:

* * *

### 1。**安装 Oni** 。

所以这很简单。前往他们的[下载](https://www.onivim.io/Download)页面，通过 Ubuntu 软件安装 deb 包。这很容易，但是打开应用程序会得到一个错误:“啊哦！无法启动 Neovim…运行 Oni 需要 Neovim v0.2.1。

### 2。**安装 Neovim** 。

有很多方法可以做到这一点，默认的方法是安装一个 appimage。我对 appimages 犹豫不决；这是一个好主意，但不完全确定如何更新它们。相反，在[下载](https://github.com/neovim/neovim/wiki/Installing-Neovim)页面的其他地方有一个 Ubuntu 个人包存档(PPA)。不幸的是，在撰写本文时，[稳定版](https://launchpad.net/~neovim-ppa/+archive/ubuntu/stable) PPA 还不是当前版本，而[不稳定版](https://launchpad.net/~neovim-ppa/+archive/ubuntu/unstable)版本[破解了 Oni](https://github.com/onivim/oni/issues/2580) 。要安装 neovim 的稳定版本:

`sudo add-apt-repository ppa:neovim-ppa/stable`

`sudo apt update`

`sudo apt install neovim`

这将为您提供一个 Oni 的工作版本，但不会为您提供 C 或 C++的 IDE 特性。

### 3。**安装金属撞击声**。

这就是事情变得更加复杂的地方。虽然 Oni 对于 JavaScript 和 HTML 这样的语言来说是现成的，但是对于 C 或 C++来说却不是。相反，他们的[语言支持指南](http://github.com/onivim/oni/wiki/Language-support#cc)告诉你你需要`clangd`。`Clangd`利用了`clang`，是语言服务器协议[的一个实现，旨在](http://clang.llvm.org/extra/clangd.html)“为……C/c++编辑器提供语言‘智能’特性，如代码完成等。”Ubuntu 中有适用于这两者的 PPAs，可以与以下软件一起安装:

`sudo apt install clang`

T1】

虽然你可能认为这样做可以——但事实并非如此。当 Oni 初始化时，它期望在`/usr/bin/`目录中找到`clangd`，然而 PPA 安装的文件的名称是不同的:`clangd-6.0`(指版本号)。要解决这个问题，可以参考预期的文件创建一个符号链接:

`sudo update-alternatives --install /usr/bin/clangd clangd /usr/bin/clangd-6.0 1000`

* * *

还有 wallah，Oni 适用于 C 和 C++，几乎是开箱即用的…有趣的是，一旦我让它工作起来，当你查看的代码超过 100 行左右时，它的速度就慢得像爬行一样，所以我放弃了它，直接回到 neovim。