# Python for Android 如何在 Android 上运行 Python 程序

> 原文：<https://dev.to/bauripalash/how-to-run-python-programs-on-android-49bg>

[![.](../Images/3c5104173906049b067b1e0dc5dd0728.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UVwbTPuC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://palash.tk/assets/images/py-in-an.jpg)

如果你正在阅读这篇文章，你可能对学习 Python 感兴趣，或者已经是一个热爱实验的 Python 程序员，如果是的话，**欢迎我的朋友来到好奇的世界**。如果你有一个 Android 设备，并且你想在你的设备上运行 python 程序，这篇文章就是为你准备的。

> [Android](https://en.m.wikipedia.org/wiki/Android_(operating_system)) 基于[Linux](https://www.linux.com/what-is-linux)T4】内核所以运行 python 是 100%可能的。

在 Android 中运行 python 程序有很多方法，我们将在这里讨论其中的几种。所以让我们开始我们的旅程吧。

## 1 qython

说到在 Android 上运行 Python 程序、脚本，我首先想到的名字是由 **QPythonLab** 开发的 **QPython**

[![QPython Screenshot - Python For Android](../Images/75400ef82ed141c2ea1620cb4c49e8fd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rVtDCf9W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://fsgh.palash.tk/imgs/qpy.jpg)

它有如此多的特点，你会喜欢它的。它拥有**终端模拟器**、Python REPL、**强大编辑器**、众多 Python 库、**通过二维码运行脚本**、 **PIP** 、课程等等。

但它的缺点是，当你第一次从 Play Store 安装它时，它附带了 Python 2，你必须安装 Python3 插件才能使用 Python 3。

访问官网:[https://qpython.org](https://qpython.org)

从[游戏商店](https://play.google.com/store/apps/details?id=org.qpython.qpy)下载应用

## #2 PyDroid 3

PyDroid 是不太出名的 Android python IDE，也是我最喜欢的*。我喜欢它是因为它的简单，是 QPython 无法提供的额外特性。使用 PyDroid，你甚至可以创建图形用户界面、游戏等等。PyDroid 是 IIEC 开发的*

[![PyDroid 3 - Screenshot](../Images/e1074d6d0e9538eab07c3e0d65e07337.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UOFeOr9e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://fsgh.palash.tk/imgs/pyd.jpg)

这是专门为教育目的设计的。大多数流行的机器学习和数据科学 python 库都是可用的，这使得它非常适合在旅途中测试和实验

 *> 自己试试，你会喜欢的。

下载自: [Play Store](https://play.google.com/store/apps/details?id=ru.iiec.pydroid3)

> 下一个项目是为你准备的，如果你是个科技通的话

## #3 Termux

顾名思义，这是一个用于 Android 的[终端](https://en.m.wikipedia.org/wiki/Computer_terminal)模拟器，所以像往常一样，你可以使用它的包管理器`pkg`或`apt`来安装 python。

[![Vim Running On Termux](../Images/5d0509baeb832df23d3b1a644b36c40f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bSgEM5b9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://termux.com/files/vim-main_framed.png)

> 除非你是一个实验爱好者，喜欢尝试新事物或技术极客

从 [Play Store](https://play.google.com/store/apps/details?id=com.termux) 安装 Termux

要在 Termux 上安装 Python 3，请在 Termux 中键入以下内容:

`pkg install python`

要在 Termux 上安装 Python 2，请在 Termux 中键入以下内容:

`pkg install python2`

要编辑 Python 脚本，你可以使用 GUI 编辑器，比如 [QuickEdit](https://play.google.com/store/apps/details?id=com.rhmsoft.edit) 或者你可以使用基于终端的强大编辑器[T3】VIMT5】](https://en.m.wikipedia.org/wiki/Vim_(text_editor))

要在 Termux 上安装 [VIM](https://en.m.wikipedia.org/wiki/Vim_(text_editor)) ，请在 Termux 中键入:

`apt install vim`

访问他们的官方网站:[https://termux.com](https://termux.com)

* * *

目前就这些了，还有其他可用的 ide 和方法，但是我只提到了 3 个能够离线运行程序的方法，我自己也试过了

我建议三个都试试，决定你最喜欢哪一个。

* * *

感谢阅读，请在下面的评论中告诉我你的想法👇

* * *

如果你喜欢我的作品(我的文章、故事、软件、研究等等),考虑给我买一杯☕咖啡🤗*