# 说真的，定期做啤酒清理。

> 原文：<https://dev.to/clpsplug/seriously-periodically-do-brew-cleanup-36bi>

**编辑:**

好像是这样的[从 v2.0.0 开始，homebrew 决定每 30 天进行一次自动清理，](https://brew.sh/2019/02/02/homebrew-2.0.0/)所以现在很少遇到下面这样的问题:)由于历史原因(也可能是为了其他不自动清理的软件)我一直坚持这样做。)

我刚刚发现我的自制软件为我的可升级桶保留了所有旧的安装程序😱

我跑着`brew cleanup`去寻找令我惊恐的:

```
_( _・ω)_<(me XD)@localhost H30 11/12 12:13:17)
> brew cleanup                                                                [~]
Removing: /usr/local/Cellar/apr/1.6.3... (60 files, 1.3MB)
Removing: /usr/local/Cellar/arpack/3.6.2... (22 files, 1.4MB)
...
Removing: /Users/(me XD)/Library/Caches/Homebrew/Cask/unity--2018.2.1f1,1a9968d9f99c.pkg... (940.3MB)
Removing: /Users/(me XD)/Library/Caches/Homebrew/Cask/unity--2018.2.6f1,c591d9a97a0b.pkg... (935.7MB)
...
Removing: /Users/(me XD)/Library/Caches/Homebrew/Cask/unity--2018.2.13f1,83fbdcd35118.pkg... (944.6MB)
Removing: /Users/(me XD)/Library/Caches/Homebrew/Cask/unity--2017.3.1f1,fc1d3344e6ea.pkg... (721.0MB)
Removing: /Users/(me XD)/Library/Caches/Homebrew/Cask/unity--2018.2.12f1,0a46ddfcfad4.pkg... (935.6MB)
Removing: /Users/(me XD)/Library/Caches/Homebrew/Cask/unity--2018.1.0f2,d4d99f31acba.pkg... (943.8MB)
...
==> This operation has freed approximately 19.9GB of disk space. 
```

Enter fullscreen mode Exit fullscreen mode

***19.9GB？！？*T3】**

我总共只有 250GB 的空间；这是一大块数据。如果你正在使用家酿木桶安装你最喜欢的应用程序，一定要做`brew cleanup`(或在升级时使用标志`--cleanup`！)