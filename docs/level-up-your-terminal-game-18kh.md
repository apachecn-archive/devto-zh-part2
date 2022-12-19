# 升级你的终端游戏

> 原文：<https://dev.to/bdmorin/level-up-your-terminal-game-18kh>

一位同事在我们公司的 slack 频道上发布了一个关于 [alacritty](https://github.com/jwilm/alacritty) 的链接。Alacritty 是一个基于 gpu 的终端渲染控制台应用，用 Rust 编写。我在台式电脑上用的是 mac，所以我不得不摸索着用 Rust 来启动它，多亏了[自制软件](https://brew.sh/)，这相当容易。

我很惊讶地发现它的性能是如此惊人。自从我在 2009 年左右转用 Mac 以来，我一直在使用 iTerm，我的很多工作流程都与 iTerm 集成在一起。我真正没有准备好的是，我应该对终端应用程序有多高的期望。

我使用 [rg](https://github.com/BurntSushi/ripgrep) 运行了一些基本测试，我知道它会向终端输出数据。我的测试是在我的项目目录中简单地搜索‘perl’。它返回 ascii 和二进制文本的混合包，确切地说是 176002 行。

所以我在终端中记录了每个输出的时间。我明白，这不是最好的测试方法。然而，不管怎样，结果是惊人的:

*   [alacritty](https://github.com/jwilm/alacritty) : rg perl 7.28s 用户 28.04s 系统 21% cpu 2:43.80 总计
*   [iTerm](https://www.iterm2.com/) : rg perl 7.15s 用户 26.87s 系统 0% cpu 58:14.69 合计
*   [kitty](https://github.com/kovidgoyal/kitty) : rg perl 6.26s 用户 19.47s 系统 11% cpu 3:35.96 总计
*   [hyper](https://hyper.is/) : rg perl 6.65s 用户 16.02s 系统 25% cpu 1:27.68 总计

alacritty 滚动数据的速度令人震惊，scrollback 的速度是我滚动鼠标滚轮的最快速度。

iterm2 完全符合我的预期，很好。

kitty 感觉就像一个小玩意，但感觉更像一个成熟的项目。

hyper *是*终端，因为我喜欢它的可配置性，wingdings 补充道，然而，它的性能只比 iterm2 稍好。不幸的是，我的测试结果不支持我的感觉，hyper 的“时间”结果比 iTerm2 更差，但它肯定完成得更快。

因此，差异是强烈的，令人兴奋的。发送所有内容大约需要 3 分钟。Iterm2 用了将近一个小时。凯蒂比阿拉克里蒂长了大约一分钟。然而，很明显，基于 alacritty/kitty/gpu 的终端仿真器提供了令人难以置信的性能。

对我来说，Kitty 是个很棒的选择。它感觉更成熟一点，可以通过自制软件(brew install kitty)获得，在我的 Antergos linux destktop 上运行得很好。在 vim 中滚动，tmux scrollback 如丝般平滑，字体(一旦你解决了)令人惊叹。~~我用的是 powerline9k 和 prezto~~ ，kitty 处理我的提示没问题。

Kitty/Alacritty 需要手动配置文件，并且正确使用字体可能是一个挑战。如果你在 mac 上，你可以用这个命令`fc-list : family | rg -i powerline`节省大量的时间，这个命令会给你在配置中输入字体。

如果你有更好的方法来测试终端性能，我很乐意尝试一下。