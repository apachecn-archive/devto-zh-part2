# 当你跑 sudo 的时候会发生什么！！`?

> 原文：<https://dev.to/captainsafia/what-happens-when-you-run-sudo--2ah7>

我相信每个人都在命令行上做过的一件事就是使用`!!`快捷键来运行之前用 sudo 运行的命令。

```
$ gimme-the-secrets
You can't run this command.
$ sudo !!
The answer to life, the universe, and everything is 42. 
```

这篇博文与我通常做的有点不同，因为`!!`快捷方式的逻辑没有利用任何系统调用，所以查看堆栈跟踪不会产生任何启发性的东西。

`!!`命令利用了`history`文件，Bash 在该文件中维护用户执行的命令的记录。您可以通过运行以下命令找到该文件的位置。

```
$ echo $HISTFILE
/home/captainsafia/.bash_history 
```

我通过在`!`上运行`type`开始调查。

```
$ type !
! is a shell keyword 
```

这是有道理的。`!`是一个外壳级结构，是外壳语法的一部分。我怀疑 shell 设法替换了任何引用“！”的命令执行前面的命令。实际上，我对这件事还是很困惑。我决定跳到 [bash 代码库](https://github.com/bminor/bash)来看看我是否能对此进行一点剖析。

我从寻找“！”的参考资料开始在代码库中，但是没有找到任何东西，因为它可能是用一个单词引用的，而不是符号(正常意义上的符号，不是计算机科学意义上的符号)。我试图寻找“感叹词”和“关键字”，但一无所获。后来我终于意识到，它可能会在代码库中使用“bang”来引用，并从中获得了一些有用的片段。

原来，将`!`扩展为对历史中元素的引用的能力是由可以传递给新 bash 实例的`-H`标志控制的，并将`BASH_HISTORY`标志设置为 true。

更多的窥探引导我找到了源代码中的`bashhist.c` [文件](https://github.com/bminor/bash/blob/7de27456f6494f5f9c11ea1c19024d0024f31112/bashhist.c)，它管理与历史文件的交互。我开始四处窥探这个文件，看看我是否能找到任何负责采取“！”并适当地替换它。

我在处理扩展的 [`pre_process_line`](https://github.com/bminor/bash/blob/7de27456f6494f5f9c11ea1c19024d0024f31112/bashhist.c#L517) 中发现了一个有希望的结果到最近的历史条目中。该函数检查当前 bash 会话中是否启用了历史扩展，并调用`history_expand`。

```
# if defined (BANG_HISTORY)
  /* History expand the line. If this results in no errors, then add that line to the history if ADDIT is non-zero. */
  if (!history_expansion_inhibited && history_expansion && history_expansion_p (line))
    {
      expanded = history_expand (line, &history_value); 
```

我通读了`history_expand`函数的代码，它基本上翻译了所有不同形式的“！”那可以用，像”！!"访问最后一个命令或"！2”来访问历史文件中的第二个命令，转换成被引用的实际命令，并将它们作为要由 shell 解析的新字符串返回。

总之，当你输入“sudo！!"会发生以下情况。

1.  Bash 开始预处理该行。
2.  如果启用了历史扩展，Bash 会传递“！!"返回历史文件中最后一项的`history_expand`函数的字符串。
3.  Bash 开始预处理新行。因为其中没有历史扩展字符串，所以它只执行其中引用的命令。
4.  鳍。

当然，还会有更多的边界检查和其他业务发生，但是上述步骤涉及到了流程的实质。

下期帖子再见！