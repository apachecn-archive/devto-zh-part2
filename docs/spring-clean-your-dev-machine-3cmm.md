# 春季清理您的开发机器

> 原文：<https://dev.to/philnash/spring-clean-your-dev-machine-3cmm>

开发机器可以构建如此多的 cruft。旧版本、过时的程序和未使用的缓存充斥着硬盘。偶尔花点时间清理一下，腾出一些空间，这很好。

这里有一些可以运行的命令或可以采取的清理机器的操作的提示。如果你有我遗漏的建议，请在 [Twitter](https://twitter.com/philnash) 上与我分享。

## 自制

如果你使用[自制软件](https://brew.sh/)来管理 macOS 上的软件包，你可以运行`brew cleanup`从缓存中移除旧版本的软件包和旧下载。

```
$ brew cleanup
Removing: ...
...
==> This operation has freed approximately 6.9GB of disk space. 
```

Enter fullscreen mode Exit fullscreen mode

使用`-s`标志从缓存中清除最新包版本的下载，给你更多的空间。感谢[大卫·盖恩](https://twitter.com/DavidGuyon)提供的[提示](https://twitter.com/DavidGuyon/status/995038050341281792)。

为了真正把它开到最大，[丹尼尔·米勒](https://twitter.com/dalanmiller) [建议使用 bash 别名](https://twitter.com/dalanmiller/status/994729616077082624)来更新自制软件，升级软件包，然后自己清理。将以下内容添加到您的`.bash_profile` :

```
alias bu="brew update && brew upgrade && brew cleanup" 
```

Enter fullscreen mode Exit fullscreen mode

然后使用
运行命令

```
bu 
```

Enter fullscreen mode Exit fullscreen mode

一切都应该是最新的，不留下任何混乱！

### 自制奖励

一旦所有的缓存都整理好了，花一点时间来确保家酿本身运行顺畅。运行以下命令，获得整理安装时可以采取的操作列表。

```
brew doctor 
```

Enter fullscreen mode Exit fullscreen mode

## 码头工人

几千兆字节的包已经很不错了，但是我们能做得更好吗？如果你正在使用 [Docker](https://www.docker.com/) 你可以用
来清理东西

```
$ docker volume prune
...
Total reclaimed space: 40.77GB 
```

Enter fullscreen mode Exit fullscreen mode

我自己并不是一个 Docker 的大用户，这是 Jack Wearden 给我的提示。

## 红宝石

我使用 [rbenv](https://github.com/rbenv/rbenv) 和 [ruby-build](https://github.com/rbenv/ruby-build) 安装多个版本的 Ruby。我刚刚检查了我安装的版本，我发现 12 颗红宝石已经过期。因为他们也安装了他们的 gem，清除它们为我每个 Ruby 版本节省了几十到几百兆字节。

您可以使用
检查您已安装的版本

```
rbenv versions 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以用
卸载不想要的 Ruby 版本

```
rbenv uninstall 2.1.0 
```

Enter fullscreen mode Exit fullscreen mode

## 节点

我还使用 [nvm](https://github.com/creationix/nvm) 来管理 Node.js 的多个版本。找到你身边的旧节点版本:

```
nvm ls 
```

Enter fullscreen mode Exit fullscreen mode

然后用
卸载

```
nvm uninstall v6.9.2 
```

Enter fullscreen mode Exit fullscreen mode

## 还有别的想法吗？

这应该会在你的机器上清理出一堆空间，在我的机器上确实如此。现在您有更多的空间来安装更多的产品、更多的容器和更多语言的更多版本！

我很乐意收集您可能拥有的保持开发机器平稳运行的任何其他技巧。让我知道你是如何在 Twitter 上清理你的开发机器的。

<small>尘埃图标由 [Smalllike 引自名词项目](https://thenounproject.com/smalllike/collection/cleaning/?i=1683969)T3】</small>

* * *

*[春季清理你的 dev 机](https://philna.sh/blog/2018/05/27/spring-clean-your-dev-machine/)原载于[philna . sh](https://philna.sh)2018 年 5 月 27 日。*