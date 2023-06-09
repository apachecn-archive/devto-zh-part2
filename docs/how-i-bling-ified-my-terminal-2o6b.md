# 我如何美化我的终端

> 原文：<https://dev.to/chiangs/how-i-bling-ified-my-terminal-2o6b>

[![](img/f9fd991b1ac71aa953bb8f5e4a72283c.png)T2](https://res.cloudinary.com/practicaldev/image/fetch/s--Ume68FQ_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1wfjklunj5gwf19qi1vn.jpg)

> 不要害怕终端，它很神奇！
> 
> ——表示从来没有初学者。

首先，这不会让你成为一个更好的开发人员，但这是一个令人敬畏的快速而简单的改变，你可以立即让你在控制台上的时间赏心悦目，并让你*感觉*好像你是 2000 级的开发人员。至少，您将从中获得更多的效用和便利(对于像我一样有在工作前忘记检查自己在哪个分支的坏习惯的人来说)。

*前* [![](img/958fd5e6573f9415e14faa4e27875275.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EdLIzOgl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wzgjxjmfkdctk5u7bd17.png) 
*后* [![](img/dbfc4ffd09daf6215133c7111900e9bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Bt5_tEXy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i3efv77855ooh0pxgf28.png)

随着我花更多的时间开发，我发现自己越来越多地盯着我的控制台。我想说的是，我现在对它相当满意，真的很喜欢移动文件和目录，并快速创建新的目录和文件结构，而不是重复的右键单击>>新的废话。

你们中的许多老手已经听说过不同的 shells，所以这是为初学者准备的。

### 什么是 shell，它是做什么的？

对于 Mac 和 Linux 发行版，最常见的缺省 shell 是`Bash`。对于那些使用 Windows 的人来说，你可能正在使用`PowerShell`。`Bash`是一个很棒的 shell，它提供了诸如命令和文件名完成、高级脚本特性、命令历史、可配置颜色、命令别名等功能。

另一种更新、但非常相似的 shell，`Z shell`或`Zsh`是我们今天要讨论的。要详细了解 bash 和 zsh 的区别: [Zsh vs Bash](http://stackabuse.com/zsh-vs-bash/) 。因为我还不打算为其他开发人员编写脚本，所以切换到 zsh 是安全的，然而，在活动终端中使用
在两者之间切换是非常容易的

```
\> exec bash or $ exec zsh 
```

Enter fullscreen mode Exit fullscreen mode

老实说，我只是想让 zsh 提供一些便利的功能，比如更相关的自动完成功能，(试着用 zsh 输入`git &lt;tab&gt;`)主题、插件和语法突出显示，也就是彩色化，因为像所有有设计头脑的开发人员一样，如果我每天都在使用它，这些东西必须看起来很漂亮并且工作得很好。我确信随着我的进步，zsh 的真正力量会变得更加清晰。

关于更多细节，这里有一篇很棒的文章:【Bash、Zsh 和其他 Linux Shells 有什么区别？

### 定制

基本的终端相当乏味，但是当您用类似于 [iTerm2](https://www.iterm2.com/) 的东西替换默认的终端应用程序时，您已经在操纵 shell 环境方面有了一个很好的开始，比如在内存中保留多少行，或者文本的着色。

但是有了 zsh，你可以得到更多有趣的东西，而且真的很容易管理。我是这样做的:

### TL；Z 博士外壳安装和定制

这些步骤在 Mac 上；关于 Linux 和 Windows 等其他平台的说明，请参考主要的 zsh repo。通常 zsh 包含在 Mac 发行版中，你只需要检查版本。回购的链接在本文的末尾。

#### 安装 zsh

*检查 zsh 是否已经预装在你的机器上*

*你会想要 5.1.1 或更新的版本*

在您的终端:

```
zsh --version 
```

Enter fullscreen mode Exit fullscreen mode

您应该将 zsh 设置为默认 shell。

从新终端运行`echo $SHELL`进行确认。

预期结果:`usr/bin/zsh`或类似

如果没有，下面是安装 zsh 的说明:[安装 ZSH](https://github.com/robbyrussell/oh-my-zsh/wiki/Installing-ZSH)

在 Mac OSX 上，你可以打开用户和群组面板，进入你的用户的高级选项，将登录外壳设置为类似于`usr/bin/zsh`的东西。

#### INSOL oh-my-zsh

oh-my-zsh 只是一个用于管理 zsh 配置的框架，但它是一个非常棒的框架，使管理它变得非常容易，非常值得。*搞定*。

回购协议提供了两种使用 curl 和 wget 获得它的方法，请查看回购协议的最新版本: [Oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh/) 。有非常棒的文档说明了它是如何工作的，以及它的去向。

*   通过 curl 或 wget

```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)" sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)" 
```

Enter fullscreen mode Exit fullscreen mode

#### 这个。zshrc

这相当于。bash shell 的 bash_profile，更有帮助，并附带了如何覆盖默认设置的说明。我只是从我的`.bash_profile`中复制了我的别名，但是你可以通过`.zshrc`或者类似的方式导出它来创建一个 bash 和 zsh 都可以引用的`.profile`。你的电话。但是这个文件对于别名来说是很棒的，它允许你输入常用目录的快捷方式，甚至是远程回购，等等。

您可以使用 vim、vs code、atom 或 plan text note pad 打开这个文件。

`∆ code ~/.zshrc`

就这样你就搞定了，找一个你喜欢的主题就行了，如果是预包装的，你只要在`ZSH_THEME="nameOfTheme"`里改个名字就行了。

如果你想下载一个自定义的或者一个没有预装的，你只需要下载它并把它放在`custom/themes`目录中，然后改变名字`oh-my-zsh`就会为你找到它。

插件也一样，它什么都有，你只需要把它们的名字加到`plugins=()`行就可以激活它。至少你会想要一个`git`的。

另一个最喜欢的非。zshrc 预打包插件是一个语法高亮器，它告诉你输入的命令是否正确。GitHub–Zsh-users/Zsh-syntax-highlighting:Zsh 的类似鱼壳的语法突出显示。

[![](img/a7099214e59ec9efc1005998aa2958cc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4xsbhrpx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/www.chiangs.ninja/blog/wp-content/uploads/2018/03/Screen-Shot-2018-03-15-at-21.26.47.png%3Fresize%3D277%252C372%26ssl%3D1)

只需按照下载和安装的指示，不要忘记将其添加到。zshrc 文件是这样的:`source $ZSH/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh`

每次对该文件进行更改时，不要忘记`∆ source ~/.zshrc`。

#### 文本编辑器集成

当然，我也想把它添加到我最喜欢的文本编辑器中，VS 代码！

在设置文件中，键入`shell`并滚动到底部，您将看到为集成终端使用的 shell 设置的路径:`"terminal.integrated.shell.osx": "/usr/local/bin/zsh"`

如果还是说 bash，那就改成 zsh。重启你的 VS 代码，打开集成终端，它应该会说。终端窗口右上角的 zsh。

在这一点上，我观察到一个奇怪的行为，bash 和 zsh 明显地来回翻转了几次，如果终端启动缓慢，请仔细检查您的。zshrc 简介和你的`$PATH`。我有几个重复的路径垫片，所以我必须清除这些，现在终端立即启动。

#### 有一些非常酷的主题，比如任何一个电力线版本，但我更喜欢保持简单。在下面的评论里扔掉你的终端，告诉我们你在用什么！

***链接转存和安装说明***

[Zsh](http://www.zsh.org/)

[哦我的 Zsh](http://ohmyz.sh/)