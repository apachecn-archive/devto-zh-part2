# 使用来自 windows gvim 的: terminal 的各种 shell

> 原文：<https://dev.to/shiena/windows-gvim-terminal-41ac>

# 前言

最近，因为 vim 上安装的`:terminal`看起来很方便，所以从 Windows 上进行了各种各样的摆弄。
Windows 版可以从[vim-jp](https://vim-jp.org/) 下载 zip。
如果有什么问题，也可以试试夜总会大楼的[vim/vim-win32-installer](https://github.com/vim/vim-win32-installer/releases) 。

[![gvim-terminal.png](img/aa35572f6898fb3af1e952c954687e20.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FBu4BLOi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/urydzfzm37pqxttf6g28.png)

在上面的捕获中，我们将 Vim 划分为四个部分，并分别移动以下外壳:

|  |  |
| --- | --- |
| 去吧，巴什 | 命令提示符 |
| WSL Bash | PowerShell |

`:terminal`的帮助和概要从这里开始

*   [terminal - Vim 日语文档](http://vim-jp.org/vimdoc-ja/terminal.html)
*   [在 Vim 中终端功能开始工作了。](https://mattn.kaoriya.net/software/vim/20170724011404.htm)

以下，根据我的喜好将选项指定为`++close`和`++curwin`。

# 移动命令提示符和 PowerShell 的家伙

这两个很简单。

```
:terminal ++close ++curwin 
```

Windows 的缺省 shell 是命令提示符，因此可以不指定 shell。

```
:terminal ++close ++curwin powershell 
```

将 PowerShell 指定为参数，因为它不是默认 shell。

# 移动 wsl bash 的家伙

### Windows10 秋季创作者更新以降

```
:terminal ++close ++curwin ubuntu 
```

`ubuntu.exe`命令兼有`bash`的执行和主目录的移动，所以很简单。

### Windows10 秋季创造者 Updateより前

```
:terminal ++close ++curwin C:/WINDOWS/System32/bash.exe -c "cd; bash -l" 
```

因为无法从 Windows 端移动到 WSL 的主目录，所以用`-c`选项移动后启动了`bash`。
如果 shell 启动时的目录在任何地方都可以，则不需要此选项。

# 移动 Git for Windows 的 Bash 的家伙

### Vim8.0.1240 to descend

因为想传递环境变量，所以使用`term_start`函数而不是`:terminal`。

```
function! GitBash()
    " 日本語 Windowsの場合`ja`が設定されるので、入力ロケールに合わせたUTF-8に設定しなおす
    let l:env = {
 \ 'LANG': systemlist('"C:/Program Files/Git/usr/bin/locale.exe" -iU')[0],
 \ }

    " remote 連携のための設定
    if has('clientserver')
        call extend(l:env, {
 \ 'GVIM': $VIMRUNTIME,
 \ 'VIM_SERVERNAME': v:servername,
 \ })
    endif

    " term_startでgit for windowsのbashを実行する
    call term_start(['C:/Program Files/Git/bin/bash.exe', '-l'], {
 \ 'term_name': 'Git',
 \ 'term_finish': 'close',
 \ 'curwin': v:true,
 \ 'cwd': $USERPROFILE,
 \ 'env': l:env,
 \ })

endfunction

nnoremap <Leader>g :<C-u>call GitBash()<CR> 
```

### vim 8.0.1240 之前

vim 8.0.1240 之前的 Windows 版在`term_start`中有错误，不会传递`env`，所以直接设定环境变量。

```
function! GitBash()
    " 日本語 Windowsの場合`ja`が設定されるので、入力ロケールに設定しなおす
    let l:oldlang = $LANG
    let $LANG = systemlist('"C:/Program Files/Git/usr/bin/locale.exe" -iU')[0]

    " remote 連携のための設定
    let l:oldgvim = $GVIM
    let l:oldvimserver = $VIM_SERVERNAME
    let $GVIM = $VIMRUNTIME
    let $VIM_SERVERNAME = v:servername

    " Git for Windowsに同梱されたvimで誤作動するのでvimが設定する環境変数を削除する
    let l:oldvim = $VIM
    let l:oldvimruntime = $VIMRUNTIME
    let l:oldmyvimrc = $MYVIMRC
    let l:oldmygvimrc = $MYGVIMRC
    let $VIM = ''
    let $VIMRUNTIME = ''
    let $MYVIMRC = ''
    let $MYGVIMRC = ''

    " :terminalを実行したvimのservernameを設定する
    if has('clientserver')
        let $VIM_SERVERNAME = v:servername
    endif

    " ホームディレクトリに移動する
    lcd $USERPROFILE
    " :terminalでgit for windowsのbashを実行する
    terminal ++close ++curwin C:/Program Files/Git/bin/bash.exe -l

    " 環境変数とカレントディレクトリを元に戻す
    let $LANG = l:oldlang
    let $GVIM = l:oldgvim
    let $VIM_SERVERNAME = l:oldvimserver
    let $VIM = l:oldvim
    let $VIMRUNTIME = l:oldvimruntime
    let $MYVIMRC = l:oldmyvimrc
    let $MYGVIMRC = l:oldmygvimrc
    lcd -
endfunction

nnoremap <Leader>g :<C-u>call GitBash()<CR> 
```

要从在`:terminal`中启动的 Git Bash 运行附带的 vim，由于以下原因需要进行各种设定。

*   如果不将`LANG`设为`ja_JP`或`ja_JP.UTF-8`这样的值，`ls`就不会出现日语
*   如果 GVim 仍然设置环境变量，则与 Git for Windows 附带的 vim 路径不匹配，出现错误

此外，如果将以下的 alias 设定为`~/.bashrc`，则可以使用[remote - Vim 日语文档](http://vim-jp.org/vimdoc-ja/remote.html)的功能，在不使用 Git Bash 附带的 vim 的情况下用 Gvim 打开文件。

```
if [ -x $(cygpath "$GVIM\\vim.exe") -a -n $VIM_SERVERNAME ]; then alias vim="$(cygpath "$GVIM\\vim.exe") --servername $VIM_SERVERNAME --remote-silent"
fi 
```

# 最后

与 mintty 相比，虽然有些部分绘画速度稍慢，或者没有达到 256 色，但通过 vim 的操作进行终端复制，将多个外壳集中在一个窗口中很方便。

# 参考链接

*   [terminal - Vim 日语文档](http://vim-jp.org/vimdoc-ja/terminal.html)
*   [remote - Vim 日语文档](http://vim-jp.org/vimdoc-ja/remote.html)
*   [在 Vim 中终端功能开始工作了。](https://mattn.kaoriya.net/software/vim/20170724011404.htm)
*   [发布版](https://vim-jp.org/)
*   [骑士构建版](https://github.com/vim/vim-win32-installer/releases)