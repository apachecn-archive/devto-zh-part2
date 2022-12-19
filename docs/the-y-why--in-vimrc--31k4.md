# vimrc 中的“Y”(为什么)

> 原文：<https://dev.to/mohitkalra/the-y-why--in-vimrc--31k4>

[![vim image](../Images/636eea906a7177d6bd63ed069e4dfaca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--z0XpdMMy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.vim.org/images/vim_editor.gif)

我是一个狂热的 vim 用户。我在 Windows 上用过 [GVim](https://www.vim.org/download.php#pc) ，现在在 Mac 上用 [MacVim](http://macvim-dev.github.io/macvim/) 。我也在需要的时候从终端使用它。我喜欢 vim 的原因有很多，但这篇文章不是关于那个或关于 vim 的教程。如果 vim 是你的编辑器选择，你会喜欢这个系列，因为我将分享“为什么”[我的 vimrc](https://github.com/technochakra/config/blob/master/vimrc) 看起来是这样的。

我了解到许多人更喜欢标准的现成工具，而不喜欢定制它。相反，我喜欢根据自己的需求配置 vim。我的 vimrc 是我的想法和一些在互联网上分享配置的 vimmers 伙伴的复制粘贴的结果。

## 管理 vimrc 文件。

在第一部分中，我讨论了 vimrc 文件本身和一些管理它的技巧。

### 使 vimrc 始终可用:

你的第一个目标应该是当你设置一台新的计算机时，你应该能够在最少的时间内配置你最喜欢的编辑器。我的大多数设置和插件都在我的 vimrc 文件中。我通过一个插件管理器安装所有插件(稍后会详细介绍)。

为了在最短的时间内高效工作，我要求我的 vimrc 能尽快提供给我。我通过以下方式实现这一点。

1.  我将我的 vimrc 发布给一家公开的 github.com 回购公司。在将文件提交到公共回购之前，确保删除文件中不想共享的专有内容。
2.  我的 vimrc 位于我的数据文件夹中——一个包含我关心的内容的文件夹。这些数据会定期备份。因此，即使我无法访问 github.com，我通常也可以通过备份使用我的 vimrc。

我不在默认位置使用 vimrc。我总是将文件同步到一个文件夹中，比如~/config，然后修改默认的 vimrc 文件，并将我同步的文件作为源文件。

```
cd ~
git clone https://github.com/technochakra/config.git
echo so ~/config/vimrc > ~/vimrc 
```

Enter fullscreen mode Exit fullscreen mode

### 配置是代码。

如上所述，我将我的 vimrc 文件签入 github。如果你是一名软件开发人员，我们已经到了代码、基础设施、配置应该被当作代码并定期检入的时代。vimrc 也正好属于这一类。

从头开始重建您的 vimrc 文件是痛苦的，不应该这样做。

### 避免默认值:

我最近意识到我的 vimrc 文件包含一些明显的缺省值。如果你查看一个设置的帮助，它默认为你所需要的，你不需要在你的 vimrc 文件中有它，除非一些插件覆盖了你的默认设置。

```
help errorbells 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，默认情况下，errrorbells(出错时发出嘟嘟声)是 **off** 。所以，您的 vimrc 文件不需要下面一行。

```
set noerrorbells 
```

Enter fullscreen mode Exit fullscreen mode

拥有一个最小的 vimrc 文件使它更易于管理，并且当您几年后再次访问它时，花更少的时间就能弄清楚它的设置。

### 一写就源 vimrc

当我修改 vimrc 文件时，下面的命令将它作为源文件。这帮助我立即尝试我的更改，立即修复错误，并给我一个总是工作的 vimrc 文件。看到你的设置马上生效也是很满足的。

```
autocmd bufwritepost vimrc source ~/config/vimrc 
```

Enter fullscreen mode Exit fullscreen mode

当我修改我使用的插件时，这不起作用，但是我将在后面介绍这一点。

### 无绒 vimrc

如上所述，我喜欢像对待代码一样对待我的 vimrc 文件。那么，就没有借口不静态分析它的警告和错误了。我使用 [vint](https://github.com/Kuniwak/vint) 作为我的 lint 程序。出于某种原因，当从与之集成的插件中调用 vint 时，它不能在 MacVim 上工作，所以我最终从命令行使用 vint。

```
vint ~/config/vimrc
Sample output: <some_path>/vimrc:1:1: Use scriptencoding when multibyte char exists (see :help :scriptencoding)_ 
```

Enter fullscreen mode Exit fullscreen mode

vint 是避免常见错误的好工具。例如，我不知道 augroups，直到该工具为我标记了它们。

```
<some_path>/vimrc:31:3: autocmd should execute in an augroup or execute with a group (see :help :autocmd) 
<some_path>/vimrc:32:3: autocmd should execute in an augroup or execute with a group (see :help :autocmd) 
```

Enter fullscreen mode Exit fullscreen mode

事实证明，通过将 autocmd 放入一个 augroup，并使用 autocmd 清除该 augroup！命令，可以防止在再次对文件进行源处理时，在您的 augroup 中出现 autocmds 的副本。这使事情保持干净和最佳状态。

```
augroup NERDTREE 
autocmd! 
autocmd vimenter * NERDTree
augroup END 
```

Enter fullscreen mode Exit fullscreen mode

管理 vimrc 文件相当简单。这有助于不断清理它，并保持其最小化。现在我们有了维护 vimrc 文件的结构，我将在后续的文章中讨论我使用的设置。