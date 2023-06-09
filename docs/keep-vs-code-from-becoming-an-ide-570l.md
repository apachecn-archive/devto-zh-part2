# 防止 VS 代码成为 IDE

> 原文：<https://dev.to/rpalo/keep-vs-code-from-becoming-an-ide-570l>

有些人喜欢又大又重又舒服的房子。有些人喜欢轻便、灵活、相对简单的文本编辑器和终端窗口。还有一些人喜欢 Emacs。我们不谈论他们。(我开玩笑的，对不起，我没忍住。)我是 zippy 编辑器/终端组的一员。这是一个帮助保持 VS 代码编辑器轻量级的技巧，就像你已经了解和喜欢的那样。

上周我在使用 [VS Code](https://code.visualstudio.com/) ，它开始感觉非常迟钝。看起来它(相对地)消耗了很多内存，我不确定发生了什么。

然后我突然想到:

## 我有太多的扩展在运行

我喜欢工具。尤其是新的闪亮的。我不认为我是一个人在这样做。正因为如此，我经常安装新的扩展，只是为了尝试一下，踢踢轮胎。然后我很快就忘记了它们，不仅安装了它们，还激活了它们。

这些扩展中的每一个都占用内存，使用电池，降低你的速度。他们也可以增加启动速度的时间。请记住，我的笔记本电脑并没有那么大，而且一台新的电脑或更多的内存暂时不会出现，所以我能节省的每一点内存都是值得的。

## 修复方法如下

把它们都关掉。

你听到了。

好吧，也许不是全部。但是当你在做一个 Ruby 项目时，你不需要整个 Go-lang 支持系统，对吗？您可能也不需要所有的 Python 支持。或者 C#代码片段。

在扩展栏中检查你的每个扩展并禁用它们。你不需要重新加载，直到你把它们都做完。

[![Disabling an extension](img/7785a8342bb5dd39da3027e94c355a1e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8wXtNd6t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://assertnotmagic.com/img/extensions-disable.gif)

现在你很快。你很时髦。*你。是。速度。*

[![Look at all those disabled extensions](img/7864502a0f2f18cd20a55e2192ba1d56.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--07SNG96Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://assertnotmagic.com/img/extensions-installed.gif)

## 好吧，好吧，不是全部

在我看来，有两种延伸。第一个是语言支持扩展。当我学习一门新的语言时，我发现有一个扩展可以让我写得更好。Python、Go、Ruby、特定于语言的林挺、美化、片段和智能感知。

很明显，其中很大一部分被设置为只有当你使用特定语言处理文件时才被激活。(谢谢！ [@SirWindfield](https://dev.to/sirwindfield) ！)

未找到注释

然而，一些表现不太好的并不是这样设置的。您可以通过查看您的扩展目录来检查扩展何时被设置为激活。在我的系统上，它在`~/.vscode/extensions/`。您将看到每个扩展名的目录。如果你查看一个分机的`package.json`，你可能会看到一个`activationEvents`的按键。行为良好的扩展只会在某些文件类型、命令和终端活动上激活。捣乱者只会有一个无伤大雅的小“*”。这些是您想要禁用的。

如果这看起来工作量太大，就像我一样，禁用所有这些特定于语言的功能。

另一种扩展是通用开发人员快乐扩展。像[日志支持](https://marketplace.visualstudio.com/items?itemName=pajoma.vscode-journal)、[彩色括号匹配器](https://marketplace.visualstudio.com/items?itemName=2gua.rainbow-brackets)、[基于星球大战的主题](https://marketplace.visualstudio.com/items?itemName=dustinsanders.an-old-hope-theme-vscode)，当然还有[表情符号支持](https://marketplace.visualstudio.com/items?itemName=Perkovec.emoji)。请随时让这些功能一直处于启用状态。你应该得到幸福。

## 但是如果我想要我的扩展怎么办？

不要惊慌。如果你从事一个需要这些特定语言的项目，你可以在你的工作空间中启用它们*。*

[![Enabling within a workspace](img/869397de5be71726a0abf3692a05d61d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--G78_ZtHq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://assertnotmagic.com/img/extensions-enable-workspace.gif)

现在，您获得了开发环境的全部功能，您的计算机也喜欢您。

* * *

你有喜欢的扩展吗？有没有不太出名或者不太受欢迎的？让我了解他们。我总是在寻找另一个扩展来添加到我的列表中。我甚至可能让它开着。

* * *

*原帖 [`assert_not magic?`](https://assertnotmagic.com/2018/04/26/keep-vs-code-light/)*

感谢 [@SirWindfield](https://dev.to/sirwindfield) ，更新了一些关于扩展何时激活自己的信息。