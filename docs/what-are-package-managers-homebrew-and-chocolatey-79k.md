# 什么是包管理器(家酿和巧克力)？

> 原文：<https://dev.to/raddevon/what-are-package-managers-homebrew-and-chocolatey-79k>

**软件包管理器**让安装、更新和管理软件变得简单。想想 Mac 和 iOS 应用商店、Windows 应用商店或各种 Android 应用商店。不同之处在于，包管理器主要用于命令行应用程序(尽管大多数现代的包管理器也会处理 GUI 应用程序)，这些应用程序通常是免费和开源的(尽管闭源应用程序也会出现在包管理器中)。

在这篇文章中，我将集中讨论与堆栈无关的包管理器 [Homebrew](https://brew.sh/) 和 [Chocolatey](https://chocolatey.org/) 的好处和用法。从现在开始，当我提到“包管理器”时，我指的是这两个。

## web 开发者为什么要关心包管理器？

作为一名 web 开发人员，您会发现自己安装了许多不同的命令行应用程序。每个版本都有一套独特的安装说明和升级指导。每一个都减慢了你的工作流程，因为你必须阅读文档并经历一些神秘的安装过程。

## 示例:安装 AWS CLI

### 推荐说明

这里有一个很好的例子。如果您想在 AWS 上主持一个项目，该怎么办？您可能需要安装 AWS CLI，以便自动部署应用程序。

为了做到这一点，如果您的系统上还没有 Python，您必须首先安装它。你需要 Python 2.6.5+或 3.3+的 pip。然后，您将运行以下命令来安装带有 pip 的 AWS CLI:

```
pip install awscli --upgrade --user 
```

Enter fullscreen mode Exit fullscreen mode

完成后，您需要将安装位置添加到您的路径中，以便您可以从命令行运行它。

### 家酿/巧克力安装

在 macOS 上，您可以使用 [Homebrew](https://brew.sh/) 来安装 AWS CLI，如下所示:

```
brew install awscli 
```

Enter fullscreen mode Exit fullscreen mode

在 Windows 上，您可以使用 [Chocolatey](https://chocolatey.org/) 安装 AWS CLI，如下所示:

```
choco install awscli 
```

Enter fullscreen mode Exit fullscreen mode

这就是你需要做的。新安装的程序会自动添加到路径中。

## 包管理器的优点

这是一个有点奇怪的比较，因为 AWS CLI 的推荐安装说明也使用了一个包管理器:pip 是 Python 包管理器。(那是因为 CLI 是用 Python 写的。)使用系统级软件包管理器的好处是**它们可以平滑独特的安装过程**。

您可以看到，即使在不同的操作系统中，安装命令也是**简单且类似的**。我只需将名称换成合适的包管理器，就完成了。也许为了一个不同的项目，我决定尝试在 Heroku 而不是 AWS 上托管。他们建议我下载一个安装程序并运行它。我可以这样做，或者我可以用家酿或巧克力运行一个简单的命令。一个软件包管理器**规范了设置新工具的过程**。

软件升级也是如此。每个工具都有自己的一套升级说明。升级是软件包管理器提供的**管理**的一部分。你可以一个接一个地升级每个软件包，也可以用一个命令更新所有已安装的软件包，这样可以节省大量的时间。

## 软件包管理器入门

如果你用的是 Mac，你可以把它粘贴到你的终端来安装家酿:

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 
```

Enter fullscreen mode Exit fullscreen mode

Windows 用户将改为安装 Chocolatey。这并不那么简单，但仍然比单独安装您需要的每个工具要好。如果您在命令提示符下，您将使用这个奇怪的命令:

```
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin" 
```

Enter fullscreen mode Exit fullscreen mode

在 PowerShell 中，您将使用以下代码:

```
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1')) 
```

Enter fullscreen mode Exit fullscreen mode

关于如何处理安装 Chocolatey 时可能遇到的问题的更多信息，请参见[他们的安装文档](https://chocolatey.org/install)。

* * *

你想成为一名网络开发人员，但你不断遇到障碍。我想帮忙。前往[拉德德文](https://raddevon.com/)报名参加免费辅导课程！

* * *

## 使用包管理器

(我将在 Homebrew 中显示后续命令，但在 Chocolatey 中命令是相同的。您只需用`choco`而不是`brew`开始命令。)

现在，您已经准备好开始安装软件包了。当您了解到需要安装一个新的命令行工具时，首先检查它是否在您的软件包管理器的 repo 中。您可以通过使用`search`命令来完成此操作。

```
brew search aws 
```

Enter fullscreen mode Exit fullscreen mode

包管理器会吐出一个匹配的列表。浏览找到正确的，然后**使用结果中的确切名称安装**。

```
brew install awscli 
```

Enter fullscreen mode Exit fullscreen mode

如果你得到多个结果，看起来其中任何一个都可能是你想要的，**做一个快速的网络搜索**来找出如何安装你正在寻找的工具没有坏处。如果你*没有*找到匹配，这也可能会显示一个新的库，你可以添加到你的包管理器，允许你安装应用程序。

稍后，当您准备好更新您的一个包时，您将使用 upgrade 命令。

```
brew upgrade awscli 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
你已经准备好安装几乎任何工具！

软件包管理器有更多的工具供您探索，但是这些是让您以最小的努力开始和安装任何东西的基础。您的包管理器将使保持工具最新和测试新的工具更加容易，并减少对您的开发工作流程的拖累。