# 从 Debian 上的源代码安装 nftables

> 原文：<https://dev.to/isabelcmdcosta/installing-nftables-from-sources-ondebian--4ic>

在这篇文章中，我将向你展示我是如何从源代码安装 *nftables* 的。我需要从源代码中这样做，以获得最新版本的 *nftables* 。

我需要使用 [iptables](https://www.netfilter.org/projects/iptables/index.html) 来执行无状态网络地址转换(NAT ),但是后来我发现使用 *iptables* 似乎是不可能的。所以我找到了 [nftables](https://netfilter.org/projects/nftables/) ，它让我可以做到。

为了拥有最新版本的 *nftables* ，至少高于 v0.7，我从源代码安装了这个工具。我开始按照[nf tables 的 wiki 页面上的说明进行安装](https://wiki.nftables.org/wiki-nftables/index.php/Building_and_installing_nftables_from_sources)。

这里列出了 *nftables* 包的依赖关系[。这些是主要的:](https://packages.debian.org/source/stable/nftables)

*   极简的网络链接库
*   [libnftnl](https://www.netfilter.org/projects/libnftnl/) —低级 netlink 用户空间库

先是尝试在 Debian 上用`aptitude search libmnl`安装了 *libmnl* 提供的包，然后安装了`libmnl-dev`，但是后来对我不起作用，所以安装了`libnftnl`之后就从源码安装了这个。

* * *

为了安装 *libnftnl* 用户空间库， *nftables* wiki 页面建议使用这些命令:

```
# git clone git://git.netfilter.org/libnftnl
# cd libnftnl
# sh autogen.sh
# ./configure
# make
# make install 
```

Enter fullscreen mode Exit fullscreen mode

运行命令时，我得到第一个错误(在第三个命令中):

```
root@debian:/home/debian/libnftnl# sh autogen.sh 
autogen.sh: 3: autogen.sh: autoreconf: not found 
```

Enter fullscreen mode Exit fullscreen mode

然后我安装了缺失的包: *autogen* ，*auto conf*。

```
# aptitude install autoconf autogen 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我再次尝试了`sh autogen.sh`步骤，得到了以下错误:

```
root@debian:/home/debian/libnftnl# sh autogen.sh 
configure.ac:28: error: possibly undefined macro: AC_DISABLE_STATIC
      If this token and others are legitimate, please use m4_pattern_allow.
      See the Autoconf documentation.
autoreconf: /usr/bin/autoconf failed with exit status: 1 
```

Enter fullscreen mode Exit fullscreen mode

经过一番研究，我发现我必须安装 *libtool* 包，用`aptitude install libtool`。

然后我又试了一次，得到了这个输出:

```
root@debian:/home/debian/libnftnl# sh autogen.sh 
libtoolize: putting auxiliary files in AC_CONFIG_AUX_DIR, `build-aux'.
libtoolize: copying file `build-aux/ltmain.sh'
libtoolize: putting macros in AC_CONFIG_MACRO_DIR, `m4'.
libtoolize: copying file `m4/libtool.m4'
libtoolize: copying file `m4/ltoptions.m4'
libtoolize: copying file `m4/ltsugar.m4'
libtoolize: copying file `m4/ltversion.m4'
libtoolize: copying file `m4/lt~obsolete.m4'
configure.ac:8: installing 'build-aux/ar-lib'
configure.ac:8: installing 'build-aux/compile'
configure.ac:5: installing 'build-aux/config.guess'
configure.ac:5: installing 'build-aux/config.sub'
configure.ac:10: installing 'build-aux/install-sh'
configure.ac:10: installing 'build-aux/missing'
examples/Makefile.am: installing 'build-aux/depcomp' 
```

Enter fullscreen mode Exit fullscreen mode

终于`autogen.sh`脚本工作了！此时，我可以前进到下一个命令:`./configure`。这是我的输出:

```
root@debian:/home/debian/libnftnl# ./configure
checking build system type... x86_64-unknown-linux-gnu
checking host system type... x86_64-unknown-linux-gnu
checking for gcc... gcc
checking whether the C compiler works... yes
checking for C compiler default output file name... a.out
checking for suffix of executables... 
checking whether we are cross compiling... no
checking for suffix of object files... o
checking whether we are using the GNU C compiler... yes
checking whether gcc accepts -g... yes
checking for gcc option to accept ISO C89... none needed
checking whether gcc understands -c and -o together... yes
checking for ar... ar
checking the archiver (ar) interface... ar
checking for a BSD-compatible install... /usr/bin/install -c
checking whether build environment is sane... yes
checking for a thread-safe mkdir -p... /bin/mkdir -p
checking for gawk... gawk
checking whether make sets $(MAKE)... yes
checking for style of include used by make... GNU
checking whether make supports nested variables... yes
checking how to create a pax tar archive... gnutar
checking dependency style of gcc... gcc3
checking whether make supports nested variables... (cached) yes
./configure: line 4135: syntax error near unexpected token `LIBMNL,'
./configure: line 4135: `PKG_CHECK_MODULES(LIBMNL, libmnl >= 1.0.0)' 
```

Enter fullscreen mode Exit fullscreen mode

从这个输出中，我注意到我丢失了 *libmnl* 包，这个包是我后来安装的，如下所示。

* * *

为了正确地从源代码安装 *libmnl* 用户空间库，我运行了这些命令:

```
# git clone git://git.netfilter.org/libmnl
# cd libmnl
# sh autogen.sh
# ./configure
# make
# make install 
```

Enter fullscreen mode Exit fullscreen mode

对于我安装的以前的包，这些步骤没有错误。

* * *

现在回到 *libnftnl* 的安装，我试着再次运行`./configure`，我仍然得到同样的问题。我按照[这篇博文](http://blog.anarey.info/2014/08/pkg_check_moduleslibmnl-libmnl-1-0-0-error/)的说明修复了这个问题。以下是我遵循的步骤:

```
root@debian:/home/debian/libnftnl# whereis libmnl
libmnl: /usr/local/lib/libmnl.so /usr/local/lib/libmnl.la /usr/include/libmnl 
```

Enter fullscreen mode Exit fullscreen mode

然后我做了:

```
root@debian:/home/debian/libnftnl# ldd /usr/local/lib/libmnl.so
 linux-vdso.so.1 (0x00007ffe5212a000)
 libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007efc29faf000)
 /lib64/ld-linux-x86-64.so.2 (0x000056203c383000) 
```

Enter fullscreen mode Exit fullscreen mode

帖子还建议我用`aptitude install pkg-config`安装 *pkg-config* ，用`aptitude install libgmp3-dev`安装 *gmp* 包。这里有一个帖子展示了如何在其他 Linux 发行版中安装[。](http://www.mathemagix.org/www/mmdoc/doc/html/external/gmp.en.html)

还有，上面的帖子建议我配置 pkg-config 环境路径:

```
# PKG_CONFIG_PATH=/usr/local/lib/pkgconfig
# export PKG_CONFIG_PATH 
```

Enter fullscreen mode Exit fullscreen mode

然后我又跑了`sh autogen.sh`和`./configure`。之后我得到了一个更好更长的输出，就像这样:

```
root@debian:/home/debian/libnftnl# ./configure
checking build system type... x86_64-unknown-linux-gnu
checking host system type... x86_64-unknown-linux-gnu
checking for gcc... gcc
checking whether the C compiler works... yes
checking for C compiler default output file name... a.out
checking for suffix of executables... 
checking whether we are cross compiling... no
(...)
checking for LIBMNL... yes
(...)
config.status: creating tests/Makefile
config.status: creating libnftnl.pc
config.status: creating doxygen.cfg
config.status: creating config.h
config.status: executing depfiles commands
config.status: executing libtool commands
libnftnl configuration:
  JSON support:    no 
```

Enter fullscreen mode Exit fullscreen mode

在这一步之后，我终于正确无误地运行了最后两个命令— `make`和`make install` —
。

* * *

既然已经成功安装了 *libmnl* 和 *libnftnl* ，我尝试从源代码中安装用户空间 *nft* 命令行实用程序、 *nftables* ，命令如下:

```
# git clone git://git.netfilter.org/nftables
# cd nftables
# sh autogen.sh
# ./configure 
```

Enter fullscreen mode Exit fullscreen mode

运行最后一个命令`./configure`时，我得到一个错误，表明我丢失了 *nftables* 依赖的 *bison* 包:

```
root@debian:/home/debian/nftables# ./configure
checking build system type... x86_64-unknown-linux-gnu
checking host system type... x86_64-unknown-linux-gnu
(...)
checking for flex... no
checking for lex... no
checking for bison... no
checking for byacc... no
*** Error: No suitable bison/yacc found. ***
    Please install the 'bison' package. 
```

Enter fullscreen mode Exit fullscreen mode

后来我收到了关于 *flex* 和 *docbook2x* 包的同样消息。[注意，这两者都在 *nftables* 依赖列表](https://packages.debian.org/source/stable/nftables)中。所以为了修复这些错误信息，我安装了这些包——bison、flex 和 docbook2x——以及`aptitude install <package>`(例如:`aptitude install flex`)。

在这之后，我得到了这个错误消息:`configure: error: No suitable version of libreadline found`。为了解决这个问题，我遵循了这篇文章的[步骤。](https://www.howtoinstall.co/en/debian/jessie/libreadline-dev) 

```
# aptitude update
# aptitude install libreadline-dev 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，我已经安装了足够多的东西来运行 *nft* 工具。这是没有错误的安装输出:

```
root@debian:/home/debian/nftables# ./configure
checking build system type... x86_64-unknown-linux-gnu
checking host system type... x86_64-unknown-linux-gnu
(...)
config.status: creating include/linux/netfilter_ipv4/Makefile
config.status: creating include/linux/netfilter_ipv6/Makefile
config.status: creating doc/Makefile
config.status: creating files/Makefile
config.status: creating files/nftables/Makefile
config.status: creating config.h
config.status: executing depfiles commands
config.status: executing libtool commands
nft configuration:
  cli support:                 yes
  enable debugging symbols:    yes
  use mini-gmp:                no
  enable man page:             yes
  enable pdf documentation:    no
  libxtables support:          no 
```

Enter fullscreen mode Exit fullscreen mode

然后我运行了`make`和`make install`，也没有出错。

* * *

最后，我检查了一下 *nftables* 是否安装成功:

```
root@debian:/home/debian/nftables# nft
nft: no command specified
root@debian:/home/debian/nftables# nft -v
nftables v0.8.2 (Joe Btfsplk) 
```

Enter fullscreen mode Exit fullscreen mode

的确如此。成功了！

## 总结

完成所有这些步骤后，我必须将它安装在另一台虚拟机上。这次我尝试了一种更简单的方法，顺序如下:

*   首先，我运行 aptitude update 来下载新的和可升级的软件包列表。
*   然后我用 aptitude install 安装了第一次安装时需要的所有包。其中包括 *autoconf* ， *autogen* ， *libtool* ， *pkg-config* ， *libgmp3-dev* ， *bison* ， *flex* ， *docbook2x* 和 *libreadline-dev* 。在这里可以查看*nf tables*T20】的依赖关系。
*   接下来，我用下面几行配置了 pkg-config 的路径:

```
# PKG_CONFIG_PATH=/usr/local/lib/pkgconfig
# export PKG_CONFIG_PATH 
```

Enter fullscreen mode Exit fullscreen mode

*   然后我安装了 [*libmnl*](https://www.netfilter.org/projects/libmnl/) 库，使用了之前给出的命令:

```
# git clone git://git.netfilter.org/libmnl
# cd libmnl
# sh autogen.sh
# ./configure
# make
# make install 
```

Enter fullscreen mode Exit fullscreen mode

*   之后，我安装了 [*libnftnl*](https://www.netfilter.org/projects/libnftnl/) 库，使用这些命令，也如前面所示:

```
# git clone git://git.netfilter.org/libnftnl
# cd libnftnl
# sh autogen.sh
# ./configure
# make
# make install 
```

Enter fullscreen mode Exit fullscreen mode

*   最后，我这样安装了 [*nftables*](https://www.netfilter.org/projects/nftables/) :

```
# git clone git://git.netfilter.org/nftables
# cd nftables
# sh autogen.sh
# ./configure
# make
# make install 
```

Enter fullscreen mode Exit fullscreen mode

*   接下来，为了检查 *nftables* 是否工作，我用`nft -v`检查了版本。令人惊讶的是，我得到了一个我从未见过的错误，我用`ldconfig`命令修复了它。如果你对`ldconfig`不熟悉，你可以在这里[了解更多。您可以检查以下命令的顺序:](https://linux.die.net/man/8/ldconfig)

```
root@debian:/home/debian# nft -v
nft: error while loading shared libraries: libnftnl.so.7: cannot open shared object file: No such file or directory
root@debian:/home/debian# ldconfig
root@debian:/home/debian# nft -v
nftables v0.8.2 (Joe Btfsplk) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

[*这个也贴在介质上。*T3】](https://medium.com/@isabelcmdcosta/installing-nftables-from-sources-on-debian-eb8c24bbc5ca)

你可以在 [Twitter](https://twitter.com/isabelcmdcosta) 、 [LinkedIn](https://www.linkedin.com/in/isabelcmdcosta) 、 [Github](https://github.com/isabelcosta) 、 [Medium](https://medium.com/@isabelcmdcosta) 、[我的个人网站](https://isabelcosta.github.io/)上找到我。