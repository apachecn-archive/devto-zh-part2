# Arch Linux for Devs

> 原文：<https://dev.to/svettwer/arch-linux-for-devs-26dm>

这份报告是关于我使用 Arch Linux 作为开发人员工作站的操作系统的经验。将向您介绍 Arch Linux 的概念，然后介绍主要任务，如软件包安装和 OS 维护。最后，我将讨论为什么我认为 Arch Linux 对于开发者来说是一个伟大的操作系统，并以一个结论结束。

## 简介

首先，我想避免“Linux 比 Windows 好”、“Mac 比 Linux 好”、“Windows 比 Mac 好”这类典型的 OS 比较语句。如果操作系统符合您的个人需求，它就是您的正确选择。正如生活中的许多方面一样，没有绝对“更好”的选择。

请注意，这篇文章仍然是基于我的个人观点，可能与你或其他人的观点不同。

## 什么是 Arch Linux？

Arch Linux 是一个极其轻量级、完全可定制的、[滚动发布的](https://en.wikipedia.org/wiki/Rolling_release) Linux 发行版，由其社区驱动，从头开始编写。它遵循 [KISS 原则](https://en.wikipedia.org/wiki/KISS_principle)，这导致在操作系统中没有*开销*，例如图形或命令行安装对话框，也没有发行版本身提供的配置工具。因此，Arch Linux 的目标是更有经验的 Linux 用户。Arch Linux 的简单性允许您完全按照您的需要构建您的系统，而不需要重新安装来获得新的主要操作系统版本，这是由于滚动发布的尝试。如果你维护得好的话，这给了你一个永远运行你的第一个 Arch Linux 系统的机会。与其他发行版不同，Arch Linux 不附带默认桌面安装、默认文件系统或默认工具集。它只是一个操作系统。不多也不少。这就是简单，不是吗？

在[官方维基](https://wiki.archlinux.org)中有各种各样的指南可以帮助你根据需要设置你的系统。也有项目提供基于 Arch 的发行版。其中之一是[无政府 Linux](https://anarchy-linux.org/) ，我个人正在使用。它基本上是一个带有安装对话框的 Arch Linux。您仍然可以通过中止安装程序来定制您的安装，手动操作或者取消选中一些您不想安装的软件包。

## 套餐管理

Arch Linux 的包管理器叫做 *[pacman](https://wiki.archlinux.org/index.php/Pacman)* 。这是一个 CLI 工具，允许您在系统上安装、删除和更新软件包。可能这些包是自建的，从官方仓库或者从[【AUR】(拱形用户仓库)](https://aur.archlinux.org/)下载，在那里用户能够提供自我维护的包，pacman 是你管理它们的工具。让我们来看看最重要的命令:

*   `pacman -Syu`:同步程序包库数据库后，更新所有程序包
*   `pacman -S <package-name>`安装软件包
*   `pacman -R <package-name>`取出包裹
*   `pacman -Qdt`查询软件包数据库，找到所有不再需要的软件包。

唯一有点不舒服的任务是从 AUR 安装一个包。在你能够安装它们之前，这些包必须被编译。这需要以下步骤:

*   下载源代码
*   `cd /path/to/the/sources`
*   仔细检查所有文件，因为 AUR 回购可能包含任何东西。
*   `makepkg -si`

另一个不舒服的任务是保持你的 AUR 软件包最新，因为吃豆人不能做到这一点。所以你必须定期检查你的 AUR 软件包的版本，如果需要的话重新安装。但是不要害怕！ [AUR 帮手工具](https://wiki.archlinux.org/index.php/AUR_helpers)来救援了！这些工具能够管理官方资料库和 AUR。我最近选择了 *aurman* 作为我的 AUR 助手，因为它维护得很好，有很好的文档记录，包含了很多功能，而且简单好用。它也相对较新，因为其 AUR 条目已在 2018-03-20 22:31 创建。更多详情请看第[页的奥曼 AUR](https://aur.archlinux.org/packages/aurman/)。要在一个新的 Arch Linux 上安装 aurman，只需如前所述从 AUR 手动安装即可。如果你对更多的细节感兴趣，请访问 [Arch 用户信息库维基条目](https://wiki.archlinux.org/index.php/Arch_User_Repository#Installing_packages)。

因为 aurman 有一个原生的 pacman 集成，你可以像使用 pacman 一样使用它，但有 AUR 的支持。

**2018-12-28 快速编辑** :
多偶已经停止了 aurman 开发:
*我不愿意继续开发 aurman 供公众使用。GitHub 的问题已经解决了，我对反馈、功能请求或错误报告不再感兴趣。aurman 从现在开始只为自己开发，我建议迁移到 yay。[来源](https://github.com/polygamma/aurman/commit/c409feef4c93137c2f0917d8ecdede2d51e06ea9)*
更多信息，请看 github 上的[自述](https://github.com/polygamma/aurman/blob/master/README.md)。

我迁移到 yay，它工作得很好。感谢 [@maxdevjs](https://dev.to/maxdevjs) 指出这个博客仍然推荐 aurman。大家新年快乐！

### 可用套餐

我最大的担忧之一是，我可能无法找到我日常工作所需的所有软件包，或者这些软件包可能已经过时，因为 Arch Linux 正在使用自己的打包系统。从今天开始，我需要的所有包都可以通过官方库或 AUR 获得。这也包括作为 IntelliJ 终极理念的软件。与我曾经认为的相反，Arch Linux 包绝对是最新的，如果不是最前沿的话。

## 维护您的系统

### 软件更新

对于许多其他操作系统，最常见的维护过程是安装更新。如前所述，如果您选择了 aurman，这可以通过一个命令`pacman -Syu`或`aurman -Syu`轻松完成。如果你每周安装一次更新，不会花很长时间。目前我的更新需要大约 10 分钟。这不包括我必须更新 IntelliJ IDEA 的更新。这需要 5-10 分钟。号外。在你开始更新之前，我强烈建议你检查一下 archlinux.org 的已知问题。如前所述，Arch Linux 是一个前沿的滚动发行版。因此，有些更新可能比其他更新更需要您的关注。截至今天，我只有一个需要手动交互的更新。解决此问题的说明(js52 更新)已在 archlinux.org 上得到很好的记录。

### 备份和恢复

使用计算机时，备份文件是最重要的规则之一。我想几乎每个人都有过因为备份不足而丢失数据的情况。所以，我假设这一课已经学过了，因此我将跳过那一章。更有趣的是，我想说的是为你的操作系统创建一个备份，这样你就可以在系统发生严重故障时恢复你的系统。和往常一样，你有很多机会去实现它。让我们讨论一些场景和可能的解决方案。

#### 不稳定系统

我们假设，你已经更新了你的系统。现在，您的系统到处都是错误消息，速度比预期的要慢。如果不是只对一个软件包，而是对 10 个或 20 个软件包执行更新，那么确定根本原因很容易成为一项乏味的任务。所以，回滚你的系统会很棒，对不对？没问题！如果您选择了 [BTRFS](https://wiki.archlinux.org/index.php/Btrfs) 作为您的文件系统，您就能够创建整个文件系统的快照。如果您已经启动到恢复介质或救援分区，则可以恢复这些快照。*请注意，从技术上讲，btrfs 快照* *不是* *备份。它只是你的原始设备的一个[副本。*
好吧，平心而论，BTRFS 并不是 Arch Linux 独占的。但是它像其他软件包一样利用了滚动发布的概念。因此，您很可能会比其他发行版更早地收到错误修复和改进。](https://en.wikipedia.org/wiki/Copy-on-write)

#### 致命的系统崩溃

最坏的情况！硬盘/固态硬盘损坏。因为你已经为你的文件创建了备份，这不是那么糟糕，对不对？你只需要重新设置你的系统，从备份中恢复你的文件，一切都很好。然而，安装所有必需的软件包和设置您的个人配置可能会花费一些时间。因此我建议也备份这些信息，因为操作系统不仅仅是一堆包和配置，对吗？以下是我使用的工具:

*   [etckeeper](https://wiki.archlinux.org/index.php/Etckeeper) :为你的`/etc`文件夹创建一个 git 库，由于生命周期挂钩，包括每次 pacman 操作后的全自动提交。
*   [packup](https://aur.archlinux.org/packages/packup/) :导出已安装软件包列表的简单工具。它还提供了一个安装功能，但我也同意 aurman 的观点。
*   [点文件](https://aur.archlinux.org/packages/dotfiles/):一个跨多个系统跟踪你的点文件的工具。

因此，使用这些工具恢复系统非常简单:安装 Arch Linux，安装导出包列表中的所有包，恢复您的配置，恢复您的点文件，完成！

### 更多信息

如前所述，[官方维基](https://wiki.archlinux.org)中有大量信息。这还包括一篇关于[系统维护](https://wiki.archlinux.org/index.php/System_maintenance)的文章。在这里，您可以找到许多工具和任务来保持您的系统健康和稳定。请注意，并非所有提到的任务都是保持系统稳定所必需的。只挑你认为对你重要的。

## 为什么我认为 Arch Linux 对开发者来说很棒

本文中提到的许多方面也可以用其他发行版来实现，但是从我的角度来看，有几点使 Arch Linux 成为开发人员的理想选择。作为开发人员，我们了解软件和技术。我们知道软件是如何工作的，我们知道这些技术是如何结合在一起的，我们知道如何对待软件系统。如果我们不知道，我们可以学得很快，因为软件是我们的日常业务和激情。这些方面与 Arch Linux 的简单性和灵活性相结合，是一个很好的组合，有很大的潜力使您的日常工作尽可能高效。

作为一个滚动发布的发行版，你总是有一个最新的系统，而不用担心 LTS 时期的结束。软件包也是最新的，如果不是最先进的。这意味着，你不必等到维护者决定一个新的包版本是稳定的，可以使用了。您负责维护您的系统，并根据您的需要对其进行定制，而维护工作与其他发行版类似。你决定，如果你想要一个完全成熟的现代桌面环境与所有好看的效果，或者如果你想要一个最小的或 CLI 驱动的系统释放你的机器的全部计算能力。如果东西坏了呢？那么，你知道在哪里搜索这个问题，因为你已经设置了你的系统，因此你知道什么在上面运行。在我看来，所有这些与活跃的 Arch Linux 社区和[官方维基](https://wiki.archlinux.org)中的详细文档相结合，使它成为开发者的一个伟大选择。

## 结论

如开头所述:
*如果操作系统符合你的个人需求，那么它就是你正确的选择。*
对我来说，这是 Arch Linux。这并不是因为它给 Linux 世界带来了一个令人惊叹的新特性或改变生活的增强功能，而是因为它带来了一些不同的概念，从用户的角度来看，这些概念有意义并改善了系统的工作方式和感觉。

我希望我的系统尽可能高效，同时又不失桌面环境的舒适性。我也希望能够使用最新的软件。我希望我的系统稳定可靠，我想了解系统上发生了什么。所有这些在 Arch Linux 中都找不到。有了 Arch Linux，您就有了一个简单的、完全可定制的、社区驱动的滚动发布操作系统，它拥有一个庞大的软件包和文档生态系统。您拥有其他发行版中所有可用的东西，但是在 Arch Linux 中，您可以选择使用或不使用它。然而，Arch Linux 并不适合初学者。在开始之前，您应该对 Linux 系统有一个扎实的了解。但是如果你获得了这些知识，你就能充分发挥它的潜力。

本文原载于[labs . consol . de](https://labs.consol.de/development/linux/operating-systems/2018/06/18/arch-linux-for-devs.html)2018-06-18