# 修复腐败的 ZSH 历史

> 原文：<https://dev.to/rishibaldawa/fixing-corrupt-zsh-history-4nf4>

今天，当我登录我的控制台时，我看到了下面的错误信息

```
zsh: corrupt history file /home/rishi/.zsh_history 
```

Enter fullscreen mode Exit fullscreen mode

通过快速搜索，我能够找到一个 [stackoverflow 链接](https://superuser.com/a/957924) <sup id="fnref1">[1](#fn1)</sup> ，它推荐我运行下面的命令集

```
mv .zsh_history .zsh_history_bad
strings .zsh_history_bad > .zsh_history
fc -R .zsh_history 
```

Enter fullscreen mode Exit fullscreen mode

我运行了它们，一切又变得非常方便。这让我想知道这些命令是做什么的。

## 重命名

```
mv .zsh_history .zsh_history_bad 
```

Enter fullscreen mode Exit fullscreen mode

玩过 linux 的人应该都很熟悉。它将文件从一个路径移动到另一个路径。也经常被用来作为一个便宜的是重命名文件，就像我们在这里做的。关于 move 的更多细节请参见[手册页](https://linux.die.net/man/1/mv)。

## 消毒

```
strings .zsh_history_bad > .zsh_history 
```

Enter fullscreen mode Exit fullscreen mode

弦乐很有趣。我以前没有遇到过它。基于[手册页](https://linux.die.net/man/1/strings):

> ...strings 打印长度至少为 4 个字符(或以下选项给出的数字)的可打印字符序列，后跟一个不可打印的字符...

看来`strings`是一种在文件中获取可打印字符的好方法。在我们的例子中，这意味着如果`.zsh_history`由于不可打印的字符 <sup id="fnref2">[2](#fn2)</sup> 而损坏，这将有助于解析它们。这也意味着，如果您运行了任何带有不可打印字符的命令，那么它们也将从您的历史记录中消失。我不太可能运行可打印的字符，所以这不是一个大问题，但这可能会使 QAs 和笔测试人员过去的命令有点烦人。

## 解析

```
fc -R .zsh_history 
```

Enter fullscreen mode Exit fullscreen mode

很有趣。它不会出现在联机帮助页中。我最终搜索了相当多的时间才意识到这是一个 zsh 内置的特性。在 [zsh 文档](http://zsh.sourceforge.net/Doc/Release/Shell-Builtin-Commands.html)中有一些关于它的信息。

> ...fc -R '从给定文件中读取历史记录

看来`fc`会和你的 zsh_history 互动。在本例中，我们从文件中读取数据，因为没有提到第二个文件，所以将它放回同一个历史文件中。即通过解析同一文件重写历史。

## 清理

```
rm .zsh_history_bad 
```

Enter fullscreen mode Exit fullscreen mode

这不是原始脚本的一部分，但你应该总是清理自己。该命令删除您在第一步中创建的 tmp“坏”文件。

## 结论

如果我在 zsh 还在写内容的时候关闭我的 VM / linux 机器，我仍然可以破坏 zsh_history。只要我不在乎过去任何其他不可打印的字符，并开放继续使用 zsh，我就可以运行上面的命令回到更快乐的阶段

* * *

1.  严格来说不是 stackoverflow 但是我希望你明白我的意思。 [↩](#fnref1)

2.  可能是因为我在一台小型虚拟机上进行负载测试时关闭了电源。 [↩](#fnref2)