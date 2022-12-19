# brew cleanup 可能会节省您的磁盘空间

> 原文：<https://dev.to/ohbarye/brew-cleanup-might-save-your-disc-space-2aj>

你在 macOS 上用家酿吗？你有椎间盘突出的问题吗？

那就试试`brew cleanup`吧。

## 概述

由于 brew 不会自动删除旧公式，因此您安装的软件包会堆积起来并挤压您的光盘。

按照[官方常见问题](https://docs.brew.sh/FAQ)，我尝试了`brew cleanup`，在我的情况下获得了大约 4 GB 的自由空间。

## 用法

官方页面介绍的样本很少。

```
# remove a formula
$ brew cleanup <formula>

# clean up everything at once
$ brew cleanup

# see what would be cleaned up
$ brew cleanup -n 
```

Enter fullscreen mode Exit fullscreen mode

要了解更多信息，请参阅帮助。

```
$ brew cleanup -h

brew cleanup [--prune=days] [--dry-run] [-s] [formulae]:
    For all installed or specific formulae, remove any older versions from the
    cellar. In addition, old downloads from the Homebrew download-cache are deleted.

    If --prune=days is specified, remove all cache files older than days.

    If --dry-run or -n is passed, show what would be removed, but do not
    actually remove anything.

    If -s is passed, scrub the cache, removing downloads for even the latest
    versions of formulae. Note downloads for any installed formulae will still not be
    deleted. If you want to delete those too: rm -rf $(brew --cache) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了新发现。

*   可以在 N 天或更早的时间摧毁缓存。虽然当我们省略这个选项时，它缺少关于一个案例的描述，但是通过读取[代码](https://github.com/Homebrew/brew/blob/c933247a77fde69a79e9172f821147ea5140e3a1/Library/Homebrew/cleanup.rb#L49)，它默认为 14 天。
*   `-n`是`--dry-run`的缩写。
*   我们甚至可以用`-s`清理最新的公式。

## 进一步的细节

如果你想更深入地了解它，阅读代码是最好的方法。

*   [入口点](https://github.com/Homebrew/brew/blob/76cd7c79498daf380d99ed7379394618164b602b/Library/Homebrew/cmd/cleanup.rb)
*   [实际过程](https://github.com/Homebrew/brew/blob/c933247a77fde69a79e9172f821147ea5140e3a1/Library/Homebrew/cleanup.rb)