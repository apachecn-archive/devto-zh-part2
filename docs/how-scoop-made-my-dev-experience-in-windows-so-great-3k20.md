# scoop 如何让我在 Windows 中的开发体验如此棒

> 原文：<https://dev.to/lgraziani2712/how-scoop-made-my-dev-experience-in-windows-so-great-3k20>

从开始编程开始，我大部分时间都在用 Windows。每次格式化电脑或更换电脑时，我总是在配置所需的一切时遇到问题。一开始，我尝试使用 xamp 或 wamp，但是我对这些工具感到不舒服，更新、改变和在队友之间共享配置都很困难。

窗户对我来说也非常不舒服。自从学会了在 Linux 上使用 CLI(VI 4 ever，lol)。开始用 Cmder 的时候一见钟情。

我一直在和一个 4 人团队合作，主要在后端使用 nodejs 或 php7.2，在前端使用 vue。今年年初，当我们开始一个新项目时，我真的想和他们分享我是如何配置我的开发环境的，因为每个人都使用 Windows。

我开始搜索一些软件包管理器，但 Windows。我先找到了 chocolatey，我试过但是不喜欢(不记得为什么了)。于是我继续寻找，直到找到`scoop`。

## 勺

> Scoop 是 Windows 的命令行安装程序。

*   Github 库:[https://github.com/lukesampson/scoop](https://github.com/lukesampson/scoop)

这个工具使我的开发环境配置变得轻而易举。我甚至可以创建一个 git 存储库，存储每个工具的每个配置。这让我和我的队友可以在不到一个小时的时间内安装、更新和配置 php(带 x-debug)、apache、nodejs、yarn、mariadb、composer、cmder 和其他通用工具，如 gimp、screentogif(另一个优秀的 oss 工具)或 filezilla。

今天我不得不在另一台笔记本电脑上从头开始安装所有的东西，我只需要安装 scoop，克隆配置 repo，然后开始安装其他的东西。

## 那么我该如何开始用 git repo 来使用 scoop 呢？

首先你要安装 scoop(摘自官方页面):

### 要求

*   Windows 7 SP1+/Windows Server 2008+版
*   PowerShell 3(或更高版本)和。NET Framework 4.5 以上版本
*   必须为您的用户帐户启用 PowerShell，例如

    `set-executionpolicy remotesigned -s currentuser`

### 安装

从 PowerShell 中运行这个命令，将 scoop 安装到它的默认位置(C:\Users <user>\scoop):</user> 

```
iex (new-object net.webclient).downloadstring('https://get.scoop.sh') 
```

### 创建您的配置存储库

考虑到你在默认位置安装了 scoop(C:\ Users<user>\ scoop)，你必须访问`C:\Users\<user>\scoop\persist`文件夹。每个 scoop 应用程序都会将它们的配置存储在这个不受卸载影响的文件夹中。然后您可以运行`git init`并开始保存您的配置。</user>

顺便说一句，在你开始提交和推送所有东西之前，考虑到有一些特殊的文件是我们不想推送的。为了防止像这些二进制文件来自 mariadb 或 nodejs 缓存，我们必须在存储库的根目录下创建一个`.gitignore`文件，换句话说，在`persist`文件夹中。可以添加自定义文件。

我的`.gitignore`有以下值:

```
vendor
cache
data
plugins
composer
nodejs
yarn
log.*
user-ConEmu.xml
.history
*.dmp 
```

我还写了一个`README.md`文件，里面有每个人都需要知道的手动配置每个应用程序的所有信息，比如 mariadb，或者添加 apache 和 mariadb 作为 windows 服务。

像用 xdebug 配置 php 这样的事情，对我来说总是很麻烦，第一次就像安装它一样简单。默认情况下，当您安装`php-xdebug`时，scoop 会在`C:\Users\<user>\scoop\persist\php\conf.d\`文件夹中创建一个`xdebug.ini`文件，内容如下:

```
zend_extension=C:\Users\<user>\scoop\apps\php-xdebug\current\php_xdebug 
```

但问题是，如果我想有一个通用的配置，每个人都可以使用，我不能有绝对路径。以下配置是我有的:

```
zend_extension=../../../../apps/php-xdebug/current/php_xdebug

[XDebug]
xdebug.remote_enable = 1
xdebug.remote_autostart = 1 
```

新的`[XDebug]`配置允许 IDE 的 VSCode 调试 php 代码。

## 队友之间分享配置

一旦您开始将您的配置推送到您的存储库中，您现在就可以共享它了。由于`persist`文件夹会在安装后立即包含数据，如果你试图克隆里面的东西，`git`会抱怨。要解决这个问题，你必须做到以下几点:

```
git init 

git remote add origin PATH/TO/REPO 

git fetch 

git checkout -t origin/master -f 
```

来源:https://stackoverflow.com/a/43287779/2862917

我希望这有助于简化任何使用 Windows 的人的配置过程！随便问我什么！