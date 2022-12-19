# 你对多重包管理器有什么想法？

> 原文：<https://dev.to/sudiukil/what-are-your-thoughts-on-multiples-package-managers-elh>

我刚刚意识到:我曾经因为一件事(许多事中的一件)而喜欢 Linux:事实上所有的东西都是用一个集中的包管理器安装的。与窗户相比，它脏多了。当我在 2009 年发现 Linux 的时候，这里似乎是天堂。

今天我意识到完全相反:我现在使用许多不同的包管理器来安装我日常使用的软件。一些例子:

*   我用眼色，装着皮普
*   我用的是 Rubocop，装的是 Gem
*   我用 TLDR，装了 NPM
*   我刚开始用 exa 和 bat(感谢这个[牛逼的帖子](https://dev.to/autoferrit/what-are-your-favorite-terminal-apps-or-scripts-to-use-for-productivity-404b))，都装了货

不仅我使用的许多软件包是通过外部软件包管理器安装的，而且这些软件包管理器本身甚至不是通过我的发行版的软件包管理器安装的！例如，Rubocop 是通过 Gem 安装的...通过 RVM 安装...通过脚本安装。NPM(通过 NVM 安装)和 Cargo(通过脚本安装)也是如此。与我的发行版的包管理器一起安装的唯一的包管理器是 PIP。

我注意到的另一件事是:许多“现代”软件甚至不再提供真正的 Linux 包(例如 deb 或 yum)。其中一些只提供了一个 AppImage(比如 BitWarden)或者只是一个. tar.gz(比如 Postman)。

除了我必须管理多个“软件列表”(例如，如果我想在重新安装之前列出它们)和我不能在一个命令中更新我所有的包之外，我对此并不特别烦恼。而且感觉我用的所有软件都分散在各处...还好我没有强迫症😀

你对此有什么想法？你使用许多软件包管理器吗？为什么或为什么不？如果是，你认为这是好事还是坏事？