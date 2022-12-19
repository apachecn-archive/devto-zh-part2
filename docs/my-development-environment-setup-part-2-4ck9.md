# 我的开发环境设置(第 2 部分)

> 原文：<https://dev.to/jorge_rockr/my-development-environment-setup-part-2-4ck9>

我在[上一篇文章](https://dev.to/jorge_rockr/my-development-environment-setup-part-1-4a33)中谈到:

*   安装必要的工具
*   安装 zsh 并默认使用它
*   安装哦我的 Zsh
*   安装 vim，为什么是开发的好选择

第二部分的时间到了。

## 6。配置 vim

我知道学习和习惯 vim 很难，但这是我的秘密🤐。

在互联网上做一些关于 vim 技巧和配置的研究，我发现了这个:[https://github.com/sohjiro/.vim](https://github.com/sohjiro/.vim)

是的，一个 shell 脚本来设置 vim 并添加一些类固醇。

只需运行以下命令:

```
curl -L https://raw.githubusercontent.com/sohjiro/.vim/master/run_config.sh | sh 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已💪。

## 6.1。修复 vim 上的 0x7A69 颜色错误(可选)

现在试着打开 *vim* (当然是用命令`vim`)。

出于某种原因，它抛出了一个关于丢失`0x7A69`颜色的错误。我以前没遇到过这种情况，但如果你也遇到了，这里有解决办法。

从这里下载文件`0x7A69_dark.vim`:

[https://www.vim.org/scripts/script.php?script_id=4695](https://www.vim.org/scripts/script.php?script_id=4695)

并且只需要做以下事情:

```
mkdir -p ~/.vim/colors

# <path> is the path where the 0x7A69_dark.vim file is located
mv <path>/0x7A69_dark.vim ~/.vim/colors 
```

Enter fullscreen mode Exit fullscreen mode

下一次你打开 *vim* 它就会被修复。

## 6.2。修复 vim 上奇怪的退格字符(可选)

解决了颜色错误后，我开始测试键盘，我发现当按 backspace 时总是得到`^?`字符😫。

那是什么？我不知道，但是解决这个问题的方法是编辑文件`.zshrc`(记得我们用的是 *Zsh* ，对吗？)并在底部添加下一行:

```
stty erase '^?' 
```

Enter fullscreen mode Exit fullscreen mode

解决🔨。

## 7。安装 vim 插件

您在步骤 6 中安装的配置有一些内置插件。

要安装它们，打开 *vim* 并输入:

```
:PlugInstall 
```

Enter fullscreen mode Exit fullscreen mode

只需等到所有东西都安装好:

[![](../Images/7758744c437bf831f2006d78a9f5ca84.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pSnEoJ0U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p98quqngb9bv8ru52bkm.png)

## 8。掌握 vim: NerdTree 是你的朋友

我在 step 7 中告诉你的一个插件是 [NerdTree](https://github.com/scrooloose/nerdtree) ，它可以让你打开当前目录，浏览文件，添加/重命名/删除/移动/复制文件等等。是不是很酷？。

[![](../Images/dcff006a50e334bc15d802f215ee73ae.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PMc-BEUh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9tag38pxlfyfvqulmk5k.png)

要使用它，打开 *vim* 并输入:

```
:NerdTreeToggle 
```

Enter fullscreen mode Exit fullscreen mode

每次需要打开当前目录时...有点无聊，但好的一面是你可以将命令映射到一个键盘组合。

编辑文件`~/.vim/vim_config.vim`并添加以下行:

```
noremap <C-w>n :NERDTreeToggle<cr> 
```

Enter fullscreen mode Exit fullscreen mode

重启 *vim* ，现在按下`Ctrl-w + n`就可以了。要关闭*树*只需再次按下组合键。

最后，要对目录或文件进行操作(移动、复制、删除等)，打开 *NerdTree* ，将光标移动到你想要的目录或文件，然后按`m`:

[![](../Images/c44823610f302c5a631626c751c6ea5d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XN4Bo1WQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/071l7d3kosglh8gakn2v.png)

选择您想要的选项。

## 9。掌握 vim:窗口拆分

有时你需要同时打开两个或更多的文件，所以窗口分割就来了。

再次编辑文件`~/.vim/vim_config.vim`并添加以下几行:

```
noremap <C-w>- :split<cr>
noremap <C-w>\| :vsplit<cr> 
```

Enter fullscreen mode Exit fullscreen mode

重启 *vim* ，现在按`Ctrl-w + -`水平分割当前窗口:

[![](../Images/d7dde0bfcad10ab53889416c550ec860.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oXS8rzB6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jwyew58k9ojt9m16e1pn.png)

和`Ctrl-w + |`(竖线字符)垂直分割当前窗口:

[![](../Images/1e1a9dea8c4ed6b01b4ab190969b9e34.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hNa9FH1G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sk2y62efchsj15quad0g.png)

要在面板之间切换，只需按两次`Ctrl+ w`。要关闭面板，只需按下`Ctrl-w + c`。

现在，混合使用 *NerdTree* 和窗口分割来打开你想要的文件！

[![](../Images/c1dbb4b29d65c387ade56a46a5408dcb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MzanJDm1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oigfmnpr4keskv91a143.png)

## 10。掌握 vim:删除文件缓冲区

使用崇高的文本，视觉代码或任何 IDE 当你完成工作的一些文件，你只需关闭它。嗯，这在 vim 上是不一样的，你可以关闭你的面板(`Ctrl-w + c`)，但是缓冲区仍然存在😫。幸运的是，我找到了关闭当前缓冲区的方法！🤓

在`~/.vim/plugin/bclose.vim`上创建一个新文件(如果*插件*目录不存在，请也创建一个)并粘贴以下内容(归功于 [vim.wikia](http://vim.wikia.com/wiki/Deleting_a_buffer_without_closing_the_window) ):

```
" Delete buffer while keeping window layout (don't close buffer's windows).
" Version 2008-11-18 from http://vim.wikia.com/wiki/VimTip165
if v:version < 700 || exists('loaded_bclose') || &cp
  finish
endif
let loaded_bclose = 1
if !exists('bclose_multiple')
  let bclose_multiple = 1
endif

" Display an error message.
function! s:Warn(msg)
  echohl ErrorMsg
  echomsg a:msg
  echohl NONE
endfunction

" Command ':Bclose' executes ':bd' to delete buffer in current window.
" The window will show the alternate buffer (Ctrl-^) if it exists,
" or the previous buffer (:bp), or a blank buffer if no previous.
" Command ':Bclose!' is the same, but executes ':bd!' (discard changes).
" An optional argument can specify which buffer to close (name or number).
function! s:Bclose(bang, buffer)
  if empty(a:buffer)
    let btarget = bufnr('%')
  elseif a:buffer =~ '^\d\+$'
    let btarget = bufnr(str2nr(a:buffer))
  else
    let btarget = bufnr(a:buffer)
  endif
  if btarget < 0
    call s:Warn('No matching buffer for '.a:buffer)
    return
  endif
  if empty(a:bang) && getbufvar(btarget, '&modified')
    call s:Warn('No write since last change for buffer '.btarget.' (use :Bclose!)')
    return
  endif
  " Numbers of windows that view target buffer which we will delete.
  let wnums = filter(range(1, winnr('$')), 'winbufnr(v:val) == btarget')
  if !g:bclose_multiple && len(wnums) > 1
    call s:Warn('Buffer is in multiple windows (use ":let bclose_multiple=1")')
    return
  endif
  let wcurrent = winnr()
  for w in wnums
    execute w.'wincmd w'
    let prevbuf = bufnr('#')
    if prevbuf > 0 && buflisted(prevbuf) && prevbuf != w
      buffer #
    else
      bprevious
    endif
    if btarget == bufnr('%')
      " Numbers of listed buffers which are not the target to be deleted.
      let blisted = filter(range(1, bufnr('$')), 'buflisted(v:val) && v:val != btarget')
      " Listed, not target, and not displayed.
      let bhidden = filter(copy(blisted), 'bufwinnr(v:val) < 0')
      " Take the first buffer, if any (could be more intelligent).
      let bjump = (bhidden + blisted + [-1])[0]
      if bjump > 0
        execute 'buffer '.bjump
      else
        execute 'enew'.a:bang
      endif
    endif
  endfor
  execute 'bdelete'.a:bang.' '.btarget
  execute wcurrent.'wincmd w'
endfunction
command! -bang -complete=buffer -nargs=? Bclose call s:Bclose('<bang>', '<args>')
nnoremap <silent> <Leader>bd :Bclose<CR> 
```

Enter fullscreen mode Exit fullscreen mode

然后，编辑文件`~/.vim/vim_config.vim`并添加下面一行:

```
noremap <C-w>b :Bclose<cr> 
```

Enter fullscreen mode Exit fullscreen mode

重启 *vim* ，打开任意文件:

[![](../Images/634a3b76db54f7f1ef776d7b4e059deb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7Qzsu3iC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5j2yv9r8vj5be1nboc4z.png)

按下`Ctrl-w + b`和**就这样**！

[![](../Images/46a460bc7b8b34ad54b94c3ba9020762.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YLGx0ZvX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/69fspgbha3ch0myp0p1y.png)

伙计们，就这样吧。

有什么问题尽管问！

感谢你阅读❤️