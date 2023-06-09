# 点文件-2018 年 1 月

> 原文：<https://dev.to/coreyja/dotfiles---january-2018-253l>

**原帖[coreyja.com](https://coreyja.com/blog/2018/01/06/dotfiles-december-2018.html)2018-01-06**

# 历史

我的网络文件回购是从某个家伙那里开始的。进行回购的主要原因是为了在我的工作和家用机器之间共享我的配置。有一段时间，我喜欢有一个体面的起点。给了我很多好的别名，其中大部分我还在用！我不太喜欢使用脚本将实际的回购文件复制到我的主目录中。这是一个额外的步骤，导致我通常在我的“真正的”主目录上测试东西，并且忘记将它们复制到我的 repo 中🤦
我无耻地从[尼克·洛佩兹](https://github.com/nlopez)那里窃取了制作我的真实家庭目录的想法！从标准的 git 白名单变成了更多的黑名单格式。首先创建一个 gitignore 来忽略所有东西，然后告诉它哪些文件是你不想忽略的。现在没有更多的脚本和废话！

然后我决定为了 VIM 抛弃 Rubymine！这次我想从头开始，而不是从别人的配置开始。所以我从原始配置中删除了 vim 文件，并从头开始。我真的很喜欢从轻量级的 vim 开始。这让我意识到 vim 可以做什么，以及我想要什么样的附加功能。我还得选择所有的键绑定，因为我是从零开始的！

接下来是[电力线](http://powerline.readthedocs.io/en/master/)，真的很有趣！但是设置和运行起来有点困难。但是很有潜力！写了一个不错的小 python 脚本来运行我的个人项目，它真的很容易破解，我想我在一个下午就让它工作了。安全团队中的一些人对我工作时的电力线设置不太满意😆。因为它是在一个单独的进程中运行的，特别是当我开始让它 ping 我的个人项目时。他们安装的安全软件定期发送关于我的设置的警报。因此，为了不让他们的警报变得嘈杂，我决定停止使用电力线。所以我不得不为[航空公司](https://github.com/vim-airline/vim-airline)掉线。这是一个有着相似目标的项目！最酷的是它是 100%用 vimscript 编写的，设置起来非常容易！用 [tmuxline.vim](https://github.com/edkolev/tmuxline.vim) 把它连接起来，这给了 tmux 和我的 vim 行一样的功能。它的功能并不丰富，但我认为我可以编写命令行应用程序，其功能类似于我之前使用的 python 脚本！我还没有真正开始做这件事，但是也许当我决定做这件事的时候，我会写博客。我考虑在家里使用电力线，在工作中使用航线，但是我决定我宁愿保持一致性，所以在两个 env 中都切换到使用航线。

# 通用工具

*   巴什，我不是同性恋。简单的狂欢对我很有效。很久以前我用鱼做了实验，但它从来没有卡住过。现在我知道了足够多的 Bash 和一切，我不认为交换会有多大意义。
*   [tmux](https://github.com/tmux/tmux) 。我以前用 iTerm 做标签和分割等等，但是最近换成了 tmux。当时我转换的原因实际上是为了使用 Tmuxinator。我真的喜欢有不同的项目，我可以很容易地设置和复制。它还让我可以轻松地切换到一个新的应用程序/项目，而不会丢失我目前所在的位置。例如，我目前打开了我的`dotfiles`和`coreyja-blog`会话，我可以在两者之间来回切换。这些会议中的每一个都有自己的窗口和分割等等。这有助于加快上下文切换，因为我可以准确地从我停止的地方重新开始！
*   vim/ [neovim](https://github.com/neovim/neovim) 。这是我的工具箱中最近增加的东西之一！在我使用 RubyMine 之前，这是一个非常棒的 IDE(在我看来，Jetbrains 的所有东西都非常好)，但我想要一些更加可定制和轻量级的东西，所以 VIM 是我最终选择的地方。我和一个同事几乎同时学会了这个，这帮助我们互相学习新的技巧。我实际上使用的是 nvim，它是 vim 的一个新分支，但本质上功能是相同的。
    *   [https://github.com/coreyja/dotfiles/blob/master/.vimrc](https://github.com/coreyja/dotfiles/blob/master/.vimrc)
*   [航空公司](https://github.com/vim-airline/vim-airline)。这是最近在我的设置中取代了电力线。它的重量要轻得多，也更容易设置，但是我在转换过程中损失了一些功能。
*   [fzf](https://github.com/junegunn/fzf)
    *   fzf 是一个我绝对喜欢的模糊查找器！我在 Bash 和 vim 的命令行中都使用它。它提供了超级快速，惊人的准确模糊查找。这是我最喜欢的工具之一，我离不开它。我喜欢它能很好地集成到我使用的其他工具中。

# 红宝石

*   rbenv——不同的项目需要不同版本的 Ruby，而 rbenv 完美地完成了这项工作！
    *   我也用一些 rbenv 插件来帮忙:
        *   rbenv/ruby-build
            *   下载并构建新版本的 Ruby
        *   rbenv/rbenv-默认-gems
            *   安装新版 ruby 时安装一套 gem
            *   [https://github.com/coreyja/dotfiles/blob/master/.rbenv/default-gems](https://github.com/coreyja/dotfiles/blob/master/.rbenv/default-gems)
        *   ianheggie/rbenv-binstubs
            *   再也不用跑`bundle exec`了！
        *   t ope/rbenv-ctags
            *   为已安装的 Ruby 版本自动生成 ctags 文件。稍后会有更多关于 ctags 的内容！
        *   rkh/rbenv-更新
            *   运行`rbenv update`时自动更新 rbenv 插件

# Ctags

Ctags 是我能够离开 RubyMine 去 vim 的原因！我喜欢 RubyMine 中的“跳转到实现”特性，ctags 基本上为我在 VIM 中复制了这个特性！我承认它们不如我记忆中的“跳转到实现”功能那么好，但是对我来说已经足够好了！所以基本上 ctags 是通过创建`tags`文件来工作的，这些文件告诉它所有不同的类和方法是在哪里定义的。一旦为您的项目(和您的依赖项)创建了这些，vim 就可以搜索它们，快速跳转到函数的定义。但是生成所有的 ctags 可能有点困难！我基本上是跟着[这位了不起的导游](http://tbaggery.com/2011/08/08/effortless-ctags-with-git.html)[蒂姆·波普(tpope)](https://github.com/tpope) 的走的，所以我不会说太多！

有一件事我已经开始做的不同于那本指南，那就是开始为我的 Ruby 项目使用 [ripper-tags](https://github.com/tmm1/ripper-tags) 而不是 ctags，这是我目前的大部分项目。它为 Ruby 文件提供了更好的索引，因为它专门针对 Ruby。我修改了 tpope 的`ctags` bash 脚本，对 Ruby 文件使用 ripper 标签。[这里的](https://github.com/coreyja/dotfiles/blob/master/.git_template/hooks/ctags)是我修改过的那个剧本的版本。

我还使用 [gem-ripper-tags](https://github.com/lzap/gem-ripper-tags) 来自动生成 ctags，使用 [ripper-tags](https://github.com/tmm1/ripper-tags) 为我所有安装的宝石，这是在我的默认宝石列表中，以便它自动为每个版本的 Ruby 安装。

为了真正利用 VIM 中的 ctags，我将它们与 fzf 进行了比较，因为这种模糊的发现很好！这是我的重映射，这样我就可以按 Control-P 键，从所有匹配光标的标签中得到一个 fzf 窗口，然后跳转到我选择的标签。这是与 Rubymines 最相似的命令点击行为。

# 包装完毕

显然，我的 dotfiles 中的内容比我在这里概述的要多得多！可能还有更多我想写的东西。一个特别的例子是我用 GPG 和我的 Yubikey 设置 Github 提交签名和 SSH 验证。但是就像我说的，我们将把它留到另一篇文章中。

谢谢你一直坚持到最后！这是我 2018 年的第一篇帖子，但我会努力让它成为一种习惯。今年我会试着每周贴一张！我们拭目以待吧！

编辑(2018-01-07):今天我发现了[宝石-撕裂者-标记](https://github.com/lzap/gem-ripper-tags)，我现在用的不是[宝石-ctags](https://github.com/tpope/gem-ctags) 。