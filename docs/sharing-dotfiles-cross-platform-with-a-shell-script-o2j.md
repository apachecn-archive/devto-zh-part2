# 分享。带外壳脚本的跨平台点文件

> 原文：<https://dev.to/amcsi/sharing-dotfiles-cross-platform-with-a-shell-script-o2j>

在我的职业生涯中，我经历过许多 Linux 环境。我在第一个工作场所就有一个，在那里我可以设置自己的`.vimfiles`设置。然后在家里我第一次安装 Ubuntu 的时候，会有一个`.bashrc`文件用来存储一些别名。然后在我的 Windows 上，在我的类似 Linux 的 cygwin 环境中，我不得不复制同样的`.bashrc`，也许我创建了一个`.screenrc`来配置 GNU 屏幕。然后在我的个人数字海洋服务器上，我有了另一个 Linux 环境，我不得不再次复制我的配置。然后我在一台新电脑上安装了 Linux，需要再次找到并复制我的别名...诸如此类。

我可能不得不一遍又一遍地手动复制本地 Linux 配置的部分，大约二十次左右，然后我才意识到这必须以某种方式自动化，这样我就不必在处理新的 Linux 环境时为准备好所有的配置而遭受如此大的痛苦。在这篇文章中，我将向你展示我是如何解决这个问题的。

为什么我在标题中选择 shell 脚本？这是为了最大限度地减少需要安装的依赖项，以便让我的`.dotfiles`在任何服务器上启动和运行。

## 用 Git 对点文件进行版本控制

首先，除了使用版本控制，保存几个文件(`.bashrc`、`.gitignore_global`、`bin/`)的状态以保持它们同步的最好方法是什么？

在考虑对您的主目录中的文件进行版本控制时，您可能会想到一个问题...

#### 您想要版本化的点文件是您主目录中文件/目录的一个非常小的子集

确实如此。如果您开始对您的主目录进行版本控制，所有其他不相关的文件/目录将会显示为未版本化，从而一直混淆您的`git status`输出。

为了解决这个问题，我在我的主目录中创建了一个`.gitignore`文件，其中包含这个文件:`*`。基本上忽略一切。

这完全不是问题。每当我想添加新的文件，我可以只做`git add -f <file>`绕过忽略。

## 克隆。新环境中的点文件

现在我们已经在 Git 中对我们的点文件进行了版本控制，我们需要能够将其克隆到新的环境中来使用它们。

在任何新的 Linux 环境中，我们都希望将我们的点文件克隆到主目录中。不幸的是，在每一个 Linux 环境中，您的主目录都不是空的，Git 没有提供克隆到现有目录的方法。

所以我们需要克隆到一个新的目录中。我们可以称它为`dotfiles`，它可以在我们的主目录中。

之后，我们需要将文件从`dotfiles`目录复制到主目录。这也存在一些问题，尽管这是一个很好的解决方法:

1.  对于非 Linux 专家来说，从一个目录中递归地复制文件通常是很重要的，包括以点开始的文件(大多数我们想要版本化的点文件)
2.  我们的新 Linux 环境可能已经有了一些`.bashrc`文件，我们可能不想盲目地用我们的覆盖它。也许这实际上是我们的一个旧的 Linux 环境，其中有我们的一个旧的`.bashrc`，里面有一些有趣的东西。
3.  此外，担心这一切也只是浪费时间。在任何 Linux 环境下尽快准备好我们所有的点文件会很好，这样就不会觉得这是一件苦差事。

那么为什么不将这一切自动化呢？

## 使用安装程序作为 bash 脚本

我不是 bash 脚本的忠实粉丝，但是它们的可移植性非常好，所以我选择制作其中的一个。

bash 脚本会放在哪里？当然，它会在我们的点文件中进行版本控制。我自己把它放在`bin/install_dotfiles.sh`里。

从`*`复制文件时如何包含以点号开头的文件，这个问题的答案是通过设置:

```
shopt -s dotglob 
```

Enter fullscreen mode Exit fullscreen mode

你现在知道你需要什么了...但是你可能会忘记。所以把这个留在你的 bash 脚本中，这样你就不用记住了！

另一件事是如何避免覆盖现有文件。

如果您不知道，可以将一个参数传递给`cp`，它为任何可能被副本覆盖的文件保留编号备份。是`--backup=numbered`。

因此，初始安装脚本会复制所有带有备份的文件，包括`.git`文件夹和`.gitignore`文件，以确保您的主文件夹是版本化的，但会忽略所有文件。

bash 脚本基本上包含:

```
#!/bin/bash
shopt -s dotglob
cd ~/dotfiles
cp -rv --backup=numbered * ~ 
```

Enter fullscreen mode Exit fullscreen mode

就这样，我们有了一种跨 Linux 环境同步点文件的便携方法，只需一个简单的初始安装程序 bash 脚本，每个新环境只需运行一次。

## 为 MAC 添加

不幸的是，要在 Mac 上工作，还需要一个依赖项。

为了让 macOS 上的`cp`和 Linux 上的`--backup=numbered`一样工作，你需要确保安装 coreutils(比如用`brew install coreutils`，然后确保在`setopt` :
之后使用 GNU `cp`命令

```
# Wrapper for cp to work in macOS.
cp() {
    if [ -x "$(command -v gcp)" ]; then command=gcp
    else command=cp fi command $command "$@"
} 
```

Enter fullscreen mode Exit fullscreen mode

## 最后扩展名:dist 文件

您可能有一些不想要版本化的本地配置文件，但是希望在新的 Linux 环境中最初安装一个默认的“dist”版本。为此，你可以有一个名为`.dotfiles.dist`的文件夹来存放所有这样的文件。然后在你的脚本文件末尾拷贝完文件后，你可以接着做:

```
cp -rv --backup=numbered .dotfiles.dist/* ~ 
```

Enter fullscreen mode Exit fullscreen mode

如果你感兴趣，你可以在这里看到我的 dotfiles 安装脚本。