# VSCode vs VIM 我的编辑器之旅:sublime，VIM，emacs，VSCode

> 原文：<https://dev.to/lucasprag/my-editor-journey-sublime-vim-emacs-vscode-19k0>

我将谈谈我使用每一个文本编辑器的经验，也许对那些评估文本编辑器的人来说花时间学习会有帮助。

## 崇高的文字

我在高中时开始学习编程，写一些代码并在浏览器上看到结果是如此有趣。我使用崇高的文本，因为我是通过博客帖子和视频学习的，而且大部分内容都提到崇高的文本。此外，它很快，我安装了一些插件来处理 php，html，javascript 和 css。我在工作中也用了几年，直到有一天我加入了一家有 vim 和 emacs 用户的公司。

## vim

vim 用户从事的项目更接近我自己的项目，所以我开始检查他们是如何做事情的，然后我开始了解 vim。它并不酷，学习如何使用它太难了，每次加载新的配置文件我都必须重启 vim，还有许多其他问题，但既然他们这么做了，我就说服自己我也能学会它，所以我也这样做了。

我使用 vim 有几个月了，我最喜欢 vim 的地方是它为编辑文件而不是创建新文件进行了优化。大多数时候，我们编辑文件，在代码行之间移动，这样才有意义。最后，使用 vim 我很开心，也很有效率。

## neovim

有一天，我听说了 neovim，但不确定我为什么转而使用它，但看看 github 项目中的两个项目:

