# 如何在 Mac 的 VS 代码中使用 zsh

> 原文：<https://dev.to/nyanev/how-to-use-zsh-in-vs-code-for-mac-39dp>

今天我将向你展示如何使用。VS 代码终端中的 zsh。

首先你需要安装. zsh，按照这里的说明操作:

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[ohmyzsh](https://github.com/ohmyzsh)/[ohmyzsh](https://github.com/ohmyzsh/ohmyzsh)

### 🙃一个令人愉快的社区驱动的(有 1800 多个贡献者)框架，用于管理您的 zsh 配置。包括近 300 个可选插件(rails、git、OSX、hub、docker、homebrew、node、php、python 等)，超过 140 个主题为您的早晨增添趣味，以及一个自动更新工具，使您可以轻松跟上社区的最新更新。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Oh My Zsh](img/da7b353e1ac29e3f23e58909717e96a2.png)T2】](https://camo.githubusercontent.com/3ec75cb1c3278cce3c661d3bcf72a4eca75db241a6ace648ea014b02f3f44458/68747470733a2f2f73332e616d617a6f6e6177732e636f6d2f6f686d797a73682f6f682d6d792d7a73682d6c6f676f2e706e67)

哦，我的 Zsh 是一个开源的、社区驱动的框架，用于管理您的 [zsh](https://www.zsh.org/) 配置。

听起来很无聊。让我们再试一次。

哦，我的 Zsh 不会让你成为 10 倍的开发者...但你可能觉得自己是。

一旦安装完毕，你的终端外壳将会成为全镇的话题或者你的退款！在你的命令提示符下每击一次键，你就可以利用数百个强大的插件和美丽的主题。陌生人会在咖啡馆里走过来问你，*“太棒了！你是某种天才吗？”*

最后，你会开始得到你一直认为应该得到的那种关注。...或者也许你会利用节省下来的时间开始更频繁地使用牙线。<g-emoji class="g-emoji" alias="grimacing" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f62c.png">😬</g-emoji>

要了解更多信息，请访问 [ohmyz.sh](https://ohmyz.sh) ，在 Twitter 上关注 [@ohmyzsh](https://twitter.com/ohmyzsh) ，并加入我们的 [Discord](https://discord.gg/ohmyzsh) 。

[![CI](img/58122a3ae1059d177c8c472b79f09652.png)](https://github.com/ohmyzsh/ohmyzsh/actions?query=workflow%3ACI)[![Follow @ohmyzsh](img/f6eca8206149480fee97daab1e67387a.png)](https://twitter.com/intent/follow?screen_name=ohmyzsh)[![Discord server](img/c253b4014ef0b26630dc98bc04582723.png)](https://discord.gg/ohmyzsh)[![Gitpod ready](img/f6cec0405cc0f427e18820fb3ae7bc06.png)T11】](https://gitpod.io/#https://github.com/ohmyzsh/ohmyzsh)

## 入门指南

### 先决条件

*   一个类 Unix 的操作系统:macOS，Linux，BSD。在 Windows 上:WSL2 是…

</article>

[View on GitHub](https://github.com/ohmyzsh/ohmyzsh)

然后打开 VS 代码的设置窗格，找到这个属性:*" terminal . integrated . shell . OS x "*。点击编辑按钮，将值设置为*“zsh”*

现在在 VS 代码中有了 zsh