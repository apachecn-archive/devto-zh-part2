# 改善您的 Windows 开发环境

> 原文：<https://dev.to/antjanus/improve-your-windows-development-environment-59m0>

大约 4 年来，Windows 一直是我的主要开发环境，其中穿插了一些 linux。在此之前，我交替使用 OSX 和 Windows。当我做出改变的时候，我写了一篇关于我的 Windows 开发环境的文章，今天，我想写一篇关于如何让你的 Windows 机器成为 web 开发的最佳场所的更新和介绍，不管你使用什么语言。

事不宜迟，我们开始吧！

## 终端

在上面链接的前一篇文章中，我提到了几个选项。嗯，不会再有了。我遇到了一个赢家，那就是康内姆。什么是 [ConEmu](https://conemu.github.io/) ？嗯，它是一个终端模拟器，可以运行 PowerShell、CMD、Bash、Cygwin 和几乎任何其他终端。

它有跳转，颜色设置，自定义字体，热键，等等。没有它我无法工作。非常欢迎您复制我的 [conemu.xml 设置文件](https://github.com/AntJanus/my-dotfiles/blob/master/windows-specific/conemu.xml)，其中包含我最喜欢的快捷方式:

*   `ctrl + win + l`和`ctrl + win + h`在标签之间切换
*   这将粘贴带有换行符的文本
*   `ctrl + shift + t`这将打开一个对话框，用终端设置创建一个新标签(常规`ctrl + t`只是用你的默认设置打开一个新标签)

ConEmu 最大的优点是它可以为你处理这些标签。如果愿意，可以用 webpack、node server 运行一个选项卡，用 Bash 运行另一个选项卡。

如果你想要一个稍微预配置的版本，可以查看一下 CMDer,它有很多关于 CMD 的定制。

## 控制台/外壳

在离开终端之前，我强烈推荐用 PowerShell 和子系统 Bash 设置 ConEmu。我运行一个默认为 PS 的系统和一个默认为 Bash 的系统，老实说，这取决于你做什么，但我注意到，即使我想主要使用 Bash，我仍然不得不偶尔跳到 PowerShell。

PowerShell 真的很强大，很神奇。我只接触了它的表面，但对我来说，它是 Bash 和其他 shells 的好对手。另外，命令的输出通常比 Bash 提供的更好！PowerShell 也可以像 Bash 一样进行配置。检查我的[个人资料 PS1 文件](https://github.com/AntJanus/my-dotfiles/blob/master/windows-specific/Microsoft.PowerShell_profile.ps1)并确保安装 [Posh-Git](https://github.com/dahlbyk/posh-git) 以获得完整的 Git 支持。

我确实在 Windows Linux 子系统中使用了 Fish，并且发现它非常酷。我对它的了解肯定不足以配置 100%的生产力，但它工作得很好——足以让我喜欢它胜过普通的 Bash。

## VIM

尽管我是 VS 代码的全职用户，但我仍然为一些小事而投身于 VIM 无论是快速的文件编辑还是仅仅想要一个良好的 Golang 开发体验。以备不时之需是值得的。

令人惊叹的是，VIM 8.1 现在支持在 VIM 内部运行终端。

可以从[官网](https://www.vim.org/download.php)下载 VIM。

## 包管理器

好吧，所以我有一个有争议的观点:不要打扰。巧克力很棒，但我发现直接找到源头更好。

节点安装和升级非常简单。长生不老药或 Golang 也是如此。升级是否需要不止一个命令？没错。但这并没有看起来那么严重。预编译二进制文件。

## 码头工人

我在工作中使用 Docker，它很棒。Windows 支持也很好，但你需要 Hyper-V。Hyper-V 基本上禁止使用 VirtualBox 做任何事情，所以要知道使用 Docker 通常意味着你不能使用 VB。

如果您想在 Linux 子系统中使用 Docker，我建议您遵循本指南中的[，它将带您一步一步地在 Linux 子系统中通过命令行无缝地使用 Windows Docker 系统。](https://medium.com/@sebagomez/installing-the-docker-client-on-ubuntus-windows-subsystem-for-linux-612b392a44c4)

## 记事本++版

如果你在 windows 上进行设计，你一定听说过 Np++。Notepad++是迄今为止我见过的最好的*文本*编辑器。它作为便笺本、快速待办事项列表、降价编辑器、CSV 查看器、文本操作器等等非常好用。我用它做什么？

*   每日待办事项列表
*   编辑我的降价
*   编写 SQL 查询
*   编辑/清理 CSV(有一个很棒的 CSV 插件)
*   当我使用浏览器时快速编辑(如果我在终端，我使用 VIM)

很强大，很神奇。现在就去[下载它](https://notepad-plus-plus.org/)。

## 代码编辑器

我多年来一直是 VIM 纯粹主义者，但 VSCode 让我神魂颠倒，为我戴上了一枚戒指。在此之前，我尝试过 Emacs、Atom 和其他一些代码编辑器。在 windows 上，它们都运行得相当好。

我仍然保留 VIM，并希望对我的`.vimrc`进行硬复位，重新开始。我也把 Emacs 留在身边，希望能重新投入进去。它的组织模式是为之而死的，没有一个替代方案能如此好地工作。

对于 VSCode，我尽量保留少量的扩展。我使用的语言、工具和框架越多，我需要的扩展就越多。在多样化的生态系统中工作越多，这种扩展蔓延是不可避免的:

*   Angular 语言服务.读取组件类型脚本文件的 angular 模板的代码完成
*   括号对着色——根据嵌套层次改变括号的颜色
*   Docker - docker 语法高亮显示
*   DotEnv-`.env`文件的语法高亮显示
*   VS 代码编辑器配置支持的编辑器配置
*   榆树-榆树支架
*   Eslint - JS 过磅机
*   Gitignore -对`.gitignore`文件的语言支持
*   材料图标主题
*   npm 智能感知-已安装的 NPM 模块的自动完成
*   org-mode -对著名的 Emacs org-mode 的基本组织模式支持
*   路径智能感知-文件名自动完成
*   polacode -选择代码并将其导出为美丽的图像
*   PowerShell - powershell 脚本语法突出显示
*   更漂亮的格式支持
*   美化 json——让 JSON 看起来更漂亮
*   锈锈 lang 支架
*   Sass - sass 支持
*   设置同步**超级重要** -这将把设置上传到 gist 并下载。过程是手动的，但它帮助我保持我的 VS 代码安装同步
*   CSS/SCSS/Less 的棉绒
*   打字稿
*   Vim - Vim 仿真！没有它，我不会使用 VS 代码
*   VS 实时分享-实时协作/分享
*   vscode-elixir - Elixir 语言支持
*   vs code-Elixir-formatter-Elixir 文件的格式化程序

## 实用程序

我收集了一些我认为值得分享的小工具，让我的生活变得更简单:

1.  [Lightshot](https://app.prntscr.com/en/index.html) -一款出色的截屏工具

## 是吗？

我现在想不出其他的东西，但应该就是这个了！