[![vim project with only one contributor](img/546dc9c4f2ee08c135c3a74dab052255.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zXs672Lh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mr475vfxouxinfcgxeaa.png)

[![vim project with 392 contributors](img/da808ce413a8d96a1a1fea765bf557d6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hMDTU5ew--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oogdvczt4j9mt5v49jxu.png)

等等什么？vim 存储库中只有一个贡献者？为什么只有一个人能做到？检查拉取请求列表时，我看到作者应用了其他人的更改，因为这是他自己的更改。好吧，我不喜欢这个，所以我换成了 neovim，当人们做出贡献时，它会给他们加分。

更新:我承认我的调查太肤浅。我的见[此评论](https://dev.to/heast/comment/4gk4)。但我还是不喜欢那样。

## nvim + tmux

那时人们在谈论`tmux`，它是一个终端多路复用器，所以你可以拆分你的终端，拥有标签，定制一切，从快捷方式到 tmux 的外观，例如在你的状态栏中添加有用的信息。我试过了，我喜欢。自从我在终端上使用 nvim 以来，它工作得非常好，我甚至找到了一些插件来平滑地集成它们。它看起来是这样的:

[![using tmux and vim having different tabs and splits and running test in a split bellow](img/b72feba67ec0836d5d29899aff052ff1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZXT-03_N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u4ncf7ir9rfeb0lx700j.gif)

我不再使用 nvim 了，但是你仍然可以找到我对 [vim](https://github.com/lucasprag/vimlociraptor) 和 [tmux](https://github.com/lucasprag/dotfiles/blob/master/tmux.conf) 的配置。

我用了这个堆栈 3 年，我很高兴。

然后我看到自己管理 nvim、tmux、zsh 的配置和插件，以便在我的 Debian 上的 terminator 上运行良好，在家里的 macOS 上运行 iTerm。并非插件之间的一切都很好，例如，我在 nvim 上有自动补全功能，它工作得很好，除非我使用多个光标，这是另一个插件的功能，自动补全功能只对第一个光标有效，因为它们没有相互集成。我还有其他类似的令人沮丧的问题，我看到自己花了太多时间调试或尝试替代插件，所以我尝试了一些不同的东西，这就是 emacs 引起我注意的地方。

## emacs

Emacs 有一个 GUI 应用程序，让我不再需要 terminator 或 iTerm，它有自己用 elist 编写的 shell，名为 eshell，所以我有一个配置和插件就可以做任何事情。我试着从零开始写下每一个配置，就像我用 vim 做的那样，但是要学的东西太多了，我必须完成一些工作，所以我尝试了 spacemacs，在一些帮助下，我可以很快提高效率。

Spacemacs 有 vim 键绑定，所以我在家，我只需要学习处理 spacemacs 的不同方面，如布局和工作空间。因为它有可用键绑定的实时显示，所以当你键入时你可以看到你的选项，而且它是助记符，所以如果你想要布局你可以使用`SPC + l`，如果你想要切换某些东西你可以使用`SPC + t`，如果你想要缓冲你可以使用`SPC + b`，对于项目`SPC + p`，对于文件`SPC + f`和保存文件你可以组合像`SPC + fs`这样的命令来查看文件的项目树。很简单，对吧？

我花了一个月的时间去理解所有的事情，并且做得很好。我对一切都不满意，我发现了一些错误，这没什么，我报告并试图修复它们，但我必须再学习一种语言(elisp)才能做到这一点。这是一次很棒的经历，因为我学到了很多 elisp，在我看来这是一门很棒的语言，所以我很开心，这让我用了 6 个月的 spacemacs。

然后我看到了这个视频:

[https://www.youtube.com/embed/8kCd4w4kc68](https://www.youtube.com/embed/8kCd4w4kc68)

等等什么？10 年对 Emacs 的热爱被一周的 VSCode 毁于一旦？我有一些朋友也很喜欢 vscode，他们强烈推荐它。好吧，我们试试看。

## vscode

我不得不在一个周末做一些编码，所以我切换到了 vscode，总体来说体验很棒，但我最初不得不经常使用鼠标。

我安装了 vim 键绑定，令我惊讶的是，它还支持常见的 vim 插件，如`vim-easymotion`、`vim-surround`、`vim-commentary`以及其他我从未听说过的插件。

我在 vscode 中发现的黑仔特性:

*   使用模糊搜索打开文件更快、更智能(将最近的文件放在最上面)
*   快捷方式与浏览器快捷方式更相似，这让我作为 web 开发人员的生活更加轻松
*   配置文件是`JSON`文件，不需要太多的定制(我的配置文件有 22 行，仅此而已)
*   集成多终端工作得非常好，它有像 tmux 一样的分裂
*   vscode 是由微软维护的，我认为有一个团队在这上面工作并添加相互集成的特性是很棒的
*   我不需要调试 vscode，而且到目前为止我没有发现任何 bug
*   我不需要安装太多插件
*   跳转到 React 的定义

我安装的 Vscode 插件:

*   [我来键绑定](https://marketplace.visualstudio.com/items?itemName=vscodevim.vim)
*   [项目经理](https://marketplace.visualstudio.com/items?itemName=alefragnani.project-manager) -使用`ALT + CMD + p`更容易在项目间切换
*   [书签](https://marketplace.visualstudio.com/items?itemName=alefragnani.Bookmarks) - `ALT + CMD + k`添加书签，`ALT + CMD + l`跳转到下一个书签
*   [文件工具](https://marketplace.visualstudio.com/items?itemName=sleistner.vscode-fileutils) -无需使用鼠标即可轻松复制或重命名文件
*   [对 Ruby 的支持](https://marketplace.visualstudio.com/items?itemName=rebornix.Ruby)
*   [支持 Ruby Haml](https://marketplace.visualstudio.com/items?itemName=vayan.haml)
*   [拼音的自动补全](https://marketplace.visualstudio.com/items?itemName=castwide.solargraph)
*   [林挺换红宝石](https://marketplace.visualstudio.com/items?itemName=misogi.ruby-rubocop)
*   [支持 Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
*   [支持仙丹](https://marketplace.visualstudio.com/items?itemName=mjmcloug.vscode-elixir)
*   [支持 React](https://marketplace.visualstudio.com/items?itemName=dsznajder.es7-react-js-snippets)
*   [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
*   一个黑暗的专业 Monokai 黑暗主题 -我最喜欢的颜色模式

[![using vscode to switch to files, bookmark files and jump to bookmarks, and use the integrated terminal](img/03b323c82ff3020d2865cc8546cbc48f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VlJntUVl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/thtlo6h65n8hmfksan23.gif)

这是我的文字编辑经验。今天，我不必调试我的编辑器来实现简单的事情，我仍然可以根据我的需要定制它。是的，我对 vscode 很满意，也许你也应该试试。我希望它对某人有用。

我也期待听到你对这些文本编辑器的看法和经验。谢了。