# 切换到了 Alacritty

> 原文：<https://dev.to/thermatix/switched-to-alacritty-2jfh>

我只是切换到使用 Alacritty(大约 40 分钟前，给予或采取)。这比我使用的 ITerm2 要简单得多，但老实说，我从未使用过它提供的任何额外功能，所以我没有错过任何东西。

它比 Iterm2 快得多，我还发现 Nvim 已经加快了很多，到目前为止最好的功能是它解决了我对 Iterm2(和苹果术语)最大的一个问题。我使用电力线字体，但一个最大的问题是 ITerm2 从来没有正确地渲染它们，结果电力线字符的尖端翘起来，结果使一切都有点难看。这个问题在 Alacritty 上是不存在的(至少对我来说)

至于负面因素，有一些。要将它作为一个`.app`来使用，你必须克隆回购协议，然后构建它，这不是一个大问题，但需要一段时间。

我确实很难找到我想要的字体。我告诉它寻找一种叫做`Knack Regular Nerd Font Complete`的字体，但它一直说它不存在。我发布了一个问题，有人说他们尝试了`Knack Nerd Font`,于是我尝试了一下，结果成功了。

最后，我在 tmux 上遇到了一个错误，状态行的最后一个字符被移到了下一行的开头。对某些人来说，这是一个交易破坏者，但对我来说，我可以忍受它出现在 neovim 的缓冲标签行(航空公司)旁边，所以我不会注意到它，而且我可以忍受输电线字符错位，只要我将 tmux 状态行的最后一行设置为`|`。我已经在 Github 上添加了一个[问题，所以希望它能尽快修复。](https://github.com/jwilm/alacritty/issues/1288)

无论如何，我真的很喜欢它，不能等待它变得更好。