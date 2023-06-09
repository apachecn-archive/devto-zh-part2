# 安装 AUR 软件包的疑难解答

> 原文：<https://dev.to/nabbisen/troubleshooting-around-installing-aur-packages-neg>

# 总结

“麻烦是我的事。”
当我试图通过 [AUR](http://aur.archlinux.org) 安装一个包时，我很幸运地遇到了 3 + 1 个问题。
我决定记录它们，因为关于如何解决它们的知识似乎对将来安装 AUR 软件包时出现的其他问题有所帮助。

# 烦恼

当我试图通过 AUR
将一个名为 qperf 的包安装到我的 [Manjaro Linux](https://manjaro.org/) 上时，它们就发生了

```
$  git clone https://aur.archlinux.org/qperf.git
$  cd qperf
$  makepkg -si 
```

**注: [qperf](https://wiki.archlinux.org/index.php/InfiniBand#qperf_-_Measure_performance_over_RDMA_or_TCP.2FIP) 是一款测量网络带宽和节点间延迟的工具。*
**注意:这篇帖子不是关于如何使用 qperf。这不仅仅是安装它。*

<center>✿✿✿</center>

### #第一个麻烦:缺乏依赖性

#### 点到为止

*   由于缺少包的依赖性，Making 有时会失败。依赖关系并不总是自动解决的。

`makepkg -si`导致失败，出现以下错误:

```
$  # qperf
$  makepkg -si
==>  Making package: qperf 0.4.11-3 ([datetime])
==>  Checking runtime dependencies...
==>  Installing missing dependencies...
error: target not found: rdma-core ==>  ERROR: 'pacman' failed to install missing dependencies. 
```

日志的最后一行提到“错误:‘pacman’未能安装缺失的依赖项。”
这是因为缺乏“依赖性”:

[![](img/27f6a3aba28a2595721f7d98e69b3f54.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--d_29Th2e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b32w5ottqrmoofmhy2r9.png)

在我的例子中，名为 rdma-core 的包没有安装，也没有自动解析。因此，我必须事先手动安装 rdma-core。

注意:我不得不使用 AUR，因为在 Manjaro 包管理器中没有找到它。

```
$  git clone https://aur.archlinux.org/rdma-core.git
$  cd rdma-core 
```

<center>✿✿✿</center>

### #第二个麻烦:GPG 签名

#### 点到为止

*   有时需要接收新的 gpg 密钥。

**注:根据 ArchWiki 的说法， [GPG](https://wiki.archlinux.org/index.php/GnuPG) 是 GnuPG，“一个由 [RFC4880](https://tools.ietf.org/html/rfc4880) 定义的 [OpenPGP](http://openpgp.org/about/) 标准的完整且免费的实现”，它“允许你对你的数据和通信进行加密和签名。”*

安装 rdma-core 失败，出现以下错误:

```
$  makepkg -si
... ==>  Retrieving sources...
 ->  Cloning rdma-core git repo...
 ... ==>  Validating source files with sha512sums...
 rdma-core ... Skipped ==>  Verifying source file signatures with gpg...
 rdma-core git repo ... FAILED (unknown public key [gpg-key-id]) ==>  ERROR: One or more PGP signatures could not be verified! 
```

为了解决这个问题，我必须接收上面日志中给出的密钥作为“未知公钥”:

```
gpg --recv-keys [gpg-key-id] 
```

**注意:`gpg`可以通过 Manjaro 包管理器安装。*

然后制作成功完成:

```
$  # rdma-core
$  makepkg -si
... ==>  Starting package()...
... ==>  Installing package rdma-core with pacman -U...
[sudo] password for [user]:
loading packages...
resolving dependencies...
looking for conflicting packages...

Packages (1) rdma-core-20.1-2

Total Installed Size:  2.24 MiB

:: Proceed with installation? [Y/n] y ... (1/3) Reloading system manager configuration...
(2/3) Reloading device manager configuration...
(3/3) Arming ConditionNeedsUpdate... 
```

<center>✿✿✿</center>

### # *无处不在*烦恼:折磨还是烦恼

#### 点到为止

*   当目标看起来很遥远时，要有耐心，根据需要休息一会儿，然后尝试另一种方式。遇到另一个错误有点进展...

依赖问题解决了，所以我回到制作 qperf。
然后一个新的(下一个)错误出现:

```
$  # qperf
$  makepkg -si
... ==>  Starting build()...
... -- Build files have been written to: [build-path]
[34/235] Creating man page umad_init.3
FAILED: libibumad/man/umad_init.3 ... /usr/bin/pandoc: error while loading shared libraries: libHSzip-archive-0.3.3-9ZXjqhjfOlnGJKb3W7x2JJ-ghc8.6.1.so: cannot open shared object file: No such file or directory
[36/235] Building C object libibverbs/CMakeFiles/ibverbs.dir/cmd.c.o
ninja: build stopped: subcommand failed. ==>  ERROR: A failure occurred in build().
 Aborting... 
```

我养成了一个习惯，在这样的时候思考，这样的情况会提高我，给我新的知识和视野。

<center>✿✿✿</center>

### #第三麻烦:老图书馆

#### 点到为止

*   制作过程伴随着几个库，由于它们没有更新，可能会出现错误。

上述错误的原因是一些软件包的版本太旧了。(在这种情况下，它是“haskell-zip-archive”库，因为日志中提到了“libHSzip-archive-...。所以:...:没有这样的文件或目录。)

[![](img/b447ca0353f6575ca1709d7a56bd92b2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oRIqRyF9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/azlk8kirdrzsgompe1ms.jpg)

我按照 Manjaro 软件包经理的指导更新了所有可用的软件包。

<center>✿✿✿</center>

### 毕竟

```
$  # qperf
$  makepkg -si
... ==>  Finished making: qperf 0.4.11-3 ([datetime])
==>  Installing package qperf with pacman -U...
loading packages...
resolving dependencies...
looking for conflicting packages...

Packages (1) qperf-0.4.11-3

Total Installed Size:  0.17 MiB ==>  Finished making: qperf 0.4.11-3 ([datetime])
==>  Installing package qperf with pacman -U...
loading packages...
resolving dependencies...
looking for conflicting packages...

Packages (1) qperf-0.4.11-3

Total Installed Size:  0.17 MiB

:: Proceed with installation? [Y/n] y ... :: Processing package changes... ... :: Running post-transaction hooks... ... :: Proceed with installation? [Y/n] y ... :: Running post-transaction hooks...
(1/1) Arming ConditionNeedsUpdate... 
```

完成😆