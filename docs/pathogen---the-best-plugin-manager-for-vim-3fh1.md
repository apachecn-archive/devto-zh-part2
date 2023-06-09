# 病原体 vim 的最佳插件管理器

> 原文：<https://dev.to/pmihaylov/pathogen---the-best-plugin-manager-for-vim-3fh1>

继续用下一个简洁的 VIM 插件- [病原体](https://github.com/tpope/vim-pathogen)提升你的 VIM 系列！

上一次，我向您展示了我最喜欢的 bash 实用程序之一(碰巧它与 vim 配合得非常好)- [tmux](https://dev.to/pmihaylov/tmux---a-terminal-multiplexer-2pm6) 。

这一次，我将向您介绍我在插件管理器方面的首选武器。

叫做[病原体](https://github.com/tpope/vim-pathogen)。这是最好也是最简单的插件管理器。

还有其他的，像 [Vundle](https://github.com/VundleVim/Vundle.vim) ，我过去也尝试过，但是没有一个能打败病原体的简单性。

它是如此简单(事实上，总的源代码是 250 行)，这将是一个非常简单的职位。

所以，如果你准备用一些简洁的插件来破解你的 VIM，从这个开始，让你的生活更简单。

# 介绍

病原体是 vim 的一个插件管理器。这让你可以很容易地安装插件，没有太多的麻烦。

通常，你必须将插件的不同文件分发到不同的地方。

病原体为你做了这一切。所有你需要做的，就是`git clone`在一个指定的文件夹中的 vim 插件 repo(通常是`~/.vim/bundle`,就是这样。

# 装置

直接取自原回购:

```
mkdir -p ~/.vim/autoload ~/.vim/bundle &amp;&amp; \
curl -LSso ~/.vim/autoload/pathogen.vim https://tpo.pe/pathogen.vim 
```

Enter fullscreen mode Exit fullscreen mode

接下来，将这个添加到您的`~/.vimrc`:

`execute pathogen#infect()`

就是这样！

# 如何使用

任何时候你想尝试一个新的 vim 插件，运行这个:

`git clone <vim plugin repo> ~/.vim/bundle`

例如，如果您想要安装 [Nerdtree](https://github.com/scrooloose/nerdtree) ，请运行以下命令:

`git clone [https://github.com/scrooloose/nerdtree](https://github.com/scrooloose/nerdtree) ~/.vim/bundle`

最后，在您的 vim 中，为了用新的插件文档填充 vim 文档，运行`:Helptags`

之后，剩下的就是根据它的帮助页面和 repo README 来配置特定的插件。如果需要的话。

# 结论

所以你有它。你最好的 vim 插件管理器。我就是喜欢它的简单。

一旦你有了这个孩子，就没有必要再为其他的事情烦恼了。

更多很酷的 vim 插件展示，请观看这个空间并订阅。