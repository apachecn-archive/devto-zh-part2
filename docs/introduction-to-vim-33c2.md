# Vim 简介

> 原文：<https://dev.to/fabiorosado/introduction-to-vim-33c2>

这篇文章最初是为我参与的一个开源项目而写的。项目创建者建议我应该在博客上发布，所以我把帖子扩展了一下，提交到我的个人博客。我决定与你分享它，我希望你喜欢它！

Vim 代表 vi improved，是一个命令行文本编辑器，除了 windows 之外，它随每个平台一起安装(除非您安装了它)。很难弄清楚如何在这个编辑器中做事情，这篇文章旨在给你 vim 的基本知识以及如何用它做事情。

让我们从基础开始。怎么打开 vim？

如果您运行的是 Linux 或 MacOS，很可能您已经安装了 vim，因此您可以打开终端并运行命令`vim <filename>`来编辑该文件。

*注意:您只能编辑工作目录中的文件。*

如果你用的是 windows，你可以从 vim.org 下载。

# 我来模式

Vim 有三种不同的模式:

*   正常模式(默认模式)
*   插入模式(按下 *i* 键)
*   视觉模式(按下 *v* 键)

这些模式很容易理解，你可以在终端窗口的左下角看到你处于哪种模式。

正常模式是默认模式，当你打开 Vim 时，你可以四处移动，阅读文本，复制，插入行等。在这种模式下，你唯一不能做的事情就是编辑文本。

## 编辑文本

如果你想编辑文本，你可以按下键盘上的 *i* 键，你可以注意到窗口的左下角现在显示了文本:- INSERT -。

您现在可以将您的终端用作文本编辑器，您可以像在任何其他文本编辑器中一样添加、编辑和删除文本。

一旦您对刚才所做的更改感到满意，按下键盘上的 *esc* 键，您将返回到正常模式。

## 视觉模式

这种模式没什么好说的。它允许你选择大块的文本，以便你复制或剪切。当此模式激活时，您可以阅读窗口左下角的文本:- VISUAL。

在可视模式下，您可以做的另一件事是突出显示文本，然后对突出显示的文本进行小的更改，如更改为大写或缩进行。

# Vim 命令

Vim 是为了帮助你不需要鼠标就能相当快速地做事情。一切都可以通过键盘完成，因此学习一些 Vim 命令将会很有帮助。

你将学到的东西:

*   如何拯救生命
*   如何退出 Vim
*   如何四处走动
*   如何查看行号

注意:Vim 有很多不同的命令和组合，这只是一个介绍，如果你想学习如何正确使用 Vim，你应该阅读其他资料。

## 保存并退出 Vim

现在你已经知道了如何在 Vim 中编辑文本，你要学习的最重要的事情就是如何保存你的修改并退出。如果您按下键盘上的 *:* 键，您将能够向 Vim 输入命令。

要保存一个文件，你需要做的就是键入`:w`，然后按回车键。

要退出一个文件并回到命令行，你需要键入`:q`。

请注意，如果您对文件进行了更改，但没有保存，Vim 不会自动退出，相反，它会告诉您运行命令`:q!`，这基本上意味着强制退出。

这两个命令可以合并成一个`:wq`。这将把更改写入文件，然后退出 Vim，如果你想更快，组合键`shift+zz`将获得相同的结果。

您还可以使用命令`:w <filename>`以该名称保存一个新文件——如果您没有用 vim 打开文件，这很有用。

## 行号

编辑文件时，显示行号非常有用。VVim 允许你直接跳到一行，如果你知道它的编号，那么你的编辑可以更快的完成，如果你知道在哪里编辑什么的话。

要显示行号，您需要运行命令`:set number`，一旦您按下 enter，您可以看到 Vim 将显示每一行的编号。

如果你想直接跳到一行，你可以输入命令`:<line number>`，光标会跳到那一行的开头。

## 四处移动

要四处移动，你只需要按几个键来做不同的事情。四处移动不需要你按`:`键进入命令模式。

*   `h`或`arrow left` -将光标向左移动一个字符
*   `l`或`arrow right` -将光标向右移动一个字符
*   `j`或`arrow down` -将光标下移一行
*   `k`或`arrow up` -将光标向上移动一行
*   `0` -将光标移动到行首
*   `$` -将光标移动到行尾
*   `w` -将光标向前移动一个单词
*   `b` -将光标向后移动一个单词
*   `gg` -移动到文件的开头(第 1 行)
*   `G` -移动到行尾(最后一行)

移动/编辑文件时可以使用这两个命令:

*   `o` -在光标下方添加一个空行，将光标移动到该行，进入编辑模式
*   `O` -在光标上方添加一个空行，将光标移动到该行，进入编辑模式

## 删除事物

你可以通过按下`d`键删除东西，这也将作为*剪切*命令。Vim 允许您组合命令来实现一个目标，因此您可以将移动命令与删除命令组合起来以提高您的编辑技能。

*   `de` -从光标所在位置开始删除，直到当前单词结束
*   `dw` -删除到下一个单词
*   `d2w` -从光标处删除两个单词
*   `d$`或`D` -从光标所在位置删除到行尾
*   `dd` -删除整行

## 其他有用的命令

由于我们倾向于删除错误的内容，vim 还附带了一个撤销和重做命令，这在很多情况下都很有用。

*   `u` -撤销之前的命令/动作
*   `CTRL-u` -重做之前的命令/动作
*   `.` -重复最后一个修改了某些东西的命令(如 2dd -删除两行)

您还可以使用以下命令让 vim 在整个文件中搜索一个术语:

*   `:?<term>` -从光标向下搜索该术语
*   `:\<term>` -从光标向上搜索该术语
*   `n`或`/` -转到下一个搜索项
*   `N`或`?` -转到上一个搜索项

## 结论

对 Vim 的介绍到此结束，希望您会觉得有用。您仍然需要学习很多东西，但是这应该为您在这个文本编辑器中工作提供了基础。

如果您想了解更多关于 Vim 的信息，可以在终端上运行以下命令来运行 Vim tutor:

`$ Vimtutor`

这将打开一个文本文件，其中包含了 Vim 中所有基本命令的分步说明。

Andrea Benfatti 在 Twitter 上表示，https://vim-adventures.com对任何刚接触 vim 的人都有好处。我以前不知道这个网站，但我检查了一下，它看起来确实是一个非常有趣的方法，可以用这个游戏练习很多 vim 命令，所以一定要检查一下！