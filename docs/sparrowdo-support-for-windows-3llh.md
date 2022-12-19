# 对 Windows 的 Sparrowdo 支持

> 原文：<https://dev.to/melezhik/sparrowdo-support-for-windows-3llh>

# 你好，我是斯帕罗多

Sparrowdo 是一个基于 Perl6/Perl5 编写的通用任务运行器和 CM 工具。它以高效简单的方式实现了配置和自动化任务。

我最近的提交使得在 Windows 上运行传统上只支持 Linux 的 Sparrowodo 工具成为可能。人们可能需要它的原因:

*   像 Ansible 和 Chef 这样的替代软件对 Windows 世界并不友好。

*   Ansible 要求在安装过程中使用 cygwin 进行[黑客攻击，因此不提供无缝安装路径。](http://www.oznetnerd.com/installing-ansible-windows/)

*   当你只需要运行自动化任务而不维护远程 cookbooks 服务器时，默认模式下采用客户端-服务器安装方法带来的开销似乎太大了。

*   那么为什么不用更自然、更轻盈的东西呢？

Sparrowdo 下面是一个任务运行器，它只需要在您的系统上安装 Perl5/Perl6，我将在几个段落中展示这是多么简单！

# 安装 Sparrowdo

今天 Sparrowdo 的 Windows 支持是实验性的，一旦我看到它变得稳定，我会将代码合并回主流库。现在，这意味着我们将从分支安装应用程序，尽管这并不困难。

## 安装 Perl5

在 Windows 上安装 Perl 最简单的方法是 [ActiveState Perl](https://www.activestate.com/activeperl) 或者你可以试试 [Strawberry Perl](http://strawberryperl.com/) ，它在 Windows 上使用 Perl 的人中也很受欢迎。这两种方法都应该可行。我们需要 Perl5 来安装 Sparrowdo 客户机 Sparrow。

## 安装 Perl6

一旦安装了 Perl，我们需要安装 Perl6 部分，因为 Sparrowdo 本身就是在 Perl6 上编写的。Perl6 作为 [Rakudo 编译器](https://rakudo.org/files/)提供，安装过程应该非常简单。

## 安装麻雀

Sparrow 是运行 Sparrowdo 任务的轻量级客户端，我们将从 GitHub 分支安装 2 个 Sparrow 组件，一旦我找到稳定的东西，我会将“Windows”代码合并回主流分支，同时与主流版本安装相比，它只是多了几个命令。

首先，让我们安装 cpanm 客户端，因为它是安装 CPAN 模块非常方便的工具:

```
$ cpan install App::Cpanminus 
```

Enter fullscreen mode Exit fullscreen mode

然后从 GitHub 分支安装 Outthentic 和 Sparrow 模块:

```
$ git clone https://github.com/melezhik/outthentic
$ cd otthentic 
$ git checkout windows
$ cpanm . --notest

$ git clone https://github.com/melezhik/sparrow
$ cd sparrow
$ git checkout windows
$ cpanm . --notest 
```

Enter fullscreen mode Exit fullscreen mode

在这些步骤之后，如果一切正常，我们将能够从命令行运行 Sparrow 客户端:

```
$ SET USER=melezhik
$ sparrow 
```

Enter fullscreen mode Exit fullscreen mode

该命令应该没有错误地退出。

现在，我们需要安装的最后一点是用于 Windows 的 Sparrowdo:

## 安装 Sparrowdo

```
$ git clone https://github.com/melezhik/sparrowdo
$ cd sparrowdo
$ git checkout windows
$ zef install --/test . 
```

Enter fullscreen mode Exit fullscreen mode

至此，我们已经准备好测试 Sparrowdo/Sparrow 安装，让我们运行第一个任务。仅仅创建目录怎么样？不是火箭科学，而是一个例子:

```
$ notepad.exe sparrowfile

#!perl6

directory 'C:\Users\melezhik\Hello-Sparrowdo';

$ sparrowind --no_color
running sparrow tasks on localhost ...
target OS is - windows
push [task] create directory C:\Users\melezhik\Hello-Sparrowdo OK
sparrow root: [C:/.sparrowind/sparrow]
C:/.sparrowind/sparrow/sparrow.index updated OK from https://sparrowhub.org
set up task box file - C:/.sparrowind/cache/task-box.json - OK
sparrow root: [C:/.sparrowind/sparrow]
public@directory is uptodate (0.2.0)
running task box from C:/.sparrowind/cache/task-box.json ...
@ directory manager
2018-09-10 22:00:12 : [task] create directory C:\Users\melezhik\Hello-Sparrowdo [path] modules/windows/create/
ok      scenario succeeded
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

好的，就像我说的，这个过程非常简单。至于剩下要做的事情，请看最后一节。

# 进一步的计划

## 改编麻雀插件

Sparrowdo 附带了相当多的插件来解决许多开箱即用的通用任务，其中许多插件是专为 Linux 编写的。增加 windows 支持并不难，但是需要时间。如果有人感兴趣，请告诉我。

## 打磨粗糙的边角

像 ansi 彩色输出之类的。同时只需使用`no_color`选项。(-;但我保证我会把事情做得更漂亮。

## 合并代码回主回购

可能是最简单的部分。

# 如此...

如果有人对使用 Perl 的 Windows 自动化感兴趣，欢迎来到 Sparrowdo 项目！

通过此处的评论或 gh 页面向您发送反馈、请求或问题。

最好的。

阿列克谢