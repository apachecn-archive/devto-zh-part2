# 使用 WSL 和 MobaXterm 在 Windows 上创建 Linux 开发环境

> 原文：<https://dev.to/nickjj/using-wsl-and-mobaxterm-to-create-a-linux-dev-environment-on-windows-1omh>

**本文原帖发布于 2017 年 11 月 10 日:[https://nickjanetakis . com/blog/using-wsl-and-mobax term-to-create-a-Linux-dev-environment-on-windows](https://nickjanetakis.com/blog/using-wsl-and-mobaxterm-to-create-a-linux-dev-environment-on-windows)T3】**

* * *

在过去 5 年左右的时间里，我一直在使用 [VMware 的 Unity mode](https://nickjanetakis.com/blog/create-an-awesome-linux-development-environment-in-windows-with-vmware) 在 Windows 上无缝运行 Linux 应用程序，而不必进行双重引导。

总的来说，这是一个相当坚实的设置，但它有一些恼人的问题。有一天我醒来，备份了我的源代码，然后愤怒地卸载了 VMware，接着删除了 100GB 的虚拟机映像。

已经没有回头路了。我必须找到一个新的解决方案。

剧透:这个新设置是我见过的最好的同时运行 Linux 和 Windows 的方式。如果你使用的是 Windows，并且也想运行 Linux 工具(甚至是图形应用程序和 Docker ),你可以通过阅读这篇文章来学习如何设置一切。

## 为什么要放弃 VMware 和 Unity 模式？

在我介绍如何设置新的开发环境之前，我想先谈谈 VMware 设置的问题，因为我认为“为什么”和“如何”同样重要。

如果你想直接进入“如何”，那么[点击这里](#wsl-conemu-and-mobaxterm-to-the-rescue)。

#### 锁定为使用 Xubuntu 14.04

VMware 正式取消了对在 Unity 模式下运行 Linux guests 虚拟机的支持，Xubuntu 14.x 是最后一个支持该功能的版本。

对其他版本 Ubuntu 的支持也很差。他们中的大多数有图形异常，这将使它无法使用，而 Xubuntu 是我发现的唯一一个工作的发行版。

我并不反对 Xubuntu 14.x，但是在我写这篇文章的时候，16.x 版本已经发布很久了，当 18.x 最终到来的时候，我不想被 14.x 所束缚。

我是一个务实的人，我不介意使用旧软件，但我不想将我的开发环境建立在不受支持的 Linux 发行版上。

#### 文件系统冲突

随着虚拟机的建立，我将所有的源代码直接保存在虚拟机中，然后将我的一个数据硬盘也挂载到虚拟机中。

我这样做是因为将我的源代码放在 VM 中意味着拥有本机性能。我使用挂载的驱动器来备份我的源代码，偶尔也在 VM 和 Windows 之间移动文件。

这听起来可能合理，但这是我的主要开发工作站，我参与的大多数项目都有不仅仅是源代码的组件。

例如，我的 [Docker 课程的页面](https://diveintodocker.com/?utm_source=nj&utm_medium=devto&utm_campaign=wsl-dev-env)也有图像，这些图像是用图像编辑工具制作的，但网站的源代码在虚拟机内部。

这意味着我必须在 Windows 中打开一个图形工具，创建图像，将其保存到共享数据驱动器上，然后从那里复制到 Docker 网站源代码文件夹内的图像文件夹中。

这可能看起来不令人讨厌，但它确实是。感觉就像你在和两个文件系统作战，你花了很多时间对它进行微观管理。

我本来可以通过将所有源代码都放在我的数据驱动器上并装载所有内容来解决这个问题，因为 VMware 的文件夹装载性能实际上非常好，但最终，我没有这样做，因为嘿，我是个白痴哈哈。

#### MS Windows 看不到 Linux 驱动的 Unity 模式 Windows

Windows 有一个内置的能力来捕捉和平铺窗口在一起，但它认为由 Unity 模式创建的窗口是未知的，这意味着，它完全忽略了它们。

如果我想安排来自 Linux 和 windows 的应用程序窗口，我必须手工完成，这是我经常做的事情。

另一个相关的问题是，Unity 模式驱动的窗口没有同样流畅的感觉，因为 windows 无法应用任何魔法来使 UI 感觉流畅。

#### 弗里肯换挡键

显然有一个 9 年前的错误，shift 键经常被卡住，而且没有解决办法。Unity 模式仅适用于 VMware Player，这意味着我甚至无法升级 VMware 软件来查看它是否得到了修复，因为 VMware Workstation 完全禁用了 Linux 来宾的 Unity 模式。

你无法想象这有多糟糕，这也是我想寻找替代方案的主要原因，因为它妨碍了我为我的[课程](https://nickjanetakis.com/courses/)进行现场编码，而且这种情况经常发生。

想象一下，键入`<%`或`{{`，字面上(我的意思是字面上)50%的时间 shift 键会停留在启用状态，当你进入键入时，所有内容都以大写字母出现，当你点击向上箭头时，它会选择整行。

这是超级破坏性的，“修复”是必须非常快速地按下 shift 键大约 15 次才能松开。如果这种情况一天发生两次，我可以处理，但这是每分钟发生两次。这是不可接受的，我不知道自己是如何活了这么久的。

每次我试图打开一个模板标签或者使用一个括号的时候，我都会有 PTSD。

下面解释的设置解决了上述所有问题！

## WSL、ConEmu 和 MobaXterm 前来救援

我的最终目标是能够在 Windows 下运行命令行和图形 Linux 应用程序，而不必进行双重引导，这就是你在设置好一切之后能够做到的。你甚至不需要 Cygwin 或者虚拟机！

它需要坚如磐石，快速而不妥协。

在使用这套设备一个多月之后，我可以自信地说一切都很棒。剪贴板共享是完美的，打字是有效的即时，移动窗口感觉流畅，Docker 运行速度足够快，不会分心，它是稳定的。

下面提到的所有工具(除了 Windows)都是免费和/或开源的。

要跟进，您需要使用 2017 年秋季版本的 Windows 或更高版本。无论您使用的是家庭版、专业版还是企业版，它们都可以工作。

#### 为 Linux 设置 Windows 子系统(WSL)

在 2017 年秋季版 Windows 之前，WSL 也被称为 Bash for Windows，但现在它被简称为 WSL。微软也不仅仅支持 Ubuntu。

你可以选择运行 [Ubuntu](https://www.microsoft.com/en-us/store/p/ubuntu/9nblggh4msv6) ，Fedora 和 [SUSE](https://www.microsoft.com/en-us/store/p/opensuse-leap-42/9njvjts82tjx) 。更多的发行版正在路上。您甚至可以将它们与各自独立的文件系统一起运行。

我个人运行 Ubuntu，但最终你使用哪一个并不重要，因为它们都应该工作。我说“应该”是因为我还没有亲自尝试过其他发行版，但是除非发行版本身被彻底清洗了，否则它们不会导致任何失败。

WSL 将让我们能够在 Windows 中运行 Linux 工具。让我们开始吧。

##### 1。启用 WSL

搜索“Windows 功能”，然后启用 WSL:

[![enable-wsl](img/3486eab1286ffc336f1e021d0af8fe82.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hLyg7G-R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nickjanetakis.com/assets/blog/enable-windows-subsystem-for-linux-9048ffa9eb4d34ccfa99c9354d885441fc5aa935f42c2f6ed079ec0219546919.jpg)

在这一点上，你需要重新启动，所以去这样做，因为你将不得不为了继续。不幸的是，这不是那些“应该”重启，而是“必须”重启。

##### 2。下载、启动并安装 Linux 发行版

我推荐 Ubuntu，但是你可以选择你认为最适合你的。

只需点击“立即获取”按钮(或类似按钮)即可下载。

下载完成后，启动它并同意安装。

##### 3。创建用户

安装后，它最终会要求您创建一个新的 UNIX 用户:

[![wsl-ubuntu](img/9f6012b9919dee371e5b26e0a8f8a2b6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7CfMtycq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nickjanetakis.com/assets/blog/wsl-ubuntu-create-new-user-264908eeb083285be175146915b25f5b71530d8e33bf6ef0bbe91e5dc160c1e5.jpg)

我建议使用小写版本的 Windows 用户。举个例子，我的 Windows 用户是“尼克”，但我对 Ubuntu 用户用的是“尼克”。

##### 未来推出 WSL

至此，WSL 已经设置完毕。你可以安装像 Ruby、Python、NodeJS 或者任何你通常会安装的工具，然后按照 Ubuntu 的安装说明去做。

图形应用程序还不能工作，但在我们开始之前，我们需要使用一个合适的终端，因为默认的微软终端很粗糙。如果你刚好喜欢，可以做一个 Windows 搜索“bash”以后推出，但是我觉得不值得用。

#### 设置 ConEmu(Windows 终端)

我花了很多时间研究不同的 Windows 终端，最终得出结论，ConEmu 拥有所有正确的东西。

[![conemu-terminal](img/941b23b73c005cabad09ddf2889bc926.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZZYSfDXC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nickjanetakis.com/assets/blog/conemu-terminal-4474671361f59992e9e6403280bfeb5fc6c4594cf367ad251c0ede1abfa58265.jpg)

##### 1。这就是 ConEmu 成为一个好选择的原因

*   它是开源的
*   它支持标签，分割窗格和会话
*   搜索和热键支持是一等公民
*   您可以将它用于 PowerShell 和 WSL (Bash)
*   这是荒谬的可配置性和坚如磐石

老实说，我甚至觉得没有必要再使用 tmux 了(但是如果你愿意，你仍然可以在 WSL 中安装 tmux)。我很感动也很感激 ConEmu 的存在。

##### 2。安装 ConEmu

只需进入他们的 [GitHub 页面并下载它](https://conemu.github.io/)。

我强烈建议你安装后在设置中切换到“最新”版本。它很稳定，而且经常添加有用的功能。这将是在“首选发布类型”下的设置的一般->更新区域。

##### 3。使 ConEmu 默认为 WSL

当打开一个新的 ConEmu 实例时，你可以让它默认为 WSL，这就是我正在做的，因为我并不真正使用 PowerShell 或常规的 Windows CMD 提示符。

[![conemu-wsl-default-terminal](img/991897f5866991acea122cc27f1d6404.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WQ93sEPr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nickjanetakis.com/assets/blog/conemu-wsl-default-terminal-34e9813ec99364e59f6327c46be8f6a1599139fda1580294f0bf5e9015142edf.jpg)

为此，只需进入设置->启动->特定任务名称屏幕，从下拉列表中选择`{Bash::bash}`并保存设置。

你也可以随意看看其他的设置，因为你可能想要调整一些东西。例如粘贴多行需要你点击鼠标右键或者使用`Shift+Insert`而不是`CTRL+V`，因为`CTRL+V`只用于粘贴一行。你可能想改变这种行为。

#### 设置 MobaXterm 运行图形化 Linux 应用程序

MobaXterm 有很多不同的特性，但我们关心的是它可以运行 X 服务器会话。这将允许我们在 Windows 支持的窗口中呈现运行在 Linux 中的应用程序的显示。

##### 1。安装和运行 MobaXterm

去他们的网站下载免费版本，然后安装。

##### 2。确保 X 服务器正在运行

你总是需要运行 MobaXterm，在应用程序的右上角有一个“X 服务器”按钮。确保它在运行。如果它说它没有运行，然后点击按钮，就是这样。

##### 3。在 WSL 中设置显示

我们需要配置 WSL，将其图形应用程序的显示发送到 Windows。如果我们跳过这一步，那么我们的图形应用程序将试图在 WSL 内部加载，不会显示任何内容。

为此，我们需要在 WSL 中设置`DISPLAY`环境变量，我们将在我们的`.bashrc`文件中这样做，这样每当我们打开一个终端时，它总是被设置。

运行`nano ~/.bashrc`，然后将其添加到文件底部并保存:

```
export DISPLAY=:0 
```

Enter fullscreen mode Exit fullscreen mode

语法是`HOST:NUMBER`。在我们的例子中，我们可以省略主机，它将通过本地 Unix 套接字绑定显示。WSL 知道如何实现这一点，所以不用担心。

`0`是在 WSL 环境中系统的第一个监视器的显示号。同样，我们不需要担心这一点，因为一旦窗口打开并运行，您将能够毫无问题地在多个显示器之间移动它。

要应用更改，请从 WSL 终端运行`source ~/.bashrc`。

##### 4。更改剪贴板的工作方式

默认情况下，MobaXterm 将从 WSL 中复制 select 上的文本，这绝对不是复制文本的预期行为，尤其是当您想要在 WSL 和 Windows 之间复制和粘贴文本时。

最初我认为剪贴板可能已经被 MobaXterm 破坏了，但是在设置中的一点探索立即修复了它。

转到设置-> X11 选项卡，然后将剪贴板下拉框更改为*选择*时禁用复制。一旦你这样做，并保存更改，你的剪贴板将按预期工作。不错！

##### 5a。在 WSL 内部测试 VSCode

如果你正在运行 Windows 10 Spring 2018 更新，你会很高兴知道 VSCode 现在可以在原生 WSL 内部运行。在这个版本之前，这是不可能的。

就我个人而言，现在我直接在 Windows 上运行 VSCode，但是我想我应该包括如何在 WSL 中设置它，因为这并不明显。

如果你使用的是 Ubuntu，你可以遵循以下步骤:

```
# Download the Linux .deb package from: https://code.visualstudio.com/download

sudo apt-get install libgtk2.0-0 libxss1 libasound2
sudo dpkg -i <the_file_you_just_downloaded>.deb
sudo apt-get install -f 
```

Enter fullscreen mode Exit fullscreen mode

你需要`libgtk2.0-0 libxss1 libasound2`,因为 VSCode 依赖于它们，并且默认情况下它们不会随 WSL Ubuntu 发行版一起安装。

一旦所有东西都安装好了，你就可以从 WSL 运行`code .`了，你会看到一个漂亮的 VSCode 编辑器。恭喜你，一切都准备好了！

##### 5b。在 WSL 中测试崇高的文本

如果你宁愿使用 Sublime 文本而不是 VSCode，那也很酷。

在最终切换到 VSCode 之前，我用了很长时间的 Sublime Text。他们都是可靠的编辑。我只是碰巧喜欢 2018+的 VSCode。

如果你使用 Sublime 并且正在使用 Ubuntu，你可以遵循以下步骤

```
wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -
sudo apt-get install apt-transport-https
echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list
sudo apt-get update
sudo apt-get install libgtk2.0
sudo apt-get install sublime-text 
```

Enter fullscreen mode Exit fullscreen mode

安装`libgtk2.0`是必要的，因为它不是默认安装在 Ubuntu 的 WSL 版本中的。如果没有这个包，Sublime 就不会启动，因为这是它呈现用户界面的方式，所以如果你没有使用 Ubuntu，你也需要在你的发行版上安装这个包。

一旦所有这些都设置好了，你就可以从 WSL 运行`subl .`,你应该会在自己的浮动窗口中看到 Sublime 文本编辑器。恭喜你，一切都准备好了！

[![sublime-text-in-wsl](img/be60792f2fb395373fd205568a7c0d20.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rM8iAfrd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nickjanetakis.com/assets/blog/sublime-text-3-running-in-wsl-through-x-server-51eced99fed8c1402c947d707c4c2e9eee507f8be3a3d08cd8cb283e126ac892.jpg)

## 启动并运行 Docker

我有另一篇文章致力于[设置 Docker 与 WSL](https://nickjanetakis.com/blog/setting-up-docker-for-windows-and-wsl-to-work-flawlessly) 一起工作。我所有的 web 应用程序都是通过 Docker 运行的，使用这种设置，它在开发中表现出色。

## 有什么定论？

我真的很高兴这个设置，除了在计算机世界发生的任何疯狂的事情，我可以看到自己在未来 5 年或 10 年使用这个设置。

我还想对微软、MobaXterm 和 ConEmu 的开发者们大声疾呼，是他们让这种结合成为可能。我终于觉得我在两全其美，而不需要不断寻找替代方案。

对于制作[软件开发者相关课程](https://nickjanetakis.com/courses/)的人来说，我无法运行原生 Linux，因为 Linux 上没有可与 Camtasia 媲美的视频录制/编辑工具。