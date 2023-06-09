# 介绍 SIMPLE -一种新的编程语言。

> 原文：<https://dev.to/kvng_zeez/introducing-simple---a-new-programming-language--ld9>

嗨 devs，我将介绍一种新的编程语言 **Simple** 。顾名思义，这种编程语言很简单。

# 简单

简单智能模块化编程语言和环境
当前版本:0.3.213
[![simple-repl](img/78d14b2596d744b5c60d9caaba132fa6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SmcL1bUo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/qx5bbc3.png)

### 讲述简单

SIMPLE 是一个简单而创新的多范例编程。它可以嵌入到 C/C++项目中。该语言可移植到各种操作系统，包括 windows、macOS、任何 linux 和 unix 发行版以及其他操作系统。语言小而快。

### 特性

Simple 具有每种编程语言都具有的特性，比如变量声明、函数式编程、面向对象编程等等。它包含了所有这些特性，因为它的父架构来自于 **C** 。Simple 是弱类型的，不需要显式的行尾，可以嵌入到 C 和 C++项目中。javascript 的可移植性是我们正在努力并打算实现的一个主要目标。

### 下载&安装简单。

Simple 拥有可以安装在我们的 Windows/Linux/Mac 系统中的编译器。Simple 可以被克隆，以 zip 格式下载或者从[这里](https://simple-lang.sourceforge.io/)
下面是分别在每个系统上安装的步骤。

#### 在 Windows 上安装。

这是非常容易的安装。从[这里](https://simple-lang.sourceforge.io/)下载 windows 类型文件并运行。

#### 在 Linux 上安装

从上面列出的任何一种方法下载 simple 后，从您的控制台解压缩/移动到 SIMPLE 文件夹，并按照下面的说明操作。

```
# Copy make file from bootsrc/makefiles/inux to sources
$ cp bootsrc/makefiles/linux/Makefile bootsrc/sources
# install simple with the "make" command.
$ make 
```

等待片刻，可能是一分钟。make 文件安装在位于 **bootsrc** 文件夹中的文件夹 *dist* 中。用以下命令启动简单的编译器

```
./simple 
```

##### 出错了？

是否有错误说明**很简单，所以找不到**？。每次通过控制台进入文件夹后，请使用此命令。

```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:./ 
```

##### 无法添加到全局路径。

遗憾的是，我们正在解决这个问题，并将在下一个版本中发布修复程序。

#### 在 Mac 上安装

要在 Mac 上安装，遵循相同的步骤，但有一些小的不同。安装步骤..
下载 Mac 包后，导航到 SIMPLE 和 run 命令的主文件夹。

```
# Copy make file from bootsrc/makefiles/inux to sources
$ cp bootsrc/makefiles/mac/Makefile bootsrc/sources
# install simple with the "make" command.
$ make 
```

嘣，我们简单安装了。

### 入门。

当正式文档出现时，您可以查看非官方文档/指南。在这里 熟悉 SIMPLE 的语法

 **### 贡献者。

我们喜欢并寻找有助于 SIMPLE 开发的贡献者。这是一个开源项目，需要社区的广泛支持。查看[投稿指南](https://github.com/simple-lang/simple/contributors.md)**