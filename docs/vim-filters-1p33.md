# Vim 过滤器

> 原文：<https://dev.to/rpalo/vim-filters-1p33>

快速提示时间！让我们来谈谈 Vim 过滤器。这是一种执行任何外部命令并将结果传送到当前缓冲区的方法。这是一个很好的方法，可以让您的 shell 在 vim 中发挥威力，而无需学习太多 VimScript。以下是基本情况:

## 读取命令输入到当前缓冲区

这是最简单的方法:

```
:r !ls 
```

Enter fullscreen mode Exit fullscreen mode

该命令将把`ls`的结果读入光标所在位置的缓冲区。您也可以指定要在其后插入的特定行。第 4 行之后，下一个命令将把`ls`命令的结果读入当前缓冲区。

```
:4r !ls 
```

Enter fullscreen mode Exit fullscreen mode

## 发送缓冲区内容作为外部命令的输入

您还可以将缓冲区中的行发送出去，用命令的结果来替换。一个常见的做法是操作您正在处理的整个缓冲区。

```
:%!sort 
```

Enter fullscreen mode Exit fullscreen mode

`%`选择整个缓冲器，然后`!`将选择的行发送到外部`sort`命令。整个缓冲区内容将被替换为该命令的结果。

比如说。假设您正在处理一个文本文件。

```
# Attention, Everyone!

This markdown file contains some **pretty interesting** stuff.

I __mean__ it. 
```

Enter fullscreen mode Exit fullscreen mode

但是有些东西不太对劲。它需要更多的变焦-更多的晦气！试着把它和光滑的红宝石眼线笔结合起来！

```
:%!ruby -ne 'puts $_.upcase' 
```

Enter fullscreen mode Exit fullscreen mode

突然，你的缓冲区的内容是:

```
# ATTENTION, EVERYONE

THIS MARKDOWN FILE CONTAINS SOME **PRETTY INTERESTING** STUFF.

I __MEAN__ IT. 
```

Enter fullscreen mode Exit fullscreen mode

这将适用于任何命令。您可以使用 shell 命令，也可以通过 Python 或节点脚本运行它。它让您能够为工作选择最好的(或您最喜欢的)工具，而不是将您局限在 Vim 的能力范围内。如果你只是想替换一部分缓冲，你可以使用其他的动作。

```
:!!ruby -ne 'puts $_.upcase' 
```

Enter fullscreen mode Exit fullscreen mode

两个感叹号将在当前行起作用。

它也适用于视觉上选择的线条。在可视模式下选择一对线:

```
v         " Visual mode activated
jj        " Select next two lines
:!sort    " Sorts the lines that were selected. 
```

Enter fullscreen mode Exit fullscreen mode

如果你不小心运行了一个命令，不知何故把你的文件吹走了，不要惊慌。在正常模式下，只需按`u`即可取消操作。

希望这能派上用场！

* * *

*原帖 [`assert_not magic?`](https://assertnotmagic.com/2018/03/18/vim-filters/)*