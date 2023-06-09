# ★在 Laravel 代客中轻松切换 PHP 版本

> 原文：<https://dev.to/freekmurze/easily-switch-php-versions-in-laravel-valet-5an4>

除了享受一些全新的工作，我们还经常在 [Spatie](https://spatie.be) 从事遗留项目。有时这些项目不能在最新的 PHP 版本上运行。在这篇博文中，我想向你展示一种在使用 [Laravel Valet](https://laravel.com/docs/5.7/valet) 时轻松切换 PHP 版本的方法。(我知道您也可以使用 Docker 或 Virtualbox 来解决这个问题，但这超出了本文的范围。)

我将假设你已经使用了最新版本的 [homebrew](https://brew.sh/) ，Laravel Valet 并且安装了 PHP 7.2。为了容易地切换 PHP 版本，你首先应该安装 PHP 的旧版本。

在您的终端中运行这些命令

```
brew install php@7.0
brew install php@7.1 
```

接下来我们将定义一个 bash 函数和一些别名来轻松切换当前的 PHP 版本。如果你不知道如何创建一个 bash 函数，也不知道该把这段代码放在哪里，那么先阅读一下[这篇博文](https://medium.com/devnetwork/how-to-create-your-own-custom-terminal-commands-c5008782a78e)。

```
phpv() {
    valet stop
    brew unlink php@7.0 php@7.1 php@7.2
    brew link --force --overwrite $1
    brew services start $1
    composer global update
    rm -f ~/.config/valet/valet.sock
    valet install
}

alias php70="phpv php@7.0"
alias php71="phpv php@7.1"
alias php72="phpv php" 
```

有了这个方法，您可以通过在终端中执行`php70`轻松切换到 PHP 7.0。您可以通过执行`php72`切换回 7.2。第一次切换到特定的 PHP 版本可能需要一段时间。后续的切换会快得多。

这个`phpv`功能的功劳归于我的同事[塞布](https://twitter.com/sebdedeyne)和[威廉](https://twitter.com/willemvbockstal)。他们基于[这篇关于如何在 Mojave](https://getgrav.org/blog/macos-mojave-apache-multiple-php-versions) 中使用多个 PHP 版本的优秀博文创建了它。