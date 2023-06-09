# 如何在 Android 上运行 C 程序

> 原文：<https://dev.to/bauripalash/how-to-run-c-programs-on-android-3cco>

[![](img/f846682072b6ae673a82b4fe82d4e2b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DTvPIkOT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://palash.tk/asseimg/conandroid.png)

c 是一种众所周知的编程语言，由 Dennis Ritchie 于 1969 年至 1973 年间在 T2 贝尔实验室创造，从那时起，它就成为了有史以来使用最广泛的编程语言之一。

它通常用于低级编程，例如为从[超级计算机](https://en.m.wikipedia.org/wiki/Supercomputer)到[嵌入式系统](https://en.m.wikipedia.org/wiki/Embedded_system)的计算机开发[操作系统](https://en.m.wikipedia.org/wiki/Operating_system)、[驱动程序](https://en.m.wikipedia.org/wiki/Device_driver)，以及各种[应用软件](https://en.m.wikipedia.org/wiki/Application_software)。

这种语言已经可以在非常广泛的平台上使用，从嵌入式微控制器到 T2 超级计算机。

[![The C Programming](img/43dd4997616bc43a1cddeea9c1a7ccbd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Pf59Vf7p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/thumb/3/35/The_C_Programming_Language_logo.svg/452px-The_C_Programming_Language_logo.svg.png)

> Android 基于 Linux 内核，因此在 Android 上编译和运行 C/C++程序是完全可能的。

C 相当[跨平台](https://en.m.wikipedia.org/wiki/Cross-platform)，所以一个用 Windows 写的 C 程序可以在 Linux(和 android)上运行，反之亦然。

> 特别提示:你可能想知道为什么我包括了 [C++](https://en.m.wikipedia.org/wiki/C%2B%2B) ，这里我们应该只关注 C。
> 原因是，C++实际上是作为 C 编程语言的超集开发的，现在没有人专门为 C 构建[编译器](https://en.m.wikipedia.org/wiki/Compiler)。所以 C 编译器也可以编译 C++程序

### 如果你是 C 编程新手或者想开始学习 C 编程，我推荐你阅读我们的文章，[C 编程入门](https://palash.tk/Baby-Steps-In-C-Programming)

因此，随着讨论的深入，让我们开始寻找在 Android 中运行 C/C++程序的方法

## 【T10】1cxx droid

CXXDroid 由 IIEC 开发，是完全成熟的 Android C/c++集成开发环境。它有下面列出的非常强大的功能

*   完全离线编译器-不需要互联网
*   可用于获取库的包管理器
*   强大的编辑器
*   C/C++解释器(REPL)
*   代码示例

[![CXXDroid - Run C programs on Android](img/f1f38bd1a1a58c428a55d8aab6323c01.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ms9xcrJ---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://fsgh.palash.tk/imgs/cxxdroid.jpg)

来自 Play Store 的 cxx droid
{:。crtx}

如果想学习和实验 C/C++，我推荐使用 CXXDroid。

#### 安装自:[播放商店](https://play.google.com/store/apps/details?id=ru.iiec.cxxdroid)

## #2 CppDroid

CppDroid 由 Anton Smirnov 开发，是一个非常著名的 Android C/c++集成开发环境，它健壮可靠，具有许多特性，

*   完全离线的 C/C++编译器——无需互联网
*   智能语法突出显示
*   自动缩进
*   主题

[![CppDroid - Run C & Cpp programs on Android](img/96328d965a63316ad94e2e407c7d7510.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PBZYthVK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://fsgh.palash.tk/imgs/cppdroid.jpg)

来自 Play Store 的 CPP droid
{:。crtx}

如果你想要一个完整的 C/C++开发工作空间，我推荐使用 CppDroid。但是现在它有一个缺点，自 2017 年 8 月 17 日以来，它没有在 Play Store 中更新过

#### 从:[游戏商店](https://play.google.com/store/apps/details?id=name.antonsmirnov.android.cppdroid)安装

访问他们的官网:[https://www . CPP droid . info](https://www.cppdroid.info)

## #3 Termux

Termux，在我们之前的文章中也提到过的一体化解决方案， [Python For Android -在 Android 中运行 Python 程序](https://palash.tk/Python-For-Android-Run-Python-Programs-In-Android)

正如前一篇文章中提到的，这是一个 Android 终端模拟器，这意味着在它的帮助下，我们可以运行任何 linux(几乎任何😉)安卓上的程序。

[![editing C scripts in vim](img/5d0509baeb832df23d3b1a644b36c40f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bSgEM5b9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://termux.com/files/vim-main_framed.png)

termux.com
{:。crtx}

要在 Termux 中安装和使用 C/C++编译器(在 Termux 中 clang 就是 C/C++编译器)，

1.  从[游戏商店](https://play.google.com/store/apps/details?id=com.termux)下载并安装 Termux
2.  安装后执行该命令`pkg install clang`
3.  成功安装`clang`后，您可以编译 C/C++脚本。

例子:
`clang hello.c`
之后你会得到一个可执行文件`a.out`，你可以使用`~$./a.out`来运行它

> 要编辑和创建 C/C++脚本，您可以使用 termux 中的 [**VIM**](https://en.m.wikipedia.org/wiki/Vim_(text_editor)) ，它可以与`pkg install vim`
> 一起安装，或者您可以使用 [QuickEdit](https://play.google.com/store/apps/details?id=com.rhmsoft.edit)

如果你是编程新手，从未使用过命令行应用程序，我不建议你使用 Termux，而是使用 **CXXDroid** 或 **CppDroid** 。

所以朋友们，就这样吧，下次见。有疑问、建议吗？让我知道下面的评论👇

* * *

如果你喜欢我的作品(我的文章、故事、软件、研究等等),考虑给我买一杯☕咖啡🤗