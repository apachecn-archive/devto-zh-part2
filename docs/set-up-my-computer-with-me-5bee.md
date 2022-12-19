# 和我一起设置我的计算机！

> 原文：<https://dev.to/aspittel/set-up-my-computer-with-me-5bee>

我刚买了我的第一台个人 Mac，我认为这是一个很好的机会来记录我设置它的过程，以及我安装的所有东西！

我期望把重点放在那些很难安装并且在整个过程中给我带来问题的东西上，但是我可以很高兴地说，在设置好一切的时候，我没有遇到任何问题！所以，这就是我所做的和原因！

### [铬](https://www.google.com/chrome/)

我几乎只使用 Chrome，而且已经用了一段时间了。它的开发工具很棒。我在游猎中挣扎，所以这是我得到的第一件东西！我刚通过 Safari 下载的。我通过登录我的电子邮件帐户同步了我的设置和收藏夹。

### [奇观](https://www.spectacleapp.com/)

眼镜是一个窗口管理工具，这样你就可以使用键盘快捷键在你的显示器上移动窗口。我已经使用它有一段时间了，我完全依赖它——老实说，没有它我真的很难操作电脑！这个很重要！我也通过浏览器下载了这个，我想在做其他事情之前设置好它。

### [自制](https://brew.sh/)

家酿是一个软件包管理器，你可以很容易地在 MacOS 上安装东西。老实说，我无法想象在这种情况下安装任何没有它的东西。

下面是安装它的命令:

```
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 
```

## 语言

我在个人电脑上通常使用的三种语言是 Python、Ruby 和 JavaScript。

### 红宝石

我使用`rbenv`来设置 Ruby 环境。它允许您轻松地在版本之间切换，并为不同的项目设置它们。我将我的全局 Ruby 版本设置为`2.5.1`，这是`dev.to`使用的版本！

```
$ brew install rbenv 
```

然后，我将`eval "$(rbenv init -)"`添加到我的。zshrc(如果不使用 Zsh，这将是您的 Bash 概要文件)。每当我打开终端时，它就会自动加载 rbenv。

```
$ rbenv install 2.5.1
$ rbenv global 2.5.1 
```

### 巨蟒

Python 是我的第一门编程语言，它仍然是我最专业的语言。有很多方法可以安装 Python，但是我个人更喜欢不安装 Anaconda。我发现以后我的安装问题更少了。它还会自动安装 pip(Python 包管理器),这样我就不用担心以后的安装了。

我还马上安装了 IPython，因为我几乎每天都用它来测试代码。

```
$ brew install python3
$ pip3 install ipython 
```

### 节点

要设置的第三种语言是 Node。这一个通常不是太坏，我在过去没有太多的节点安装问题。

```
$ brew install node 
```

## 开发者工具

### Visual Studio 代码

我使用 Visual Studio 代码作为我的文本编辑器已经有一段时间了，我对它非常着迷。我在我的设置上有一个[完整的帖子。](https://zen-of-programming.com/vs-code-setup)

我使用`brew`来安装它，尽管对于你想要桌面图标的应用程序，你添加`cask`就可以了，它会为你做到这一点！

```
$ brew cask install visual-studio-code 
```

### 去吧

Git 相当重要；绝对是必须装的！我再次安装它通过自制。

```
$ brew install git 
```

我还建立了一个全球。gitignore 以便某些文件总是被排除在版本控制之外。

然后，我对它进行了设置，以便我使用 Visual Studio 代码而不是 Vim for Git。

```
$ git config --global core.editor 'code --wait' 
```

我还设置了我的 [GitHub SSH 密钥](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)，这样我就不用一直输入我的密码了。

### Fira 代码

我的终端和 VS 代码都使用 Fira 代码字体。我喜欢绑带和它的样子！

```
$ brew tap caskroom/fonts
$ brew cask install font-fira-code 
```

### 草图

我在我所有的设计工作中都使用草图——下面是关于我的工作流程的一些信息。

```
$ brew cask install sketch 
```

### Zsh + ITerm2

我将 Zsh 与 ITerm2 一起用于我的终端设置。你可以在这里阅读所有关于那个[的内容！我用自制软件安装了 ITerm2。Zsh 安装在 MAC 电脑上，不过我用了“哦，我的-zsh”来完成安装过程。](https://zen-of-programming.com/terminal-setup) 

```
$ brew cask install iterm2
$ sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)" 
```

## 其他东西

### [天马行空](https://flexibits.com/fantastical)

我还安装了 Fantastical2。这是一个改进的谷歌日历，允许你用多个账户登录，一次查看你所有的日历。对我来说，这很重要，因为我有工作、博客和个人日程表。我曾经因为看错了日历而不小心重复预定了一次，所以我现在非常小心地检查所有的日历！

### 懈怠

尽管 Slack 主要是为了工作，但我也有几个社交组织，所以我的个人电脑上也有。他们大多是在我的地区聚会。我再次用自制软件安装它。

```
$ brew cask install slack 
```

### 桌面背景

我用[为我所有的桌面背景设计 Love Fest](http://www.designlovefest.com/category/downloads/) 。它们太漂亮了，他们每周都会发一套，所以总会有新的给我用。我的新电脑用的是[这款](http://www.designlovefest.com/wp-content/uploads/2018/06/dyt-template.jpg)。

## 结论

这整个设置花了很少的时间，非常顺利。我过去遇到过很多安装问题，所以说实话，我非常惊讶！请随意[发推文](https://twitter.com/aspittel)告诉我你最喜欢的我应该安装的应用程序！