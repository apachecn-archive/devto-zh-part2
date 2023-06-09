# 使用 vim 是反模式吗？

> 原文：<https://dev.to/jacoby/is-using-vim-an-antipattern-19f5>

[!["OH, NED!", she exclaims. "YOU **ARE** A **VI MAN** AFTER ALL!"](img/edf13d07f4843bf4b82a46938f97613d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pUwyJElY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jacoby.github.img/vi_man.png)

这个思路是从一个 [Dev 开始的。到](https://dev.to/)题为[“Vim 不会让你成为一个更高效的开发者”](https://dev.to/maestromac/vim-wont-make-you-a-more-productive-developer-h9f)的帖子。阅读这篇文章以获得关于这一立场的完整论据，但是简单地说，如果你还不知道它，已经有强大的编辑器使用你已经知道如何工作的方式工作，比如 VS 代码，并且它可能更好地利用你的时间来学习其他东西。

我在 1995 年开始成为一名虚拟人，学习早期的 CS 课程。有很多东西要记在脑子里 Unix 新手？C 语言新手？编程新手？欢迎来到深水区！—我在 Sparc 5s 上度过了漫长的一夜，当我把`ESC`、`:wq`、`:q!`和`se nu`之类的东西放到我的手和头上时，我筋疲力尽。

时机成熟时，我找到了`colorscheme`、`nowrap`和`expandtab`。现在，我的。vimrc 有 196 行，最后修改的注释是 2011 年。我不相信这一点，但我相信我可以花一些时间，让它低于 100，也许更少，但删除我不使用的设置和功能。我不会，原因和文章说新用户大概不想学 vim 一样；这是一个时间水槽，我有更好的事情要做。

我回维姆是为了一些事情。

*   **排序**没有内置到 VS 代码中，虽然我已经找到了将`sort block`功能添加到其中的魔咒，但我已经为我的一台计算机设置了它，但不是所有的计算机，所以我不能依赖它。我主要是对 CSS 块这样做，因为如果你知道`display: block`将在`margin: 0 auto`之上，visual-grep 会工作得更好。
*   **PerlTidy** 是用 Perl 编写的，用于为你的代码设置一致的格式。“是用 Perl 编写的”意味着“如果你在 Windows 上，并且没有安装 Strawberry 或 Activestate Perl(有时甚至没有安装)”，以及“如果系统 Perl 滞后，可能无法理解你想要添加的新特性”，这在 my .vimrc 中的`map ,pt <Esc>:%! perltidy -q <CR>`中从来都不是问题。
*   远程工作意味着通过 SSH 进入一台机器并在本地进行修改。而*也可能*意味着必须以不同的用户身份登录。你不知道你能不能用 SMB 或 NFS 或其他类似的东西安装它，但是你*知道你能 ssh 进去，某种 vi 将会在那里。*

这是本周的大部分时间——没有避开墨菲复仇之怒的部分——我突然想到，相反，我应该能够在本地修改代码，在本地测试代码，提交更改，推送到我们的工作 Github 实例，并将 CI/CD 拉或推到位。就地开发，在生产中开发，这不是我在业余项目中做的事情，也不是我在日常工作中应该做的事情。

另外两个是这样的事情，如果我花真正的时间让其他计算机进入开发环境，而不是在我的主 Linux 机器上做大部分工作的同时在它们上面做一些小事情，那么这种事情现在可能已经解决了。

vim 本身并不是问题。It *是*一个支持我需要改变的工作流的东西。反模式。

我觉得受到了攻击。一个人。

如果你有任何问题或意见，我将很高兴听到它。在 [Twitter](https://twitter.com/jacobydave) 或[上问我，在我的博客回购](https://github.com/jacoby/jacoby.github.io)上做文章。