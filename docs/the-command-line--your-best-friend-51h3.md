# 命令行——您最好的朋友

> 原文：<https://dev.to/codingsam/the-command-line--your-best-friend-51h3>

命令行是软件开发人员使用的工具，用于运行与框架或开发工具相关的命令、运行脚本以自动化流程等等。如果你正在学习编码或者已经编码了，我想你不应该害怕命令行。一开始看起来有点吓人，但是它会改善你的工作流程。

我遇到过一些人，他们总是逃避任何没有 GUI(图形用户界面)的东西。在某些情况下，你会没事的。例如，如果你是 C#开发人员，你将使用 Visual Studio。只需点击 IDE(集成开发环境)中的一些图标，就可以触发与运行、调试和部署相关的操作。这种方法没有错，但是……当你尝试探索其他技术、语言、框架等时，你最终会与命令行打交道。

如果您已经是一名软件开发人员，此时，您的反应应该是:

[https://medium . com/media/0aa 640 dad 9 d0 aa 8 f 10d 4709 e 424 b 8 BAE/href](https://medium.com/media/0aa640dad9d0aa8f10d4709e424b8bae/href)

> “你在说什么？我知道如何在我的 Mac 或 Linux 或 Windows 上的 cmd 上打开终端应用程序，并在其中运行命令”。

我在这里不是告诉你，你必须学会如何使用命令行…相反，让我告诉你，从我的经验，你如何可以有最好的命令行…永远！

首先，让我告诉你我是如何得知我将要与你分享的内容的。一切都是从我在一家创业公司做暑期实习开始的。我是一名寻求某种真实世界体验的学生，所以我并不真正熟悉命令行。首席技术官(CTO)向我展示了如何在我运行 Linux 的笔记本电脑上安装一个类似雷神之锤的控制台(在雷神之锤视频游戏中，当你按下“\”键时，控制台就会自动关闭)。然后，他让我安装 *oh-my-zsh* ，这个我会在这篇文章后面讲到。

[![](img/6cfbaa437b42c3bf1daabe9f90b04584.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lb9jOjJf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/637/1%2A8_M_UVHiOx6CfY8tgl-mnA.png) 

<figcaption>雷神之锤游戏中的主机</figcaption>

首席技术官基本上是一家公司技术和工程部门的负责人。

从那一刻起，我开始喜欢上了命令行。几年后，我仍然在使用这个工具，我将与你分享它。

基本上，你需要这些东西:

*   终端应用程序
*   z 外壳
*   oh-my-zsh

我一会儿会解释这些东西是什么。

只需按照下面两个步骤:)

### 首先——弄个好看的终端 app

首先要做的是弄一个终端 app。你可以使用你的操作系统自带的一个，但是有更好的解决方案，特别是对于 Windows，相信我，你不会想使用 *cmd* 。这里的目标是有一个更好的带有基于 Unix 命令行的终端应用程序。Unix 是 Mac 和 Linux 操作系统的基础系统。这就是为什么我更喜欢 Mac 或 Linux，也许这就是为什么你看到很多开发者使用这样的系统。无论如何，如果你使用 Windows，你仍然可以得到几乎相同的体验。

以下是我对 Mac、Linux 和 Windows 的终端应用推荐。

*   **Mac** : [iTerm](https://www.iterm2.com/)

[![](img/0732b69961795ff3eb9c72db609e41ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NeQN1rhR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AyRnyT30la5AukGoDkzG2_Q.png) 

<figcaption>运行在自己 Macbook Pro 上的 Iterm2】</figcaption>

*   **Linux** : [瓜科](http://guake-project.org/)

[![](img/75221632449493f2007788a677fc421a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--smRCvPeI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AdirfG6Rb4J3hbxc0aRjAHQ.jpeg) 

<figcaption>运行在 Ubuntu 上的 Guake。来源:【https://www.flickr.com/photos/xmodulo/14285529772】</figcaption>

*   **窗户** : [科内姆](https://conemu.github.io/)

[![](img/3c8240a73add54714dd8646c2b196344.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PuCBM3oU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/672/1%2ASCiK5lLCZjdlEAOY7CkBrg.png) 

<figcaption>运行在 Windows 上的 ConEmu。来源:[https://commons . wikimedia . org/wiki/File:ConEmu _ snapshot . png](https://commons.wikimedia.org/wiki/File:ConEmu_screenshot.png)</figcaption>

在 Windows 中，只安装 ConEmu 不会给你最好的体验。你需要安装[WSL(Windows Subsystem for Linux)](https://docs.microsoft.com/en-us/windows/wsl/install-win10)和一个在 Windows Store 中可用的 Linux 发行版，例如 [Ubuntu](https://www.microsoft.com/p/ubuntu/9nblggh4msv6#activetab=pivot:overviewtab) 。

这会给你一个类似 Unix 的命令行，这就是这篇文章的全部内容。

我认为使用我之前提到的一个终端应用程序，已经是一个进步了…但是让我们把它做得更好…

### 第二——让你的终端牛逼

我将向您展示一些您可以安装的东西，我认为这是最好的命令行体验。

现在，您需要安装两件东西:

*   [*Z Shell*](http://zsh.sourceforge.net/) ，本质上是一个 Unix shell(命令行解释器)。安装将取决于您的操作系统。我建议您使用包管理器。在 Windows 上，如果您成功地安装了 Linux 子系统，只需使用您在 WSL 中使用的 Linux 发行版中的包管理器。如果你使用 Linux，你可以使用你的发行版的包管理器。在 Mac OS 上，推荐你用 [*家酿*](https://brew.sh/) *。*
*   [*oh-my-zsh*](https://ohmyz.sh/) *，*是为 *Z 外壳*增加更多功能的一组插件。安装过程只是将命令复制并粘贴到您的终端上。根据您选择的安装方法，您需要 *wget* 或 *curl* 。只需使用您的软件包管理器来安装这两个选项之一。

如果你已经走了这么远，让我给你看看这有多棒！

### 见其行动

让我们看一些你可以用终端和 *oh-my-zsh* 一起做的很酷的事情的例子。

以下所有截图都是从 Mac OS 上截取的，因为我有一台 Macbook Pro…但是如果你安装了一个终端应用程序和“哦，我的天”，你将能够体验到同样的神奇。

*   **插件**

你有很多可用的插件。[查看完整的插件列表](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins)以及如何安装。就像在*的*插件*列表中添加插件名称一样简单。用户主文件夹中的 zshrc* 文件。

[https://medium . com/media/80c 061172 fa 043 dadb 7 e 68 AE 287 a08c 7/href](https://medium.com/media/80c061172fa043dadb7e68ae287a08c7/href)

*   **选择一个主题**

*oh-my-zsh* 允许您定制终端的外观。我只是使用默认的，但你可以随意给它一个不同的外观。选择主题只需要在你的*中定义一个 *ZSH 主题*变量。zshrc* 文件。检查所有可用的主题并根据您的选择设置此变量。例如，如果你想使用一个名为"[*awesome panda*](https://github.com/robbyrussell/oh-my-zsh/wiki/themes#awesomepanda)*"的主题，你需要在*中添加以下内容。zshrc* 文件:*

[https://medium . com/media/BDB 7 a2 c 906 ea 7 f 790 c 93 c 9540 CFF 24 f/href](https://medium.com/media/bdb7a2c906ea7f790c93c9540ccff24f/href)

*   **使用 git**

如果您使用 *git* 作为项目的版本控制，您知道您可以使用命令行。如果您导航到任何项目文件夹，您将在文件夹名称的右侧看到当前分支的名称。您还可以使用一些别名来缩短 git 命令。

*   git pull => gl
*   git push => gp
*   git 提交=> gc
*   git status => gst

看看这是怎么回事…

[![](img/340d23e664af6b0eef255bf87b218a6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pRf_KzQ5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Au2PYNP6CFuwhMdG4sbLW8g.gif) 

<figcaption>用 git 搭配 oh-my-zsh</figcaption>

*   **自动完成**

也许你已经知道，当你正在键入一个命令时，如果你按下 *TAB* 键，终端将试图完成你的命令。这已经是很酷的事情了。但是……*Z Shell*和 *oh-my-zsh* 将这种自动完成功能带到了另一个新的高度。没错，它可以帮助你，例如，完成一个文件夹名称，即使你在中间开始键入一个单词。如果有多个选项可用，您可以使用*选项卡*键浏览可用选项。

[![](img/7d3e45c862136f604370c9b6728562a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--feiyvTFp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AschkIWY2CsGb7VSt_bS5DA.gif) 

<figcaption>文件夹和文件的自动完成功能</figcaption>

另一个令人敬畏的自动完成级别，与一些软件开发工具有关。你有几个插件来帮助你自动完成一些特定的工具命令。例如，如果你使用 *nodejs* ，你将会使用很多 *npm* 命令，比如 *npm install* 。

[![](img/cc5613697e3c9a68ad594f2e78877e07.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FSsqPheB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AGwySiHrVJzHoISnYlJ3Dqg.gif)

> 因为你知道，开发者很懒，所以他们会尽量避免去写。

### 结论

任何软件开发人员都必须与命令行打交道。有些人不喜欢。坏消息是，除非你使用真正特定的工具集，否则你无法逃避它。我可以告诉你，从我的经验来看，开始的时候看起来有点吓人，因为它需要你记住很多命令。但是……一旦你开始感到舒适，你就会爱上它。

遵循两个简单的步骤，你可以有一个非常棒的体验。你只需要为你的操作系统下载一个终端 app。然后，继续安装 *z 外壳*和 *oh-my-zsh* 在它上面。

如果你是一名软件开发人员或者正在考虑成为一名软件开发人员，请记住…

> 命令行和其他开发工具一样重要

[![](img/0c11b4d0f6134e0519c72613a305e8ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_-rho6_A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ACioB717snwMtfEBeomP01A.gif) 

<figcaption>拿命令行开心:)</figcaption>

我谈到了终端和 *oh-my-zsh* 但是没有深入每个必需包的安装过程的细节。这将需要为每个操作系统设置一个员额。这里最重要的是，你要意识到这些工具。如果您想了解每个操作系统，请告诉我。我觉得你对 Mac 和 Linux 的体验最好。我目前的工作使用的是 Windows 笔记本电脑。我以前工作的时候用的是 Macbook，所以我习惯了一种基于 Unix 的体验。幸运的是，微软以某种方式与 Ubuntu 合作。这种合作关系允许您在 Windows 中使用 Linux 命令行。不管怎样，有时会有问题…但是我可以在新的帖子里写出来。

> 与刚开始职业生涯或总是试图逃离命令行的软件开发人员分享这篇文章:)