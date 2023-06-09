# Vim 是完美的 IDE

> 原文：<https://dev.to/allanmacgregor/vim-is-the-perfect-ide-e80>

*本文原载于[hacker noon](https://hackernoon.com/vim-is-the-perfect-ide-c226d7ed6d88)T3*

这些年来，我在许多代码编辑器、**ide**和**工具**之间来回切换；但似乎不知何故，我总是最终回到 VIM，不仅仅是为了编程——猜猜我用哪个 markdown 编辑器写这篇文章。

我已经尝试了 Atom、SublimeText、TextMate、Eclipse、Visual Studio 和大多数 Jetbrains 产品，我不断地**调整和寻找更好的设置**，然而 Vim 对我来说总是感觉像家一样；我现在已经很少使用 ide 了——除了那些杂乱复杂的项目，ide 可以做很多繁重的工作(是的，Magento，我说的就是你。)

但除此之外，Vim 是我默认的 Ruby、Elixir、Python、PHP IDE，也是我用来写草稿和书籍的主要工具。

## 它长什么样子

[![](img/180d4e9502d4ee8104ecc54cca22649a.png "source: imgur.com")T2】](http://imgur.com/AQSIXwj)

## 设置

那么这个神奇的工具是如何工作的呢？都是开箱即用的对吗？对吗？嗯，不，就像生活中所有有价值的事情一样，需要付出一点努力来实现我想要的 Vim 设置。幸运的是，它远不是定制的，而是插件的正确组合。

你可以在相应的 [Github 库](https://github.com/amacgregor/dot-files)中找到我当前的 Vim 配置和 dot 文件，你可以随意使用它。

我们对 vimrc 文件特别感兴趣，让我们把它分解一下:

```
 """""""""""""""""""""""""""""""""""""
" Allan MacGregor Vimrc configuration 
"""""""""""""""""""""""""""""""""""""
set nocompatible
syntax on
set nowrap
set encoding=utf8

"""" START Vundle Configuration 

" Disable file type for vundle
filetype off                  " required

" set the runtime path to include Vundle and initialize
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()

" let Vundle manage Vundle, required
Plugin 'gmarik/Vundle.vim'

" Utility
Plugin 'scrooloose/nerdtree'
Plugin 'majutsushi/tagbar'
Plugin 'ervandew/supertab'
Plugin 'BufOnly.vim'
Plugin 'wesQ3/vim-windowswap'
Plugin 'SirVer/ultisnips'
Plugin 'junegunn/fzf.vim'
Plugin 'junegunn/fzf'
Plugin 'godlygeek/tabular'
Plugin 'ctrlpvim/ctrlp.vim'
Plugin 'benmills/vimux'
Plugin 'jeetsukumaran/vim-buffergator'
Plugin 'gilsondev/searchtasks.vim'
Plugin 'Shougo/neocomplete.vim'
Plugin 'tpope/vim-dispatch'

" Generic Programming Support 
Plugin 'jakedouglas/exuberant-ctags'
Plugin 'honza/vim-snippets'
Plugin 'Townk/vim-autoclose'
Plugin 'tomtom/tcomment_vim'
Plugin 'tobyS/vmustache'
Plugin 'janko-m/vim-test'
Plugin 'maksimr/vim-jsbeautify'
Plugin 'vim-syntastic/syntastic'
Plugin 'neomake/neomake'

" Markdown / Writting
Plugin 'reedes/vim-pencil'
Plugin 'tpope/vim-markdown'
Plugin 'jtratner/vim-flavored-markdown'
Plugin 'LanguageTool'

" Git Support
Plugin 'kablamo/vim-git-log'
Plugin 'gregsexton/gitv'
Plugin 'tpope/vim-fugitive'
"Plugin 'jaxbot/github-issues.vim'

" PHP Support
Plugin 'phpvim/phpcd.vim'
Plugin 'tobyS/pdv'

" Erlang Support
Plugin 'vim-erlang/vim-erlang-tags'
Plugin 'vim-erlang/vim-erlang-runtime'
Plugin 'vim-erlang/vim-erlang-omnicomplete'
Plugin 'vim-erlang/vim-erlang-compiler'

" Elixir Support 
Plugin 'elixir-lang/vim-elixir'
Plugin 'avdgaag/vim-phoenix'
Plugin 'mmorearty/elixir-ctags'
Plugin 'mattreduce/vim-mix'
Plugin 'BjRo/vim-extest'
Plugin 'frost/vim-eh-docs'
Plugin 'slashmili/alchemist.vim'
Plugin 'tpope/vim-endwise'
Plugin 'jadercorrea/elixir_generator.vim'

" Elm Support
Plugin 'lambdatoast/elm.vim'

" Theme / Interface
Plugin 'AnsiEsc.vim'
Plugin 'ryanoasis/vim-devicons'
Plugin 'vim-airline/vim-airline'
Plugin 'vim-airline/vim-airline-themes'
Plugin 'sjl/badwolf'
Plugin 'tomasr/molokai'
Plugin 'morhetz/gruvbox'
Plugin 'zenorocha/dracula-theme', {'rtp': 'vim/'}
Plugin 'junegunn/limelight.vim'
Plugin 'mkarmona/colorsbox'
Plugin 'romainl/Apprentice'
Plugin 'Lokaltog/vim-distinguished'
Plugin 'chriskempson/base16-vim'
Plugin 'w0ng/vim-hybrid'
Plugin 'AlessandroYorba/Sierra'
Plugin 'daylerees/colour-schemes'
Plugin 'effkay/argonaut.vim'
Plugin 'ajh17/Spacegray.vim'
Plugin 'atelierbram/Base2Tone-vim'
Plugin 'colepeters/spacemacs-theme.vim'

" OSX stupid backspace fix
set backspace=indent,eol,start

call vundle#end()            " required
filetype plugin indent on    " required
"""" END Vundle Configuration 

"""""""""""""""""""""""""""""""""""""
" Configuration Section
"""""""""""""""""""""""""""""""""""""

" Show linenumbers
set number
set ruler

" Set Proper Tabs
set tabstop=4
set shiftwidth=4
set smarttab
set expandtab

" Always display the status line
set laststatus=2

" Enable Elite mode, No ARRRROWWS!!!!
let g:elite_mode=1

" Enable highlighting of the current line
set cursorline

" Theme and Styling 
set t_Co=256
set background=dark

if (has("termguicolors"))
  set termguicolors
endif

let base16colorspace=256  " Access colors present in 256 colorspace
colorscheme spacegray
" colorscheme spacemacs-theme

let g:spacegray_underline_search = 1
let g:spacegray_italicize_comments = 1

" Vim-Airline Configuration
let g:airline#extensions#tabline#enabled = 1
let g:airline_powerline_fonts = 1 
let g:airline_theme='hybrid'
let g:hybrid_custom_term_colors = 1
let g:hybrid_reduced_contrast = 1 

" Syntastic Configuration
set statusline+=%#warningmsg#
set statusline+=%{SyntasticStatuslineFlag()}
set statusline+=%*

let g:syntastic_always_populate_loc_list = 1
let g:syntastic_auto_loc_list = 1
let g:syntastic_check_on_open = 1
" let g:syntastic_check_on_wq = 0
" let g:syntastic_enable_elixir_checker = 1
" let g:syntastic_elixir_checkers = ["elixir"]

" Neomake settings
autocmd! BufWritePost * Neomake
let g:neomake_elixir_enabled_makers = ['mix', 'credo', 'dogma']

" Vim-PDV Configuration 
let g:pdv_template_dir = $HOME ."/.vim/bundle/pdv/templates_snip"

" Markdown Syntax Support
augroup markdown
    au!
    au BufNewFile,BufRead *.md,*.markdown setlocal filetype=ghmarkdown
augroup END

" Github Issues Configuration
let g:github_access_token = "e6fb845bd306a3ca7f086cef82732d1d5d9ac8e0"

" Vim-Alchemist Configuration
let g:alchemist#elixir_erlang_src = "/Users/amacgregor/Projects/Github/alchemist-source"
let g:alchemist_tag_disable = 1

" Vim-Supertab Configuration
let g:SuperTabDefaultCompletionType = "<C-X><C-O>"

" Settings for Writting
let g:pencil#wrapModeDefault = 'soft'   " default is 'hard'
let g:languagetool_jar  = '/opt/languagetool/languagetool-commandline.jar'

" Vim-pencil Configuration
augroup pencil
  autocmd!
  autocmd FileType markdown,mkd call pencil#init()
  autocmd FileType text         call pencil#init()
augroup END

" Vim-UtilSnips Configuration
" Trigger configuration. Do not use <tab> if you use https://github.com/Valloric/YouCompleteMe.
let g:UltiSnipsExpandTrigger="<tab>"
let g:UltiSnipsJumpForwardTrigger="<c-b>"
let g:UltiSnipsJumpBackwardTrigger="<c-z>"
let g:UltiSnipsEditSplit="vertical" " If you want :UltiSnipsEdit to split your window.

" Vim-Test Configuration
let test#strategy = "vimux"

" Neocomplete Settings
let g:acp_enableAtStartup = 0
let g:neocomplete#enable_at_startup = 1
let g:neocomplete#enable_smart_case = 1
let g:neocomplete#sources#syntax#min_keyword_length = 3

" Define dictionary.
let g:neocomplete#sources#dictionary#dictionaries = {
    \ 'default' : '',
    \ 'vimshell' : $HOME.'/.vimshell_hist',
    \ 'scheme' : $HOME.'/.gosh_completions'
        \ }

" Define keyword.
if !exists('g:neocomplete#keyword_patterns')
    let g:neocomplete#keyword_patterns = {}
endif
let g:neocomplete#keyword_patterns['default'] = '\h\w*'

function! s:my_cr_function()
  return (pumvisible() ? "\<C-y>" : "" ) . "\<CR>"
  " For no inserting <CR> key.
  "return pumvisible() ? "\<C-y>" : "\<CR>"
endfunction

" Close popup by <Space>.
"inoremap <expr><Space> pumvisible() ? "\<C-y>" : "\<Space>"

" AutoComplPop like behavior.
"let g:neocomplete#enable_auto_select = 1

" Enable omni completion.
autocmd FileType css setlocal omnifunc=csscomplete#CompleteCSS
autocmd FileType html,markdown setlocal omnifunc=htmlcomplete#CompleteTags
autocmd FileType javascript setlocal omnifunc=javascriptcomplete#CompleteJS
autocmd FileType python setlocal omnifunc=pythoncomplete#Complete
autocmd FileType xml setlocal omnifunc=xmlcomplete#CompleteTags

" Enable heavy omni completion.
if !exists('g:neocomplete#sources#omni#input_patterns')
  let g:neocomplete#sources#omni#input_patterns = {}
endif
"let g:neocomplete#sources#omni#input_patterns.php = '[^. \t]->\h\w*\|\h\w*::'
"let g:neocomplete#sources#omni#input_patterns.c = '[^.[:digit:] *\t]\%(\.\|->\)'
"let g:neocomplete#sources#omni#input_patterns.cpp = '[^.[:digit:] *\t]\%(\.\|->\)\|\h\w*::'

" For perlomni.vim setting.
" https://github.com/c9s/perlomni.vim
let g:neocomplete#sources#omni#input_patterns.perl = '\h\w*->\h\w*\|\h\w*::'

" Elixir Tagbar Configuration
let g:tagbar_type_elixir = {
    \ 'ctagstype' : 'elixir',
    \ 'kinds' : [
        \ 'f:functions',
        \ 'functions:functions',
        \ 'c:callbacks',
        \ 'd:delegates',
        \ 'e:exceptions',
        \ 'i:implementations',
        \ 'a:macros',
        \ 'o:operators',
        \ 'm:modules',
        \ 'p:protocols',
        \ 'r:records',
        \ 't:tests'
    \ ]
    \ }

" Fzf Configuration
" This is the default extra key bindings
let g:fzf_action = {
  \ 'ctrl-t': 'tab split',
  \ 'ctrl-x': 'split',
  \ 'ctrl-v': 'vsplit' }

" Default fzf layout
" - down / up / left / right
let g:fzf_layout = { 'down': '~40%' }

" In Neovim, you can set up fzf window using a Vim command
let g:fzf_layout = { 'window': 'enew' }
let g:fzf_layout = { 'window': '-tabnew' }

" Customize fzf colors to match your color scheme
let g:fzf_colors =
\ { 'fg':      ['fg', 'Normal'],
  \ 'bg':      ['bg', 'Normal'],
  \ 'hl':      ['fg', 'Comment'],
  \ 'fg+':     ['fg', 'CursorLine', 'CursorColumn', 'Normal'],
  \ 'bg+':     ['bg', 'CursorLine', 'CursorColumn'],
  \ 'hl+':     ['fg', 'Statement'],
  \ 'info':    ['fg', 'PreProc'],
  \ 'prompt':  ['fg', 'Conditional'],
  \ 'pointer': ['fg', 'Exception'],
  \ 'marker':  ['fg', 'Keyword'],
  \ 'spinner': ['fg', 'Label'],
  \ 'header':  ['fg', 'Comment'] }

" Enable per-command history.
" CTRL-N and CTRL-P will be automatically bound to next-history and
" previous-history instead of down and up. If you don't like the change,
" explicitly bind the keys to down and up in your $FZF_DEFAULT_OPTS.
let g:fzf_history_dir = '~/.local/share/fzf-history'

"""""""""""""""""""""""""""""""""""""
" Mappings configurationn
"""""""""""""""""""""""""""""""""""""
map <C-n> :NERDTreeToggle<CR>
map <C-m> :TagbarToggle<CR>

" Omnicomplete Better Nav
inoremap <expr> <c-j> ("\<C-n>")
inoremap <expr> <c-k> ("\<C-p>")

" Neocomplete Plugin mappins
inoremap <expr><C-g>     neocomplete#undo_completion()
inoremap <expr><C-l>     neocomplete#complete_common_string()

" Recommended key-mappings.
" <CR>: close popup and save indent.
inoremap <silent> <CR> <C-r>=<SID>my_cr_function()<CR>

" <TAB>: completion.
inoremap <expr><TAB>  pumvisible() ? "\<C-n>" : "\<TAB>"

" <C-h>, <BS>: close popup and delete backword char.
inoremap <expr><C-h> neocomplete#smart_close_popup()."\<C-h>"
inoremap <expr><BS> neocomplete#smart_close_popup()."\<C-h>"

" Mapping selecting Mappings
nmap <leader><tab> <plug>(fzf-maps-n)
xmap <leader><tab> <plug>(fzf-maps-x)
omap <leader><tab> <plug>(fzf-maps-o)

" Shortcuts
nnoremap <Leader>o :Files<CR> 
nnoremap <Leader>O :CtrlP<CR>
nnoremap <Leader>w :w<CR>

" Insert mode completion
imap <c-x><c-k> <plug>(fzf-complete-word)
imap <c-x><c-f> <plug>(fzf-complete-path)
imap <c-x><c-j> <plug>(fzf-complete-file-ag)
imap <c-x><c-l> <plug>(fzf-complete-line)

" Vim-Test Mappings
nmap <silent> <leader>t :TestNearest<CR>
nmap <silent> <leader>T :TestFile<CR>
nmap <silent> <leader>a :TestSuite<CR>
nmap <silent> <leader>l :TestLast<CR>
nmap <silent> <leader>g :TestVisit<CR>

" Vim-PDV Mappings
autocmd FileType php inoremap <C-p> <ESC>:call pdv#DocumentWithSnip()<CR>i
autocmd FileType php nnoremap <C-p> :call pdv#DocumentWithSnip()<CR>
autocmd FileType php setlocal omnifunc=phpcd#CompletePHP

" Disable arrow movement, resize splits instead.
if get(g:, 'elite_mode')
    nnoremap <Up>    :resize +2<CR>
    nnoremap <Down>  :resize -2<CR>
    nnoremap <Left>  :vertical resize +2<CR>
    nnoremap <Right> :vertical resize -2<CR>
endif

map <silent> <LocalLeader>ws :highlight clear ExtraWhitespace<CR>

" Advanced customization using autoload functions
inoremap <expr> <c-x><c-k> fzf#vim#complete#word({'left': '15%'})

" Vim-Alchemist Mappings
autocmd FileType elixir nnoremap <buffer> <leader>h :call alchemist#exdoc()<CR>
autocmd FileType elixir nnoremap <buffer> <leader>d :call alchemist#exdef()<CR> 
```

Enter fullscreen mode Exit fullscreen mode

此设置中的每个插件分为以下几类:

### 效用

这是一个杂七杂八的类别，由用于增强或改变核心 vim 行为的插件组成；最有用最重要的是:

*   **Nerdtree** :它以目录树的形式显示在屏幕左侧，方便您访问文件系统，并提供文件系统操作的快捷方式(创建、删除、移动文件和目录)
*   标签栏(Tagbar):当前文件的快速标签浏览器，如果你使用任何种类的**ctag**,比如 exverant-tags，这是必须的。
*   FZF:模糊查找器，另一个查找文件和命令的便利工具。
*   新完成:类固醇上的 Vim 自动完成。

### 通用编程支持

这些插件直接属于编程范畴，在我目前安装的所有或大部分**编程语言**中使用:

*   **Exuberant-Ctags** :标签是类、函数、抽象类型等等的命名定义；添加对 Vim 的支持会给您带来一些“神奇的”IDE 代码导航功能。
*   Syntastic:Vim 的语法检查插件，如果你熟悉 Jetbrains 和类似 ide 上代码检查的工作方式，synt astic 会让你感觉很舒服。
*   **Vim-autoclose** :自动关闭一个字符，这个字符可能/应该有一个匹配的结束对应字符，比如()" "[] {}等等。

### 降价/写作

正如我提到的，Vim 是我起草新帖子的编辑，无论是书籍、博客还是随机的愤怒信件。从这一部分来看，只有语言工具值得一提，因为它直接从 Vim 内部提供了一个很好的语法检查器。

### Erlang/Elixir/PHP/Elm 支持

当涉及到**个人语言支持**时，除了我已经尝试(有点失败)将插件保持在最少并只关注**基本语言支持**之外，没有什么值得强调的。

到目前为止，就插件而言，elixir 正在赢得这场战斗，因为我已经添加了额外的功能，如在 Vim 中运行测试和生成内容的能力，我还没有决定是否为它保留所有的插件。

### Git 支持

恐怕我很少使用的标准 git 支持，我发现自己直接回到了 **shell** 并在 Vim 之外做 **git 工作流**，所以我乐于接受建议并听取其他人在设置方面的使用。

### 主题和界面

好吧，这是一个大问题，但主要是因为我总是忘记删除不用的主题和配色方案，让我们突出重要的:

*   **vimar line**:Vim 的精益和平均状态/tabline；它看起来也很酷。
*   Vim-Devicons :因为不仅仅是 atom 获得了侧边栏上所有漂亮的图标，如果你正在使用 nerdtree 的话，强烈推荐这样做。

配置文件的其余部分要么是插件配置，要么是我用来保证质量的个人密钥重映射；我已经尽了最大努力来记录和分割每个部分，所以应该很容易理解每个设置在做什么。

对于任何开始使用 Vim 的人来说，我希望特别注意以下几点:

```
" Disable arrow movement, resize splits instead.
if get(g:, 'elite_mode')
    nnoremap <Up>    :resize +2<CR>
    nnoremap <Down>  :resize -2<CR>
    nnoremap <Left>  :vertical resize +2<CR>
    nnoremap <Right> :vertical resize -2<CR>
endif 
```

Enter fullscreen mode Exit fullscreen mode

因为没有最快的方法来强迫一个人使用主行进行导航。

# 最后备注

我希望这篇文章对你们中的一些人有所帮助，我还远远没有完成我目前的设置，从这个意义上说，这是一个我几乎每天都在玩的玩具。话虽如此，但为了回到帖子最初的前提；它确实是一个非常强大的玩具，特别是当与其他工具如 **Tmux** 结合时，这里是我的**仙丹**【IDE】由 **Vim** 和 **Tmux** 驱动的一个潜行高峰:

[![](img/74833d83e95abc279a3c13d4a9017611.png "source: imgur.com")T2】](http://i.imgur.com/vI0KjXB.gif)

如果你想知道更多关于我的设置，或者想**分享你的**请在下面留下评论。