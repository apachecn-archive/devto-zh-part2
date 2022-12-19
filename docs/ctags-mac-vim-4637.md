# Ctags mac Vim

> 原文：<https://dev.to/adityavarma1234/ctags-mac-vim-4637>

使用 home brew 在 vim 中安装 ctag
brew 安装 ctag
我已经在我的 vimrc 文件中为 ctag
设置了这个映射

`map <C-F12> :!ctags -R --exclude=.git --exclude=logs --exclude=doc . <CR>`

我只需按下

`ctrl + fn + f12`

，瞧，我可以使用

`ctrl + ]`

来定义 rails 项目中的函数。

上面没有找到为 gem 特定功能做标记的方法。
我不得不加上

`bundle list --paths`

到上面的线也一样。
现在我有了一个新的映射，大概是这样的

`map <C-F12> :!ctags -R --exclude=.git --exclude=logs --exclude=doc . $(bundle list --paths)<CR>`

现在，我转到一个 gem 特定函数，键入

`ctrl + ]`

，瞧，我已经迁移到 gem 文件中，现在我可以查看 gem 源代码了。

有用链接:[https://blog . sensible . io/2014/05/09/super charge-your-vim-into-ide-with-ctags . html](https://blog.sensible.io/2014/05/09/supercharge-your-vim-into-ide-with-ctags.html)
T3】https://andrew.stwrt.ca/posts/vim-ctags/