# 使用 Wordnik API

> 原文：<https://dev.to/denmch/using-the-wordnik-api-2chj>

我最终了解到的一件事是，无论您是在终端上运行 bash 还是 zsh，定制实例的文件都以`rc`结尾，它代表“runcom”或“run commands”，可以追溯到 UNIX 早期的 1965 年。`.bashrc`是包含 bourne again shell (bash)启动时要运行的命令的文件，`.zshrc`包含 z shell (zsh)启动时要运行的命令。你可以在其他地方找到它，例如在`.vimrc`中，如果你喜欢 Vim 的话。

这里可以做的一件很酷的事情是引用存储在外部文件中的环境变量、别名和函数。所以我使用 [Wordnik API](http://developer.wordnik.com/) 编写了下面的函数，这样我就可以在命令行上快速定义单词。