# 用于开发和黑客的最小 Chromebook 设置

> 原文：<https://dev.to/pbnj/a-minimal-chromebook-setup-for-development--hacking-5292>

## 目录

*   [TLDR 版](#the-tldr-version)
*   [简介](#introduction)
*   [坚实的原则](#the-solid-principles)
    *   [安全](#security)
    *   [所有权成本](#ownership-cost)
    *   [利用本地功能](#leverage-native-capabilities)
    *   [固有限制](#inherent-restrictions)
    *   [开发者体验](#developer-experience)
*   [细节](#the-details)
    *   [硬件](#hardware)
    *   [软件](#software)
*   [离别的思念](#parting-thoughts)

*   如果你买了一台 Chromebook，就呆在范围内，坚持使用 ChromeOS 为一个 Linux 发行版启用 ChromeOS 的双引导、侧加载或擦除是不值得的。
    *   如果你坚持要一个 Linux 发行版，买一个二手联想就好了；它将更加强大，更加通用，并且与您的 Linux 发行版兼容。
    *   无论如何，**不要**启用“开发模式”。
*   同类最佳 Chromebook 硬件≤500 美元(按顺序):[三星 Chromebook Pro](https://www.samsung.com/us/computing/chromebooks/12-14/samsung-chromebook-pro-xe510c24-k01us/) (大约。500 美元)，[华硕 Chromebook Flip C302](https://www.asus.com/us/2-in-1-PCs/ASUS-Chromebook-Flip-C302CA/) (大约。450 美元)， [Acer Chromebook for Work](https://www.amazon.com/Acer-ChromeBook-CP5-471-35T4-Black-NX-GE8AA-002/dp/B01EPZIJRQ/ref=sr_1_4?ie=UTF8&qid=1523224085&sr=8-4&keywords=chromebook+for+work) (大约。$400).
    *   否则，请选择基于英特尔的芯片(例如赛扬)和最低 4 GB 内存。
*   安全外壳:SSH 的 Chrome 扩展。强烈建议使用多因素身份认证(MFA)。
*   Termux :一个全功能的终端模拟器 Android 应用，有许多开发者友好的插件，比如访问剪贴板、通知、外部存储等等。
*   基于 GUI 的编辑器也可以作为 Chrome 应用程序使用，比如[插入符号](https://chrome.google.com/webstore/detail/caret/fljalecfjciodhpcledpamjachpmelml?hl=en)、[泽德](https://chrome.google.com/webstore/detail/zed-code-editor/pfmjnmeipppmcebplngmhfkleiinphhp?hl=en)、&、[文本](https://chrome.google.com/webstore/detail/text/mmfbcljfglbokpmkimbfghdkjmjhdgbg?hl=en)。
    *   Chrome Dev Editor 是一个非常好的 IDE，在功能上几乎可以与现代的基于桌面的编辑器相媲美(例如，Atom & VS Code ),但不幸的是，它不再处于活跃的开发中。

* * *

## 

我决定分享我推荐的 Chromebook 开发设置，因为我
厌倦了[看到](https://www.theverge.com/2017/11/16/16656420/google-pixelbook-chromebook-development-linux-crouton-how-to)[相同的](https://arstechnica.com/gadgets/2017/06/how-to-install-linux-on-a-chromebook/) [错误的](https://headmelted.com/coding-on-a-chromebook-84335cce96c8) [和](https://medium.com/@martinmalinda/ultimate-guide-for-web-development-on-chromebook-part-1-crouton-2ec2e6bb2a2d) [错误的](https://gist.github.com/rachelmyers/d7023ef34e58fe925f9c)建议，导致:

*   痛苦的发展经历
*   一台廉价的 Linux 机器，有着糟糕的驱动支持
*   极度不安全和高风险的机器

那么，我的推荐与之前列出的有什么不同呢？

我有自己的一套坚实的指导原则，我将详细讨论。

注意:请记住，没有一种设置是没有缺点的。

## 

### 

ChromeOS 内置了一套非常——我是说非常——强大的安全控制。以至于 CoreOS，一个专门的容器服务器操作系统
是基于 ChromeOS 的。

因此，第一个建议是
“禁用所有这些安全功能，打开开发模式”，这是有道理的。

为什么开发模式不安全？

*   禁用验证启动。
    *   验证引导确保 ChromeOS 使用已知良好的固件、内核、初始化、模块、文件系统元数据、策略等进行引导。
    *   通过禁用验证启动，您实际上是在允许持久的危害。
*   启用 VT2 (Linux 终端)。
*   激活无密码的根外壳访问。是的，你没看错。
*   访问您的 Chrome 档案的未加密内容。

最重要的是，将油炸面包丁添加到侧装 Linux 发行版中又增加了一个巨大的载体。你实际上是在 Chromebook 上以 root 用户身份运行未经审核的代码。不仅如此，在 chroot 环境中以 root 用户身份运行的代码无法逃脱 chroot 并感染 ChromeOS 的其他部分。

> 本质上，默认的开发模式在物理上不如运行 Linux 的标准笔记本电脑安全- [大卫·施奈德](https://github.com/dnschneid/crouton/wiki/Security)(油炸面包丁的创造者)

如果你坚持要 Linux 发行版，你最好找一台旧的、二手的、便宜的联想 Thinkpad，并在上面安装你喜欢的 Linux 发行版。在开发模式下，你将拥有比 Chromebook 更安全的环境，比运行 Linux 发行版的 Chromebook 拥有更好的整体体验。

### 

*   避免任何会不必要地增加拥有成本的建议(例如，购买额外的硬件/软件来执行所需的任务)。
*   尽可能降低成本。

### 

*   利用平台的原生功能(例如，web 应用> android 应用>容器)。
*   避免影响性能的不必要的攻击。

### 

*   Chromebook 堆栈(硬件和软件)针对 ChromeOS 进行了优化。
    *   因此，你应该接受并接受决定使用 Chromebook 所带来的固有限制。他们最终迫使你把你的机器当成牛，而不是宠物。
    *   您的个人资料、偏好设置、设置和文件都存储在云中。如果您丢失了 Chromebook，您可以更换它，然后继续使用，几乎不会造成中断和不便。
*   试图绕过或减轻这些限制将导致妥协，影响堆栈的一个或多个方面(不兼容的驱动程序、缓慢/微弱的性能、电池寿命短、缺乏安全性等)。

### 

*   尽可能争取最流畅的开发者体验。
*   我将开发者体验定义为，但不限于:
    *   电池寿命
    *   表演
    *   生产率
    *   工具作业
    *   离线工作的灵活性/多功能性

## 

几年来，我一直将 Chromebook 作为我的主要个人电脑。因此，我尝试了各种不同的硬件和软件组合。我也非常熟悉适应这个新环境的最初痛苦。但是，一旦我适应了它，我注意到我已经把同样的哲学应用到我的工作设置中。

我也认为自己是一个经常有冲突需求的专业用户:

一方面，作为一名软件工程师，我更喜欢在远程开发环境中从事我的个人和工作相关的项目(我也希望在绝对需要时能够进行本地/离线开发)。
另一方面，作为一名信息安全工程师，登录远程服务器不是一个选择，我当然需要能够——呃，做一些事情😏—直接从我的本地机器。

这里是我着陆的地方…

### 

*   比起 ARM 芯片，我更喜欢 Intel 芯片。
    *   大多数开发人员程序和工具首先是为英特尔芯片构建的，并在其上得到支持。
*   更喜欢更大的内存。
    *   最低 4GB 内存。
    *   我发现 2GB 几乎不可用，特别是最近增加了对 Android 应用程序的支持。
*   喜欢更大的存储空间。
    *   64GB 的存储空间很好，但是 32GB 也可以。
    *   您需要考虑多少本地开发和远程开发让您感到舒服。

基于这些松散的硬件建议，以下是前 3 项:

*   华硕 Chromebook Flip C302(约 450 美元)
    *   英特尔 m3
    *   4GB 内存
    *   64GB 存储。
*   三星 Chromebook Pro(约 500 美元)
    *   英特尔 m3
    *   4GB 内存
    *   32GB 存储
    *   包含手写笔
*   工作用宏碁 Chromebook(约 400 美元)
    *   英特尔第六代 i3
    *   4GB 内存
    *   32GB 存储。

或者，这些产品更经济实惠，足以满足您的使用情形:

*   三星 Chromebook 3(大约。$200)
*   2017 款三星 chrome book 11.6 英寸(约。$250)
*   华硕 C300(大约。$200)

### 

在这里我会直截了当地告诉你:**舒适地生活在航站楼**。

Chromebooks 上基于 GUI 的本地软件开发 IDEs 编辑器还没有出现，但它们正在变得越来越好。

有一些轻量级的 GUI 编辑器，但是它们缺少软件开发所需的许多关键特性(例如 git 集成、搜索和替换、自动完成等)。这些轻量级的 GUI 编辑器对于轻量级的编辑/笔记来说是最好的。

这是一件好事，我会告诉你为什么:

Kubernetes 和 Docker 集装箱运动开创了一个 DevOps 时代，可以总结为“*宠物对牛*”:

> 在旧的做事方式中，我们像对待宠物一样对待我们的服务器，例如邮件服务器 Bob。如果鲍勃倒下了，那就全靠他了。首席执行官收不到电子邮件，这是世界末日。在新的方式中，服务器被编号，就像牛群中的牛一样。比如 www001 到 www100。当一台服务器出现故障时，它会在生产线上被拆除、拍摄和更换。- [兰迪偏见](http://cloudscaling.com/blog/cloud-computing/the-history-of-pets-vs-cattle/)

下面是我用于开发的基线软件栈(全部安装在服务器上):

*   Secure Shell:一个 Chrome 扩展，设置为“作为窗口打开”，是我远程访问远程开发环境(公共云用于工作，私人家庭服务器用于个人项目)的首选 SSH 客户端
    *   **公共服务公告:**由于在 Chromebooks 上安装客户端证书极其困难，我对我的服务器求助于基于密码的 SSH 认证。但是如果你这样做了，请帮自己一个忙，为 SSH 启用多因素认证(MFA)([DUO](https://github.com/petermbenjamin/dotfiles/blob/master/duo/duo-pam.sh)太棒了！)
*   Tmux:终端窗口和窗格管理
*   Vim:编辑器
*   Docker:进一步划分开发环境(例如，`docker-compose up`我有一个本地 NGINX 服务器，Ruby 应用服务器，& postgresql 服务器启动并运行)

以下是我用于黑客攻击的基线软件栈:

*   同上，加上
*   Termux:基于 android 的终端模拟器，用于在需要时执行本地测试。

仅此而已。我已经到了只需要一部电话、一台便携式显示器和一个键盘就能高效工作的地步。

这就是这段旅程的真正意义所在:让技术回归到我高效工作的最基本需求，我不再仅仅为了运行 slack、chrome、atom 或 vscode 而在身体上或情感上依赖于 16GB RAM 的优质拉丝铝合金电脑。

## 

我不认为这种设置适合所有人。不可否认，当你对一个软件有一个硬性要求，而这个软件又不能作为一个 web 应用程序使用，或者作为一个 web 应用程序在经济上不可行时，这种设置就会失败。

我真的很期待未来的发展。

也许，在不太遥远的未来，您可能需要的只是口袋里已经有的多核处理能力、虚拟现实(VR)或增强现实(AR)耳机以及虚拟工作空间(因为，当您可以创建无限数量的虚拟显示器时，为什么要受到昂贵的物理显示器的限制呢？)

但是，我很好奇你对这个设置的想法、意见或担忧，或者你是否愿意分享你的设置。

谢谢你的时间。

干杯，编码快乐！🤗