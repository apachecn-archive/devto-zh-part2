# 从 Vim 切换到 Neovim

> 原文：<https://dev.to/matthewbdaly/switching-from-vim-to-neovim-3jil>

我真的以为这永远不会发生。我从 2008 年开始使用 Vim，我尝试过的所有其他编辑器(包括 VSCode、Emacs、Sublime Text 和 Atom)都没有达到标准。公平地说，PHPStorm 中有一些有用的特性，但是没有一个能够证明迁移的麻烦。此外，我还遭受了一定程度的重复性劳损，这来自于我之前的保险职员职业生涯(多年来在 Windows XP 上使用垃圾键盘和鼠标造成的伤害)...)，而且 Vim 一直是我发现的对 RSI 最友好的编辑器。

然而我实际上已经离开了...敬尼奥维姆。当然，工作流本质上相同的事实有助于迁移过程，因为它支持大多数相同的插件。

我的工作流程一直都是基于 CLI 的。我使用 GNU Screen 和 Byobu 一起运行终端中的多个“标签页”，因此 Neovim 中 GUI 支持的缺乏丝毫不会困扰我。我真正做的唯一改变是对我的`.bash_aliases`进行修改，以便 vim 命令运行`screen -t Vim nvim`，这样它将在一个新的屏幕标签中打开 Neovim 而不是 Vim。

最初，我直接切换到使用与 Vim 相同的设置和插件，它们可以无缝地工作。然而，过了一段时间后，我决定利用这个机会彻底检查我使用的插件和设置，并在很大程度上开始筛选我不再需要的插件，添加一些新的插件，并对其进行适当的评论。

## 加载插件

我曾经使用病原体来管理我的 Vim 插件，但它实际上并没有导入插件本身，只是为它们提供了一个结构。这意味着我找到的引入第三方插件的唯一可行方法是将它们设置为 Git 子模块，这意味着我必须将我的配置存储在版本控制中，并将其递归克隆到一台新机器上。这也使得更新变得很麻烦。

