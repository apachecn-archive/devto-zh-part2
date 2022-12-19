# ASP.NET 核心:从 0 到过度杀戮-简介

> 原文：<https://dev.to/joaofbantunes/aspnet-core-from-0-to-overkill---intro-1o5m>

这篇文章是一个新系列视频的介绍(我会试着附上一些文章),介绍如何在 ASP.NET 核心上创建一个复杂的应用。

你可以在这里浏览介绍视频，或者滚动过去阅读书面合成。

[https://www.youtube.com/embed/4FXUrEY9PIQ](https://www.youtube.com/embed/4FXUrEY9PIQ)

## [T1】简介](#intro)

如前所述，本系列的目标是使用 ASP.NET 核心构建一个复杂的应用程序，从简单到复杂。

最终的结果可能是过度设计的一方(因此标题上的“过度设计”)，因为应用程序将被开发为尝试大量技术和概念的借口。如果开发应用程序时没有考虑教育目的，结果肯定会更简单。

我说这个系列将是关于 ASP.NET 核心，但这不是 100%正确的，因为我可能会做一些不特定于 ASP.NET 的东西，但它仍将是应用程序的核心部分。

我假设你能理解 C#语言，但是如果你觉得在这种情况下有价值的话，请随意询问一些关于这种语言的解释。

## 参考项目

作为一个参考项目，我将创建一个应用程序来处理组织朋友之间的体育赛事，保持统计数据和其他一些功能，我们将在路上讨论。我会尽量不把它集中在足球(或我们美国朋友的足球)上，这是我最初的想法，但我们会看到在这个过程中制作这样一个通用的东西有多难(是的，我还不知道，我没有在这方面投入太多时间来计划整个事情😝).

我不想用通常的博客或购物样本，我认为这个样本将允许很多概念，工具和技术被尝试。

正如已经提到的，考虑到要解决的问题并不复杂，最终结果很可能会被过度设计，但是考虑到目标是学习和尝试新东西，我不认为有人会介意。

## 工具

将从[开始。NET Core](https://www.microsoft.com/net) 和 ASP.NET Core 2.1，但是会随着新版本的出现而升级。来安装。NET Core，你可以从微软的网站下载，但作为一个更简单的选择，你可以在 MacOS 上使用 [Homebrew](https://brew.sh/) 或在 Windows 上使用 [Chocolatey](https://chocolatey.org/) (可能在 Linux 上使用 apt-get，但没有试过)。

关于工具:

*   作为 IDE，我将在 MacOS 或 Linux 上使用 [JetBrains Rider](https://www.jetbrains.com/rider/) (虽然不是免费的，除非是开源和学生账户)。在 Windows 上， [Visual Studio 2017](https://visualstudio.microsoft.com/vs) 可能会是选择。MacOS 的另一个选择是 Visual Studio for Mac 或适用于所有平台的 [Visual Studio Code](https://code.visualstudio.com/) 。
*   [Visual Studio 代码](https://code.visualstudio.com/)，用于非 C#的东西。
*   首先，只支持数据库之类的东西，最终也会把应用程序放在里面。或者，您可以直接在计算机上安装数据库，但是如果可以的话，我不喜欢安装我想测试的所有东西，以保持系统干净。
*   [Git](https://git-scm.com) 用于源代码控制。该项目将在 [GitHub](https://github.com) 上公开。
*   当不从命令行使用 Git 时，我会使用 [GitKraken](https://www.gitkraken.com/) 、 [Git 扩展](https://github.com/gitextensions/gitextensions)或[Sublime Merge](https://www.sublimemerge.com/)——最后一个是来自 [Sublime Text](https://www.sublimetext.com/) 的新工具，我想尝试一下
*   [Postman](https://www.getpostman.com/) 用于测试 HTTP 请求
*   要在 Mac 上做命令行的事情，只是为了几个额外的功能，默认的终端也足够了。在 Windows 上，我使用[命令](http://cmder.net/)。如果你想在 Windows 上使用 bash，你也可以在 Windows 10 中使用 WSL(Linux 的 Windows 子系统)。在 Linux 上，缺省的终端将完成这个任务。

## 其他

现在让我们开始吧，如果我搞砸了什么或者你有任何问题，请告诉我！此外，该项目将开放拉请求，不要客气，帮我这个忙。

希望这有用，cyaz！