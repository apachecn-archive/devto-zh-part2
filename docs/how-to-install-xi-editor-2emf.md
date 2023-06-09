# 如何安装 Xi 编辑器

> 原文：<https://dev.to/asamolion/how-to-install-xi-editor-2emf>

[![Xi](img/175f5c9bd0b1951f0035928db16d2e3b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Eu2U4X3N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9llpwpvnezugz963mlfs.png)

我最近浏览了尼基塔·普罗科波夫的这篇博客文章。这篇文章让我大开眼界，也让我松了一口气，终于有人指出了大多数现代应用程序设计中的极端缺陷。

Nikita 在他的帖子中提到了一些注重性能并且正在积极开发的项目。其中一个引起了我的注意。

当然，这是一个文本编辑器。《Xi》编辑由[拉夫·莱维恩](https://levien.com/)构思，专注于所有正确的东西。

*   高性能
*   可靠性
*   和可定制性

它是用 Rust 构建的，还处于开发的早期阶段，所以现在还不能使用。目前没有可用的安装程序或软件包。安装它的唯一方法是直接从源代码开始构建。

我花了一些时间来弄清楚如何安装和运行 Xi，所以我决定写一个帖子来方便其他人。

Xi 基于客户机-服务器模型。有一个被称为`xi-core`的后端进程，它基本上在后台运行，负责所有文件的编辑。

## 安装后端

Xi 后端只是一个在后台运行的进程。要安装和编译它，请确保您的系统上安装了最新版本的 [Rust](https://www.rust-lang.org/) 。
第一步是简单地将 Xi 回购克隆到你机器上的一个文件夹中。

```
$ git clone https://github.com/xi-editor/xi-editor.git 
```

Enter fullscreen mode Exit fullscreen mode

导航到`xi-editor`回购、
内的`rust`文件夹

```
$ cd xi-editor/rust/ 
```

Enter fullscreen mode Exit fullscreen mode

并运行 build 命令编译 Xi，

```
$ cargo build 
```

Enter fullscreen mode Exit fullscreen mode

## 安装前端

以上命令将安装 Xi 的后端。要实际使用这个编辑器，你需要安装官方报告中给出的[多个前端](https://github.com/xi-editor/xi-editor#building-the-core)中的一个。出于本教程的目的，我将安装 [gxi](https://github.com/bvinc/gxi) 前端。它没有太多的功能，但是它能让我们编辑和保存基本文件。

要安装 gxi 前端，将其克隆到您选择的文件夹中

```
$ git clone https://github.com/bvinc/gxi.git 
```

Enter fullscreen mode Exit fullscreen mode

安装依赖项，

```
$ sudo apt-get install libgtk-3-dev 
```

Enter fullscreen mode Exit fullscreen mode

现在导航到`gxi`文件夹，简单地运行`run`命令，

```
$ cd gxi
$ cargo run 
```

Enter fullscreen mode Exit fullscreen mode

这将打开一个基本的文本编辑器，有点像 Windows 上的记事本，只是功能更少。正如我之前所说的，Xi 在这一点上并不真正有用，但我对未来充满希望。我希望我最终能够得到一个比 Sublime Text 更快并且是开源的编辑器。