现在我已经换成了 [vim-plug](https://github.com/junegunn/vim-plug) ，这样事情就简单多了。我可以在我的`.config/nvim/init.vim`中定义我的依赖项，并用`PlugInstall`将它们拉进来。如果我想更新它们，我运行`PlugUpdate`，或者如果我需要添加其他东西，我只需将它添加到文件中，然后再次运行`PlugInstall`。很好很容易。

我的配置文件的第一部分加载依赖项:

```
call plug#begin()

" NERDTree
Plug 'scrooloose/nerdtree'

" Git integration
Plug 'tpope/vim-fugitive'
Plug 'airblade/vim-gitgutter'

" Linting
Plug 'neomake/neomake'
Plug 'w0rp/ale'

" PHP-specific integration
Plug 'phpactor/phpactor' ,  {'do': 'composer install', 'for': 'php'}
Plug 'ncm2/ncm2'
Plug 'roxma/nvim-yarp'
Plug 'phpactor/ncm2-phpactor'

" Snippets
Plug 'SirVer/ultisnips'
Plug 'honza/vim-snippets'

" Comments
Plug 'tpope/vim-commentary'

" Search
Plug 'ctrlpvim/ctrlp.vim'

" Syntax
Plug 'sheerun/vim-polyglot'
Plug 'matthewbdaly/vim-filetype-settings'

" Themes
Plug 'nanotech/jellybeans.vim' , {'as': 'jellybeans'}

call plug#end() 
```

Enter fullscreen mode Exit fullscreen mode

和往常一样，给你的配置加注释并试着按逻辑分组是个好主意。请注意，这里列出了我自己的一个插件——这只是不同文件类型的设置集合，比如让 Javascript 文件使用 2 个空格进行缩进，将它保存在存储库中并作为一个依赖项放入更容易。

## 完成

配置的下一部分处理配置。大多数情况下，默认的 omnicompletion 相当不错，但是在构建这个配置的过程中，我发现了 PHPActor，它极大地改善了我的 PHP 开发体验——它最终提供了与大多数 IDE 一样好的完成功能，并且还提供了类似的重构工具。我的完成配置目前看起来是这样的:

```
"Completion
autocmd FileType * setlocal formatoptions-=c formatoptions-=r formatoptions-=o
set ofu=syntaxcomplete#Complete
autocmd FileType php setlocal omnifunc=phpactor#Complete
let g:phpactorOmniError = v:true
autocmd BufEnter * call ncm2#enable_for_buffer()
set completeopt=noinsert,menuone,noselect 
```

Enter fullscreen mode Exit fullscreen mode

## 通用配置

这是一组应用程序一般行为的标准设置，例如设置配色方案和默认缩进级别。我也经常禁用鼠标，因为它让我心烦。

```
"General
syntax on
colorscheme jellybeans
set nu
filetype plugin indent on
set nocp
set ruler
set wildmenu
set mouse-=a
set t_Co=256

"Code folding
set foldmethod=manual

"Tabs and spacing
set autoindent
set cindent
set tabstop=4
set expandtab
set shiftwidth=4
set smarttab

"Search
set hlsearch
set incsearch
set ignorecase
set smartcase
set diffopt +=iwhite 
```

Enter fullscreen mode Exit fullscreen mode

## 降价配置

本节设置降价的文件类型。它禁用了包含在`vim-polyglot`中的 Markdown 插件，因为我在使用它时遇到了问题，并设置了将在防护代码块中突出显示的语言。我可能会在某个时候将它迁移到文件类型存储库中。

```
"Syntax highlighting in Markdown
au BufNewFile,BufReadPost *.md set filetype=markdown
let g:polyglot_disabled = ['markdown']
let g:markdown_fenced_languages = ['bash=sh', 'css', 'django', 'javascript', 'js=javascript', 'json=javascript', 'perl', 'php', 'python', 'ruby', 'sass', 'xml', 'html', 'vim'] 
```

Enter fullscreen mode Exit fullscreen mode

## 新造

我曾经使用 Syntastic 来检查我的代码中的错误，但我总是发现它有问题——它很慢，并且经常会阻塞编辑器一段时间。Neovim 确实支持异步作业(Vim 8 也是)，但是 Syntastic 不使用它，所以我决定去别处看看。

Neomake 似乎好得多，所以我迁移到它。它不需要太多的配置，而且非常快——不像 Syntastic，它支持异步作业。配置的这一部分将它设置为在没有延迟的情况下运行更改，因此如果出现语法错误，我几乎可以立即得到反馈，并且它不会像 Syntastic 以前那样阻塞编辑器。

```
" Neomake config
" Full config: when writing or reading a buffer, and on changes in insert and
" normal mode (after 1s; no delay when writing).
call neomake#configure#automake('nrwi', 500) 
```

Enter fullscreen mode Exit fullscreen mode

## PHPActor

如上所述，PHPActor 通过提供通常只能在完整 IDE 中找到的特性，极大地改善了我用 PHP 编码的体验。下面是我用于重构功能的相当标准的配置:

```
" PHPActor config
" Include use statement
nmap <Leader>u :call phpactor#UseAdd()<CR>

" Invoke the context menu
nmap <Leader>mm :call phpactor#ContextMenu()<CR>

" Invoke the navigation menu
nmap <Leader>nn :call phpactor#Navigate()<CR>

" Goto definition of class or class member under the cursor
nmap <Leader>o :call phpactor#GotoDefinition()<CR>

" Transform the classes in the current file
nmap <Leader>tt :call phpactor#Transform()<CR>

" Generate a new class (replacing the current file)
nmap <Leader>cc :call phpactor#ClassNew()<CR>

" Extract expression (normal mode)
nmap <silent><Leader>ee :call phpactor#ExtractExpression(v:false)<CR>

" Extract expression from selection
vmap <silent><Leader>ee :<C-U>call phpactor#ExtractExpression(v:true)<CR>

" Extract method from selection
vmap <silent><Leader>em :<C-U>call phpactor#ExtractMethod()<CR> 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

Vim 或 Neovim 配置文件从来都不是静态的。您的需求总是在变化，您会不断发现新的插件和新的设置来尝试，并保留那些证明有用的。重新开始并抛弃一些我不再需要的插件，引入一些新的插件，并更好地组织我的配置，这很有帮助。

既然我可以在一个文本文件中设置依赖关系，而不是将它们作为 Git 子模块拉进来，那么将我的配置保存在一个 [Github Gist](https://gist.github.com/matthewbdaly/80b777ad3db885ebeecd27687fb121cd) 中比保存在 Git 存储库中更有意义，这也是我现在打算保留它的地方。如果你愿意的话，你可以随意把它分成两半。