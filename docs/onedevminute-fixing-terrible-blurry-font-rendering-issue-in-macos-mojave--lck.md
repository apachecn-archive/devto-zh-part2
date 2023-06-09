# 修复 macOS Mojave #OneDevMinute 中可怕的模糊字体渲染问题

> 原文：<https://dev.to/ahmadawais/onedevminute-fixing-terrible-blurry-font-rendering-issue-in-macos-mojave--lck>

刚刚更新到 macOS Mojave，却发现几乎所有基于 ElectronJS 的应用程序都有一个可怕的字体渲染问题，比如 [VSCode](https://VSCode.pro/) 我选择的代码编辑器。

不出我所料，经过一番研究后，我发现苹果已经在 macOS Mojave 中禁用了文本的亚像素抗锯齿功能。它不见了。

更多的研究导致了一个修复，你所要做的就是打开你的`Terminal`应用程序(`COMMAND (⌘) + SPACE`然后搜索`Terminal`)并运行下面的命令。

`defaults write -g CGFontRenderingFontSmoothingDisabled -bool NO`

[![5 Macos Mojave Font Rendering Issue Fix](img/3fdc49397fa55ec822f556957a594a47.png)T2】](https://ahmadawais.com/fix-macos-mojave-font-rendering-issue/)

这将导致启用字体平滑，特别是对于非 retina 外部显示器。你以后会感谢我的。**运行此命令**后，您需要注销并重新登录或重启 Mac 才能看到您的更改。差不多就这些了。

## 🧐

## 说明:那里发生了什么？！

一些人在评论中问我是如何偶然发现这个补丁的，所以为了技术诀窍，我更新了这个帖子，提供了更多的细节。

🎯苹果的 macOS 通常有一组`defaults`，你可以通过编程来改变。每当我从零开始安装操作系统时，我都有这样一个固执己见的工作流程。那是以后的事了。

🤖我记得在过去，有了 High Sierra，我必须在非 Apple LCDs 上启用子像素字体着色。这是我设置为 true 的`AppleFontSmoothing`选项，或者可以直接为该选项添加`-int 1`。

🤔我在 macOS Mojave `defaults`中找不到那个设置。我一直在里面找。尤其是在`Apple Global Domain`设置中，因为那是旧的设置。

🙃啊哈！我在那里发现了一个新的设置，它叫做`CGFontRenderingFontSmoothingDisabled`——很明显这个设置是做什么的。禁用平滑字体的呈现。它被设置为`1`，这意味着它被设置为`TRUE`或`YES`。

[![Macos Mojave Cgfontrenderingfontsmoothingdisabled 1](img/0a3a7d21f85f021bb799b6780ef9bd22.png)T2】](https://AhmadAwais.com/)

所以，我所要做的就是试试我的运气，禁用这个字体平滑禁用器，即在非苹果/非视网膜 led 上启用字体平滑。为此，我编写了以下命令:

`defaults write -g CGFontRenderingFontSmoothingDisabled -bool FALSE`

*   **默认值** —访问 Mac OS X 用户默认值系统
*   **—g**|**—全局域** | **NSGlobalDomain** —指定全局域。-g '和'-globalDomain '可以用作 NSGlobalDomain 的同义词
*   **CGFontRenderingFontSmoothingDisabled**—我想禁用的设置，以启用字体平滑
*   **—布尔值**【ean】—允许用户指定一个布尔值作为给定首选项的值。该值必须是`TRUE/YES`或`FALSE/NO`

我运行这个命令，通过登录和退出来检查它是否有效，结果它成功了。超级开心。我希望苹果在不久的将来的更新中解决这个问题，而不是完全禁用这个选项。

[![Macos Font Fix Cgfontrenderingfontsmoothingdisabled](img/ebcc28121fcfaa12c63f56def9414e43.png)T2】](https://AhmadAwais.com/)

🎉因为我是 [VSCode](https://VSCode.pro/) 的忠实粉丝，所以我做了一个默认设置的前/后`--diff`，看起来设置现在被设置为`0`了，它工作得太好了！

[![Macos Mojave Defaults Changed Font Fixed](img/bc312f2b1271db34ccb456204f29840d.png)](https://AhmadAwais.com/)[/corner _ full page _ image]

⚠️有些人还建议在`General`字体设置中启用字体平滑选项，但这对我不起作用。它已经启用，但对改善局势没有任何作用。

[![Font Blur Issue Macos Mojave](img/ed405ed32c5a4a4d5101402bdad30192.png)T2】](https://ahmadawais.com/fix-macos-mojave-font-rendering-issue/)

和平！✌️

*这是第一次发布在[修复 macOS Mojave 字体渲染问题](https://ahmadawais.com/fix-macos-mojave-font-rendering-issue/)T3】*