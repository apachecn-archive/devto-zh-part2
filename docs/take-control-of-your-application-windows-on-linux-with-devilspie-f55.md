# 使用 Devilspie 控制 Linux 上的应用程序窗口

> 原文：<https://dev.to/brpaz/take-control-of-your-application-windows-on-linux-with-devilspie-f55>

在这篇文章中，我将向你介绍一个令人惊奇的 Linux 应用程序，叫做 [Devilspie](http://www.nongnu.org/devilspie2/) 。

## 什么是 Devilspie

Devilspie 是一个窗口匹配实用程序，允许用户在创建窗口时执行脚本操作。

一些可能的行动包括:

*   自动最小化/最大化
*   放置在特定的工作区
*   放置在屏幕上的特定位置
*   设置其他窗口属性，如“总是在顶部”

这使您能够完全控制每个应用程序的窗口显示方式。

例如，您可以让一些应用程序总是最小化启动(我将在本文中展示一个这样的例子)，或者让 Slack 总是在 workspace 2 上启动，或者让您的终端窗口总是在屏幕的右上角打开，并将“总是在顶部”设置为 true。Devilspie 给你的灵活性和可能性是无穷无尽的。

您甚至可以做更复杂的事情，比如将应用程序的第一个实例放在 workspace 1 上，将第二个实例放在 workspace 2 上，等等。

## 真实世界的例子

现在我将向你们展示，Devilspie 是如何解决我曾经遇到的一个具体问题的。

我用 Franz T1 作为 WhatsApp、Slack、Facebook Messenger 等服务的消息应用。

我想让它自动启动系统，这样我就可以开始接收新消息的通知。Franz 的配置可以实现这一点，但有一个令人讨厌的缺点。它最大化启动应用程序，而不是在系统托盘中启动。

Devilspie 允许我用很少的几行代码创建一个脚本，该脚本将检测何时创建一个新的 Franz 窗口并自动最小化它。

请继续阅读实现的细节。

## 用法

我们要做的第一件事是安装 Devilspie。请注意，我使用的是 Devilspie2，它是原始 Devilspie 的一个分支，使用 Lua 作为其脚本引擎，而不是原始 Devilspie 的符号表达式，这使得它更容易理解和维护。

在基于 Debian 的系统上，你可以从官方仓库安装它。

```
sudo apt-get install -y devilspie2 lua5.2 
```

Enter fullscreen mode Exit fullscreen mode

它也存在于 AUR 知识库中，供 Arch 用户使用。

接下来，我们需要创建一个目录来存放我们的脚本:

```
mkdir ~/.config/devilspie2 
```

Enter fullscreen mode Exit fullscreen mode

我选择把它放在。config 文件夹，但是您可以将它放在您想要的任何地方。

现在打开您的终端，用下面的命令执行 devilspie:

```
devilspie2 --folder  ~/.config/devilspie2 --debug 
```

Enter fullscreen mode Exit fullscreen mode

`--folder`选项设置 Devilspie 将在其中查找其脚本的文件夹的路径。将它指向上面创建的目录。

现在，您应该会看到一些输出，其中包含有关当前打开的窗口的信息。

如果您打开任何应用程序，您应该会看到 Devilspie 检测到它，并在控制台输出中显示更多详细信息。该输出对于获取诸如“窗口名”、“应用程序名”或“xid”等信息非常有用，您需要在脚本中使用这些信息来识别您的应用程序。

现在让我们创建我们的脚本，这将最小化弗朗兹窗口。

在 Devilspie 文件夹中创建一个新文件。你想叫它什么都可以。只是确认一下，确实如此。lua 扩展。

添加以下内容并保存:

```
if (get_window_name()=="Franz") then
    debug_print( "minimize Franz window")
    minimize()
end 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，理解它做什么非常简单。

当一个新窗口打开时，Devilspie 将检查它是否与指定的窗口名匹配，并调用最小化函数。

您可以在脚本中使用的一些函数:

*   `get_window_name()` - >返回窗口名称
*   `get_application_name()` - >返回应用名称
*   `set_window_workspace( 2 )` - >设置窗口的工作区。
*   `set_window_geometry( x, y, width, height )` - >设置窗口大小
*   `make_always_on_top()` - >让窗口总是你顶着。

你可以在这个要点中找到一个更完整的函数列表。

然后，您可以重新启动 Devislpie 并测试脚本，方法是启动应用程序并查看是否按预期最小化。如果一切正常，你可以将 devilspie 命令添加到你的启动应用程序中，以确保它随时准备响应新的窗口事件。

## 结论

正如你所看到的，Devilspie 允许你更好的控制和灵活的设置你的应用程序窗口。Lua 脚本也很容易理解。

在写这篇文章的时候，我还发现了 [kpie](https://github.com/skx/kpie) ，它显然是 Devilspie 的更新版本，也使用 Lua 来编写脚本。你可能想看看。

希望你喜欢。欢迎在下面的评论区提出任何问题。

再见。

* * *

## 引用

*   用于在 Linux 桌面上自动放置窗口的 devilspie 2–Justin Gedge
*   [使用 Devilspie 2 在 Ubuntu 上脚本化窗口操作](https://www.howtoforge.com/tutorial/ubuntu-desktop-devilspie-2/)