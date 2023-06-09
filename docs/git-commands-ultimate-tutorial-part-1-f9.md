# Git 命令终极教程第 1 部分

> 原文：<https://dev.to/neshaz/git-commands-ultimate-tutorial-part-1-f9>

使用 Git 有许多不同的方法。最常见的就是原来的**命令行**工具和 **GUI** ( **G** 图形 **U** ser **I** 接口)。在这里，我将介绍使用 Git 时应该知道的最重要的命令。

> 命令行是唯一可以运行所有 Git 命令的地方。如果你知道如何运行命令行版本，你可能也知道如何运行 GUI 版本。此外，虽然您对图形客户端的选择取决于个人喜好，但所有用户都将安装并使用命令行工具。你可以在 [gits 官方在线书籍](https://git-scm.com/)上了解更多。

在我们开始之前，请确保*知道*如何使用 Mac 中的终端或 Windows 中的命令提示符或 Powershell，并在您的系统上安装[git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)。

## 简介

首先，你可能会问自己什么是 Git T1，你能用它做什么？ *Git 是一个快速、可扩展的分布式* *版本控制系统* *，具有丰富的命令集，提供高级操作和对内部的完全访问。*

在 Kolosek 中，我们为我们创建的每个应用程序使用 **Git** ，包括 [Ruby on Rails 项目](https://kolosek.com/rails-join-table/)，以便更容易作为一个团队工作并实现我们的目标。

通过在终端中运行`$git help`命令，您可以了解很多关于单个 Git 命令的信息。这应该会出现:

[![git-help-command](img/5c5381730514bf9aefd07c2578e16361.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hmgmuE01--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.kraken.io/kk8yWPxzXVfBD3654oMN/705193677f876525ff33c6a7eaca4901/git-help-command.png)

任何东西都可以添加到你的 git 库，甚至你的控制器测试！

## Git 选项

我将介绍最常用的命令行，从 **Git 选项**开始。这将解释你在本地机器上需要了解的关于 Git 的一切:

```
usage: git [--version] [--help] [-C <path>] [-c name=value]
           [--exec-path[=<path>]] [--html-path] [--man-path]
           [--info-path] [-p | --paginate | --no-pager]
           [--no-replace-objects] [--bare] [--git-dir=<path>]
           [--work-tree=<path>] [--namespace=<name>]
           <command> [<args>] 
```

*   **`$git --version`** :使用这个命令找出你当前的 **Git 套件版本**。

*   **`$git --help`** :每个[优秀的程序员](https://kolosek.com/tag/good-programmers/)都应该知道这个选项，它会向你展示**最常用的 Git 命令**，它还可以打印*所有可用的*命令和`--all`或`-a`。

*   **`$git -C <path>`** :不要从**当前工作目录**运行 Git，可以从给定的`<path>`运行。

> `-C <path>`命令会影响其他需要路径名的选项，如`--git-dir`和`--work-tree`，因为它们的路径名是相对于-C 选项导致的工作目录而言的。

*   **`$git -c <name>=<value>`** :您可以非常容易地从配置文件中用**覆盖值**。`<name>`必须与 *git config* 列出的格式相同，而`<value>`是您的新参数值。

*   **`$git --exec-path[=<path>]`** :这个命令帮助你找到你的核心 **Git 程序安装的路径**。另一种方法是设置环境变量`GIT_EXEC_PATH`。

*   **`$git --git-dir=<path>`** :每当需要设置**库**的路径或者设置`GIT_DIR`环境变量的时候，总是使用这个选项。

> 当我们设置 [CarrierWave example](https://kolosek.com/carrierwave-upload-multiple-images/) 为 Git 时，我们经常使用这些 Git 选项。你有过类似的经历吗？

*   **`$git --work-tree=<path>`** :这样可以设置到**当前工作树**的路径。您也可以通过设置`GIT_WORK_TREE`环境变量来实现这一点。

*   **`$git --namespace=<path>`** :用这个命令或者使用`GIT_NAMESPACE`环境变量来设置你的 Git **名称空间**。

而且，就是这样！确切地说，是现在。

这只是教程的第一部分！在下一篇文章中，我将介绍所有其他 Git 命令，请保持关注。

这篇文章最初发表在 [Kolosek 博客](https://kolosek.com/git-commands-tutorial-part1/)上。