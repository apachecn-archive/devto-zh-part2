# 使用 Windows 子系统的 Epic 开发环境

> 原文：<https://dev.to/johnbwoodruff/epic-development-environment-using-windows-subsystem-forlinux-5f0n>

**注意:**本文有一个使用 WSL 2 的更新版本。来看看[这里](https://dev.to/johnbwoodruff/far-more-epic-development-environment-using-wsl-2-439g)！

*交叉发布自 [Epic 开发环境，使用 Linux](https://medium.com/@johnwoodruff91/epic-dev-environment-with-wsl-dc81e234ae61) 的 Windows 子系统，在介质*上

像许多其他开发人员一样，我一直在做一些兼职项目。我的工作也是我的爱好。每当一个新的副业项目出现时，我会立即去追求它，因为从事一些新的、闪亮的东西会让我兴奋不已！我经常觉得这个由优秀的 [CommitStrip](http://www.commitstrip.com) 创作的漫画中的开发者就是写我的。

[![West Side-project Story](img/5406161d4add24754ddc9d33bad56811.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d056RSto--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gfjg3oume3za4v0aaqh4.jpeg)T3】

<center><small>[West Side-project Story](http://www.commitstrip.com/en/2014/11/25/west-side-project-story/) - [CommitStrip](http://www.commitstrip.com) - Nov 25, 2014</small></center>

为了支持所有这些附带项目，我显然需要一台出色的机器来完成这项工作。我想把我的工作笔记本和代码从我的个人项目中分离出来。所以，我最近决定在黑色星期五给自己买一台新的笔记本电脑。然而，对于我想要的东西，我无法说服自己花那么多钱买一台 Mac 电脑。然而，我可以以更便宜的价格购买一台我想要的所有规格(甚至更多)的 Windows 笔记本电脑。带着这个想法，我研究了几天，找到了我认为最完美的笔记本电脑！到了黑色星期五，我抢购一空。

我终于有了一台完美的笔记本电脑，准备出发了。最后的障碍？建立合法的开发环境。最初我很担心，因为过去几年我一直使用 Linux 和 macOS 进行开发。对我来说幸运的是，最新 Creators 更新的 Windows 10 对 Windows 开发者环境进行了巨大的改进。在这一切的中心？Linux 的视窗子系统，或 WSL。

没错。你可以在你的 Windows PC 上运行多个 Linux 发行版，不需要 virtualbox、双引导等等。这是本地的。这太棒了。

[![WSL Distros](img/e8461dc4a0f7c2744820adbadd2baa2e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Lnc196D_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/86n9ewmr69p2ej2fw67i.png)T3】

<center><small>Image from [New distros coming to Bash/WSL via Windows Store](https://blogs.msdn.microsoft.com/commandline/2017/05/11/new-distros-coming-to-bashwsl-via-windows-store/)</small></center>

我仍处于将它用于个人发展的早期阶段，但迄今为止，这是一次美妙的经历！这比试图与 cygwin、git for windows 和 bash emulation 以及许多其他试图弥合*NIX 体验差距的解决方案争论要容易得多，也更好(IMHO)。因此，让我们深入研究如何在您的 Windows 机器上获得这种史诗般的开发环境。

## 先决条件

为了使用这些说明，您必须运行 Windows 10，至少更新到 2017 年秋季 Creator 的更新。对于 Docker 上的部分，你还必须有一台可以使用 Hyper-V 和硬件虚拟化的机器，这需要 Windows 10 Pro。

## 用 WSL 安装 Ubuntu

[![Enable WSL](img/382bc228dc0dd3f05951ee433f4744b9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JsT9kZyu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wv1o55uid2fhn7zu8lqr.png)

您需要做的第一件事是启用 WSL。打开“打开或关闭 Windows 功能”，然后在出现的对话框中，向下滚动并选中“Linux 的 Windows 子系统”框，然后选择“确定”。这将应用更改，并在需要时重新启动您的机器。

启用 WSL 后，打开 Windows 商店并搜索“Linux”。你会看到一个大横幅，提示你查看 Linux 发行版。在撰写本文时，它们是 Ubuntu、openSUSE 和 SUSE Linux Enterprise Server。

我选择了 Ubuntu，因为我对它更有经验。本文使用 Ubuntu 命令。一旦你从 Windows 商店安装了 Ubuntu 应用程序，打开它并运行`lsb_release -a`来看看，是的，你确实在你的 Windows 机器上运行 Ubuntu 本身。

## 去吧

本文后面的许多安装都需要 git。我决定通过 WSL 安装并运行 git。要安装，只需运行`sudo apt update && sudo apt install git`。在安装 git 时，我还生成了一个 SSH 密钥，因为我以后需要用它来从我的所有 repos 中提取和推送。这可以非常简单地通过运行:`ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`来完成。

## ZSH (Optional)

下一步是完全可选的。我喜欢 bash，也喜欢 zsh，所以两者都可以。如果你喜欢 bash，跳过这一节。

安装 zsh 相当简单。在确保你已经运行了`sudo apt update`之后，用`sudo apt install zsh`安装它。你可以通过运行`zsh`来测试它是否工作，这将把你带到一个 zsh 终端！

我也喜欢 [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) ，它提供了许多漂亮的主题和优秀的插件。我使用了[基本安装](https://github.com/robbyrussell/oh-my-zsh#basic-installation)指令下的`curl`命令，一切就绪！然后，您可以通过编辑您的`~/.zshrc`文件并将主题名称添加到`ZSH_THEME`环境变量中来选择您想要的主题。我个人用的是[纯](https://github.com/sindresorhus/pure)，很美的极简主题。

[![Pure Theme](img/9b8e6336db25b26e31e541738164e4c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sYHCOHzB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/42d0py0lj8nicmq8h2v2.png)T3】

<center><small>Featured image from the [Pure](https://github.com/sindresorhus/pure) repository</small></center>

**注意:**就我个人而言，当尝试在 Ubuntu 终端应用程序中使用 Pure 和 zsh 时，它有一些样式和 unicode 问题。然而，这对我来说不是问题，因为我使用 Hyper，下面我会介绍它！

## 超

[Hyper](https://hyper.is/) by [Zeit](https://zeit.co/) 是我最喜欢的东西之一，我喜欢在我所有的机器上安装它。这是一个漂亮的终端模拟器，支持插件和主题。这是我在安装 WSL 和 Ubuntu 之后安装的第一批东西之一。要让它工作，只需打开您的`.hyper.js`文件，将 shell 属性更改为`shell: 'wsl.exe'`，然后保存并关闭文件。一旦您回到 Hyper，它应该加载您的 zsh(或 bash)终端！从现在开始，我只使用 Hyper 来满足我所有的终端需求。

**注意:**确保`shellArgs`属性中没有参数。默认情况下，WSL 使用登录 shell。

### 超级插件

如果有人没有使用过 Hyper，我推荐你把我使用的一些好的插件添加到你的`.hyper.js`插件列表中。它们包括[超级终端-物质-黑暗](https://www.npmjs.com/package/hyperterm-material-dark)、(披露:这是我的主题)[hyper wd](https://www.npmjs.com/package/hypercwd)、[超级标签-增强](https://www.npmjs.com/package/hyper-tabs-enhanced)，以及那些令人惊叹的时刻，[超能力](https://www.npmjs.com/package/hyperpower)！

## Visual Studio 代码

与 Hyper 相比， [Visual Studio Code](https://code.visualstudio.com/) 可能是我有史以来最喜欢的软件。我用过 IDEs，Sublime Text，Atom，Notepad++，用的最多的几乎都试过。VS Code 是目前为止我最喜欢的编辑器。它很漂亮，难以置信的轻量级，超级快，有大量惊人的扩展，内置调试器支持，并且是我用过的所有文本编辑器中最接近完整 IDE 功能的。我尽可能快地安装了它，并使用[设置同步扩展](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync)将所有[我的设置](https://gist.github.com/jbw91/e77025c1fc4fe1d5137ac525398f2227)都下载下来。(我强烈推荐使用它来同步设备间的设置)

我提出这一点是因为我在 VS 代码中将集成终端也设置为使用我的 WSL shell。为此，我设置了以下设置:`"terminal.integrated.shell.windows":"C:\\WINDOWS\\System32\\wsl.exe"`。一旦你打开综合终端，你应该是好的去！

## Node.js/NPM

就我个人而言，我决定采取在 WSL 环境中尽可能做更多事情的方法。此外，我痴迷于 NVM 来管理我的节点安装，这在 Windows 上不起作用。所以，我没有下载 Node.js for Windows 的安装程序，而是通过 Ubuntu 的仓库安装了 nvm！你可以按照 [NVM 的安装说明](https://github.com/creationix/nvm#install-script)上的指示。我用`curl`下载了安装脚本，我通过运行`sudo apt install curl`安装了这个脚本。

如果你做的一切都正确，你应该有 nvm 安装正确。然后，我通过运行`nvm install --lts`和`nvm use --lts`安装了 node 的长期支持版本，将其设置为我当前使用的版本。我还通过运行`nvm alias default {VERSION}`将它设置为我的默认节点版本，其中 version 是您刚刚安装的版本号。

**注意:**我最初有些纠结，当我打开我的 Ubuntu 终端时，它会给出错误:`N/A: version "N/A" is not yet installed`经过一番搜索，我发现当我运行`nvm ls`时，它会列出 node 的已安装版本。在 lts 版本中，有几个“不适用”,因为它们没有安装。我安装了这些版本，它不再给我错误。

[![Npm](img/911b6ffdb8e4c8dfff2533819290e3f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hF2gjw4Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6pn7ypli7l8z1lui6jkv.png)T3】

<center><small>A global npm install of the [Angular CLI](https://cli.angular.io/) repository</small></center>

### 纱线

作为安装 Node 的题外话，我想我应该提出我也安装了 [Yarn](https://yarnpkg.com/) ，因为我喜欢 Yarn。你可以通过 Windows 来安装它，但是同样，我正试图通过 WSL 来做我需要的一切，所以我遵循了 [Linux 安装说明](https://yarnpkg.com/en/docs/install#linux-tab)。当然，特别是 Ubuntu/Debian 指令。这进行得很顺利，而且我有纱线工作！

[![Yarn](img/da64c747681d26058e6a73d399d5f283.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CBCOwiDj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gb0z3p3p6mzqfadmg9dt.png)T3】

<center><small>A yarn install working beautifully</small></center>

## 码头工人

首先，在我们开始之前，需要注意的是，为了运行利用 Hyper-V 虚拟化的 Docker for Windows，您必须运行 Windows 10 Pro。家庭版不支持 Hyper-V，这并不意味着你不能运行 Docker。你仍然可以运行 Docker Toolbox，它使用 VirtualBox。然而，这些说明只适用于 Docker for Windows。

这是我没有通过 WSL 安装的一个东西。我为 Windows 安装了 [Docker，因为它与操作系统紧密集成。Windows 版本的体验对我来说是完美的，所以我觉得没有必要使用 WSL 来安装它。也许以后我会尝试这个。](https://www.docker.com/docker-windows)

但是，我确实让它可以从我的 WSL 环境中访问。安装 Docker for Windows(我必须在启动时进入 BIOS，并作为额外的步骤启用硬件虚拟化)并成功运行后，我简单地给它加了别名，这样它就可以像在 Linux/Mac 上一样被调用。在我的`.zsh_aliases`文件中，我添加了下面几行:

```
alias docker='docker.exe'
alias docker-compose='docker-compose.exe' 
```

Enter fullscreen mode Exit fullscreen mode

重启终端后，我可以像平常一样调用 docker 和 docker-compose 命令！

[![Docker](img/ac6514c58cebe88b19752f725967a083.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zPA83A-8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zywi8gdj24c2dvnxgp4a.png)T3】

<center><small>Docker for Windows being run through my zsh shell using the alias</small></center>

## 最后一句话

首先，大力支持 Rich Turner 和团队中为 Linux 开发 Windows 子系统的其他人。他们正在做一些疯狂的事情，让像我这样的普通开发者在 Windows 上的生活变得更加容易和愉快。不是所有的英雄都穿斗篷，这些家伙绝对是我的英雄！

最后，就我最终需要的工具和软件而言，未来肯定会有更多。就像我之前说过的，就我对这个开发环境的使用而言，现在对我来说还是早期。我确信我会遇到一些以前没有遇到过的烦恼。然而，每个环境都有这些烦恼，到目前为止，我一直喜欢在 Windows 上开发。老实说，我从没想过我会说这句话！