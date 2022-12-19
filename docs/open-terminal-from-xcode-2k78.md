# 从 Xcode 打开终端

> 原文：<https://dev.to/itscoderslife/open-terminal-from-xcode-2k78>

我喜欢 Xcode 作为 macOS 和 iOS 开发的 IDE。但是它有自己的一套限制。我非常怀念的一个特性是从 Xcode 打开一个**终端**窗口，进入项目的同一个目录。这将节省很多时间。

每当您想要运行 git 命令或 cocoapods 或任何 xcode 构建命令时，手动打开终端并将目录更改为项目目录至少需要 30 秒到 1 分钟。如果我说你能做到呢

> 现在，如果我说你可以打开一个终端窗口，在 **1 秒**内进入项目目录，或者用快捷键，会怎么样？
> 
> 是啊！一条捷径。

这是你如何做它。这是一次性设置。

1.  创建一个 2 行 shell 脚本，并授予文件执行权限。
2.  前往 Xcode 偏好设置。
3.  在 Xcode 中添加行为。给它起个名字，给个快捷键。
4.  在右侧的详细信息窗格中，选中运行选项。
5.  从相邻的下拉菜单中选择您刚刚在步骤 1 中保存的脚本。

这是关于如何做的视频。
[https://www.youtube.com/watch?v=MMeu3xeXZxM](https://www.youtube.com/watch?v=MMeu3xeXZxM)

剧本:

`#!/bin/bash
open -a Terminal "` pwd `"` 

给予执行权限的命令:

`chmod +x /path/to/the/open_terminal.sh`

如果你和我一样懒这里有要诀，下载使用:[https://gist . github . com/itsdamslife/9532d 757676 B1 F3 C9 b 25 f 3959128367 c](https://gist.github.com/itsdamslife/9532d757676b1f3c9b25f3959128367c)

希望这对你有帮助。

快乐编码 [![😉](img/b26450942c7c42752fe0b02f126abb48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fumfYCPq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s0.wp.com/wp-content/mu-plugins/wpcom-smileys/twemoji/2/72x72/1f609.png)

灵感礼遇:微软 Visual Studio 代码内置终端

-快乐编码:)