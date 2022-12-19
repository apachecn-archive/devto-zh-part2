# 像老板一样操纵文本

> 原文：<https://dev.to/nickymeuleman/manipulate-text-like-a-boss-321n>

在听 VSCode 上的语法播客[第](https://syntax.fm/show/048/vs-code-round-two)集时，使用键盘快捷键出现了。这启发了我去更深入地挖掘并写下它！

当你花大量时间处理文本时，移动和处理文本会占用你大量的时间。键盘快捷键大大加快了文本相关的工作流程。

我并不提倡完全抛弃你的鼠标。花些时间学习如何使用键盘快捷键而不是鼠标来做你经常重复的操作。你现在所做的投资将很快得到十倍的回报。

## 基础知识

当我开始学习如何编码时，我希望有人告诉我要真正熟悉这些。他们不经常被谈论，因为许多人认为他们*太*基本。我习惯用箭头键一次一个字符地浏览文本。知道了这些快捷方式中的大部分并没有转化为使用它们，直到最近，我仍然出于习惯使用我的鼠标进行这些操作。使用这些，直到你可以从肌肉记忆中激活它们，这将节省你很多时间。

> Mac 用户:用 ctrl 代替 command

按住`ctrl`键影响单词而不是字符。

*   `ctrl+left`将光标移动到前一个单词的开头。
*   `ctrl+right`将光标移动到下一个单词的开头。
*   `home`将光标移动到行首。
*   `end`将光标移动到行尾。
*   `ctrl+home`将光标移动到文件的开头。
*   `ctrl+end`将光标移动到文件的末尾。
*   擦除上一个单词。
*   擦除下一个单词。

当光标移动时，使用`shift`进行选择。

*   `shift+left/right`选择字符
*   `shift+up/down`上下移动光标并选择中间的所有内容
*   `ctrl+shift+arrows`选择单词
*   `shift+home/end`选择直到行首/行尾

## 升一级

你几乎可以在任何地方使用上面的快捷方式。接下来的问题更具体地针对代码编辑器。

> 这里列出的快捷方式是默认的，它们可能会因您而异。

我使用的是 [VSCode](https://code.visualstudio.com/) (在 Windows 上)，但是它们中的许多也可以在其他编辑器中使用。您可以在 VSCode 中查看键盘快捷键，方法是打开命令调板(`ctrl+shift+p`)并选择“首选项:打开键盘快捷键”。在这里，您可以搜索和编辑 VSCode 的每个键盘快捷键。

对于 Windows、T2、Mac 和 T4 的 Linux 来说，这里有一个有用的宝库。

以下是我常用的。

### 线冒泡

向上或向下移动/复制一行。(病名[@维斯博斯](https://twitter.com/wesbos))

*   `alt+up/down`移动当前行。
*   `shift+alt+up/down`复制您当前的线路。

> 注意:您不必选择整条线来使这些工作。

[![Line bubbling](../Images/d95964bf559abbd4b7de3af761c52e11.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XKUgtu0G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://nickymeuleman.netlify.com/line-bubbling-c27efa8831b5b89836be762964f1a27c.gif)

### 扩大/缩小选择范围

将您的选择扩展/缩小到下一个逻辑点

*   `alt+shift+right`扩展选择。
*   `alt+shift+left`缩小选择范围。

[![expand/shrink selection](../Images/33dd9bed3959a5b91d3f15838dac5197.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jfRgV7YK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://nickymeuleman.netlify.com/expand-shrink-selection-dcb22210e49087b4b7110d22d9edc52c.gif)

### 重命名符号

重命名函数/变量/...可能是一个苦差事，你不想错过一个单一的实例，它可能会打破你的整个项目，使小狗伤心！

*   `F2`重命名高亮显示的符号。

[![rename symbol](../Images/4c26a476f3d14bc303db1d26b648e665.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7z6EPCiu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://nickymeuleman.netlify.com/rename-symbol-8f38fc8a4f02e137219781463befe9a0.gif)

### 其他收藏夹

*   `ctrl+d`将选择添加到下一个查找匹配项。选择文本后，选择下一个匹配的文本。当光标在一个符号上时，选择下一个匹配的符号。

[![add next match](../Images/260ea59863f11f42ef8bf4db4df61f75.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--R1DD9Ogy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://nickymeuleman.netlify.com/add-next-match-7942a84370955f6035ba08e3f919ed22.gif)

*   `ctrl+/`(azerty 键盘上的`ctrl+:`)切换当前行的注释。
*   `ctrl+l`选择当前行。
*   `ctrl+shift+k`删除当前行。
*   `ctrl+enter`在下面插入一行。
*   `ctrl+shift+enter`在上面插入一行。当您的插入符号位于一行的中间时非常有用。
*   `ctrl+``切换集成终端。(`ctrl+ù`在 azerty 键盘上)
*   `ctrl+b`切换侧边栏可见性。

节省一些屏幕空间，切换侧边栏。`ctrl+0`将打开侧边栏并聚焦它(`ctrl+à`在 azerty-keyboards 上)
[![toggle sidebar](../Images/b52ef61e42ea85759e5ec66140510994.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Pg3IRcUq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://nickymeuleman.netlify.com/toggle-sidebar-32c55a0521b62ed64e244571acf08efb.gif)

## 绕开 VSCode

### 命令调色板

许多人可能从崇高的文本中知道这个伟大的特征。

根据您当前的上下文访问可用的命令。如果你忘记了一些快捷方式，不要担心。此功能支持您，并将在命令旁边显示快捷方式。我真的很喜欢从这里开车。

*   `ctrl+shift+p`

### 转至文件

快速搜索并打开文件。

*   `ctrl+p`

### 编辑群组

> 自撰写本文以来，VSCode 围绕这一点引入了新的特性。下面的快捷方式应该仍然有效，但是我鼓励你查看[他们的发布说明](https://code.visualstudio.com/docs/getstarted/userinterface#_grid-editor-layout)以获得更多信息。

编辑器组是指 VSCode 主窗口中的窗口。它们可以包含一组项目(选项卡)。

#### 开到一边

您可以立即在新的编辑器组中打开新文件。这可以通过侧边栏或快速打开功能来实现。

*   `ctrl+enter`

#### 在编辑器组中循环切换标签页

*   `ctrl+tab`在该编辑器组中打开下一个最近使用的标签
*   `ctrl+shift+tab`在该编辑器组中打开以前最近使用的标签

#### 关注一个编辑组

您可以使用`ctrl`和数字键更改当前聚焦的编辑器组

*   `ctrl+1/2/3`(azerty 键盘上的`ctrl+&/é/"`)

#### 移动编辑到一个编辑组

使用`ctrl/alt`和箭头键将编辑器移动到上一个/下一个组。

*   `ctrl+alt+left/right`

我肯定我漏掉了很多你最喜欢的，请让我知道它们！