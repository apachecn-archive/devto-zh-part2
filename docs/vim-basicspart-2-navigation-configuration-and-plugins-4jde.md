# Vim 基础知识(第 2 部分):导航、配置和插件

> 原文：<https://dev.to/mvoto/vim-basicspart-2-navigation-configuration-and-plugins-4jde>

# 简介

这篇文章是 Vim 基础系列的第二篇，如果你还没有查看 [v1](https://dev.to/mvoto/vim-basicspart-1-modes-and-commands--6md) 和
，你可以看到一些开始使用这个神奇编辑器的提示。
谈完模式和命令后，事情会变得有趣，因为这里的主题将是导航和配置。通过一些导航提示，你可以感受到 Vim
背后的力量和潜力，此外，配置和插件可能会让你感到舒适，把它放在与你当前的文本编辑器相同的水平上，甚至可能更好——谈论生产率的提高，耶！

# 建议

与第 1 部分相同，在谈到导航和配置时，有一些好的建议。

*   导航的第一法则:不要用箭头键，习惯`hjkl`。
*   航海的第二条规则:练习，练习，再练习。你可以从开始
*   配置的主要规则:避免使用别人的整个配置文件。如果您决定使用现有的配置文件，请先阅读它，并且只使用您理解并且适合您需要的内容。
*   配置的建议:你会永远保持改进:)

## 导航

导航是 vim 最重要的技能之一，为了达到最佳性能，您必须掌握它。
使用键盘四处移动**仅限**。我们可以通过分成不同的主题来思考导航，我们想在这里讨论的是:屏幕/文件、行和单词。
让我们看看一些(在我看来)最有用的导航键:

## 屏幕/文件导航

当谈到屏幕时，我们针对的是您当前在 vim 编辑器中查看的内容，而当谈到文件时，我们针对的是全部内容。

*   `G` -转到文件的第一行
*   `gg` -转到文件的最后一行
*   `5G or 5gg` -转到文件的第 5 行
*   `H` -转到屏幕的第一行
*   `M` -转到屏幕的中间一行
*   `L` -转到屏幕的最后一行
*   `zt` -用光标将该行滚动到屏幕顶部
*   `zz` -用光标将该行滚动到屏幕中央
*   `zb` -用光标将该行滚动到屏幕底部

## 线导航

这是关于在行与行之间导航，以及导航到当前行的开始和结尾。

*   `h` -向左移动一个字符
*   `j` -向下移动一行
*   `k` -向上移动一行
*   `l` -向右移动一个字符
*   `3j / 3k` -分别向下移动 3 行和向上移动 3 行
*   `0` -移动到当前行的开头
*   `$` -移动到当前行的末尾

## 字导航

*   `e`–转到当前单词的末尾。
*   `E`–转到当前单词的末尾。
*   `b`–转到前一个单词。
*   `B`–转到前一个单词。
*   前往下一个单词。
*   前往下一个单词。

经过一段时间的练习和掌握模式后，我们可以很容易地对一些东西进行令人愉快的预览，例如多行列编辑

```
Ctrl + V
5j
Shift + I
@ + Esc 
```

Enter fullscreen mode Exit fullscreen mode

## 配置

每个人都有自己的故事，欣赏别人的故事也没什么。开始写你自己的故事怎么样，也许有一天其他人会欣赏你的故事。在你的配置文件中，这是一个很好的例子。
我开始使用我的朋友艾伦 T4[的一个，然后过了一段时间切换到](https://github.com/alanmesquita/meuvim/blob/master/.vimrc)[思维机器人的一个](https://github.com/thoughtbot/dotfiles/blob/master/vimrc)

## 插件

这个话题绝对值得有自己的帖子，但由于这是一个基础系列，我妥协了，以保持帖子简短，将尝试用几行来处理它:)
我个人对尝试插件的建议是自己做！第一步是检查一些现有的插件管理器，选择你最喜欢的一个(我用的是 Vundle)，所以这里有一个列表给你:

*   [新捆绑包](https://github.com/Shougo/neobundle.vim)
*   [病原体](https://github.com/tpope/vim-pathogen)
*   [VAM](https://github.com/MarcWeber/vim-addon-manager)
*   [Vim-plug](https://github.com/junegunn/vim-plug)
*   [Vundle](https://github.com/gmarik/Vundle.vim)

要在 vim 上定制和管理插件，您可以通过在您的主文件夹中编辑一个 dotfile 来完成。
即。:对于 Vundle，我有一个`~/.vimrc.bundles`文件，我的配置位于`~/.vimrc`。
评论有助于保持条理。
这里列出了一些流行的、有用的/必备的/强大的插件:

*   [树资源](https://github.com/scrooloose/nerdtree) -树资源管理器/侧栏
*   [CtrlP](https://github.com/ctrlpvim/ctrlp.vim) -全路径模糊查找器
*   [确认](https://github.com/mileszs/ack.vim) -在项目中搜索
*   逃亡者 -一个很棒的 Git 包装器
*   [包围](https://github.com/tpope/vim-surround) -简单的引用/括号
*   [数字](https://github.com/myusuf3/numbers.vim) -更好的行号，带来更好的导航
*   [Ctags](https://github.com/universal-ctags/ctags) -浏览代码(类和方法定义)
*   [NeoMake](https://github.com/neomake/neomake) -异步林挺(仅适用于 NeoVim)

我可以在这里建立一个巨大的列表，为不同的语言、框架和 linters 建议主题、语法亮点和助手，而不是将这个系列作为每个想要开始使用 Vim 的开发人员的入门指南。

# 结论

vim 最美好的地方，在我看来，就是这个编辑器是如何推动你前进，让你一边用一边思考。感觉总有最好或最简单的方法来完成你的实际任务。
所以，每次你在做导航的时候，试着花些时间想想更好的方法，就像在[第一部分的帖子](https://dev.to/mvoto/vim-basicspart-1-modes-and-commands--6md)中提到的，使用 vim 的时候，你应该花些时间来提高效率，但花些时间去谷歌一下你想做但不知道或想不起来的事情是值得的。
同样的规则也适用于配置和插件，只要有可能，就花些时间改进它，定制它，尽可能留下最好的方式。
结果可能会很惊人，你会发现自己对自己取得的进步非常满意，你的编辑会像你的儿子/女儿一样。继续关注更多，在未来的帖子中仍有很多内容要介绍，比如搜索。
对你我来说都是不错的参考:

*   [https://www . the geekstuff . com/2009/03/8-essential-vim-editor-navigation-fundamentals/](https://www.thegeekstuff.com/2009/03/8-essential-vim-editor-navigation-fundamentals/)
*   [http://vim.wikia.com/wiki/All_the_right_moves](http://vim.wikia.com/wiki/All_the_right_moves)
*   [https://github.com/akrawchyk/awesome-vim](https://github.com/akrawchyk/awesome-vim)