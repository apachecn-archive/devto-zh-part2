# 铁锈历险记

> 原文：<https://dev.to/tmr232/adventures-in-rust-56fc>

在 Rust 问世后的几年里，我经常发现自己在解释 Rust 是一种多么神奇的语言。展示了它将改变系统编程世界的方式，以及编写代码是一件多么愉快的事情。

几个月前，我终于开始尝试了。太可怕了。该死的借书人就是不让我做任何事。我找不到任何有意义的东西来编译。尽管我用各种语言编程多年，但我觉得自己完全没有能力处理它。我决心放下铁锈，专注于其他事情。

尽管最初我感到失望和沮丧，但我仍然无法摆脱头脑中的铁锈。它的前提太有希望了，一次就放下了。另外，我不能被一门编程语言打败！然而，它可以等待。对现在来说这是太多的工作了。有一天，也许几年后，我会写《铁锈》。

然而，使用 Rust 是完全不同的体验。防锈工具绝对神奇。作为一个彻头彻尾的 Windows 用户，我已经习惯了 Windows 不支持开源工具(尤其是命令行工具)。(不，cygwin 不是有效的解决方案。)我不怪开发人员——他们在 Linux 上工作。即使他们有时间花在 Windows 端口上，他们也不一定有 Windows 机器来测试它。然而，我已经习惯了失望。这就是为什么，当我第一次听说 [`rg`](https://github.com/BurntSushi/ripgrep) (一个惊人的`grep`替代品)和 [`fd`](https://github.com/sharkdp/fd) (一个惊人的`find`替代品)时，我*知道*不能在 Windows 上工作。但是，作为乐观的我，我检查了一下。幸好我这么做了。

要安装 Rust 工具，最简单的方法是安装 Rust 工具集并编译它们。在其他语言中，这是一项艰巨的任务，但在 Rust 中却轻而易举。

1.  转到 [rustup.rs](https://rustup.rs/) 并安装 Rust(Linux 上的一个命令行，Windows 上的一个可执行文件)
2.  `cargo install ripgrep fd-find`
3.  就是这样。真的。现在使用工具。

这时我才意识到铁锈有多神奇。即使你完全忽略语言——它的工具和包管理是无与伦比的。在过去发布过 Python 包之后，我对简单的发布和安装机制感到惊讶。使用过 C 和 C++之后，我对带有包管理的系统编程感到惊讶。因此，尽管仍然有点害怕借用检查器，我决定我的下一个 CLI 工具将用 Rust 编写。易如反掌的部署彻底结束了。

几个月后，我终于发现自己需要一个新的 CLI 工具。我面临着一个连续的日志，我想过滤重复的。排序，所以它不能在流上工作。当然，可能有一些我可以使用的`sed`或`awk`魔法，但是我想要简单的东西。此外，一个过滤掉重复内容的工具似乎是上手操作 Rust 的完美初学者项目。所以我继续创造了 [`uq`](https://crates.io/crates/uq) 。写完之后发表在 [crates.io](https://crates.io/) 上。`cargo install uq`在第二台机器上，成功了。Windows 和 Linux 都可以。一个朋友试过，简直管用！我从未有过如此愉快的部署经历。从源代码安装然后发送一个编译好的二进制文件实际上更容易！它开箱即可跨平台工作。

过了一会儿，我想用正则表达式对一些日志条目进行分组。我环顾四周，找不到一个简单的方法来做这件事。所以，我又一次变成了铁锈。一些借棋角力之后， [`groupby`](https://crates.io/crates/groupby) 的第一个版本完成了。耶！

不久之后，我有了一次前所未有的最好的开源体验。有人开始用`groupby`，看了我可怕的代码，发了这个问题:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 小码建议 #1](https://github.com/lostutils/groupby/issues/1) 

[![vorner avatar](img/92727de75e647331bcbae5e54e6907fc.png)](https://github.com/vorner) **[vorner](https://github.com/vorner)** posted on [<time datetime="2018-03-15T16:26:48Z">Mar 15, 2018</time>](https://github.com/lostutils/groupby/issues/1)

你好

我发现这个小程序对我做的很多事情都很有用(我通常用 sed、sort、…)的组合来做类似的事情)。我也查了代码。我没猜错吧，你还在学生锈？我能提供一些小建议吗？

*   我发现至少有一个解包可以由用户输入触发(给出一个太大的组 ID)，这将导致难看的错误消息，而不是漂亮有用的消息。
*   你选择`BTreeMap` / `BTreeSet`是出于某种特定的原因吗？是元素的顺序吗？如果没有，`HashMap`和`HashSet`很可能会更快。
*   两种变体(unique vs all)看起来非常相似，不同之处仅在于内部数据类型和用于`push` / `insert`的方法。我认为这可以用一段对类型通用的代码来完成，并添加你自己的特征来实现这两者的相加。你想让我给你看这样的代码吗？

[View on GitHub](https://github.com/lostutils/groupby/issues/1)

有一个在 Rust 方面更有经验的人来帮助我改进我非常幼稚的代码真是太好了。这是我第一次听到“太好了，我能帮你吗？”而不是“这太棒了，我也想要这个”这样的评论。

就目前而言，我花在检查借书上的时间比用 Rust 写实际代码的时间还多。但我(几乎)肯定这是因为缺乏经验。另一方面，我也越来越善于发现其他语言中的生命周期问题。

所以，对于还没有做过的人，我强烈推荐使用基于 Rust 的工具。只为事物工作(和编译)的惊人体验！)开箱。稍后，如果您选择在其中实际编码，一定要做好准备，否则会有些颠簸。朋友告诉我，一段时间后生锈变得更容易，加速他们的发展。我还没到那一步，但我正在努力。