# 带 Devicons 的 VIM fzf

> 原文：<https://dev.to/coreyja/vim-fzf-with-devicons-11ee>

[![Example Screenshot](img/a082731fc57b3fc66be30f9d86ad8417.png)T2】](https://coreyja.cimg/posts/vim-fzf-with-devicons/example-screenshot-7d212371.png)

*此文最初发布于[coreyja.com](https://coreyja.com/vim-fzf-with-devicons/)T3】*

## 背景

当我用 fzf 写我的上一篇博文 [VIM 拼写建议时，我得到了一些关于](https://coreyja.com/vim-spelling-suggestions-fzf/) [dev.to](https://dev.to) 的评论！其中一条来自 [@maxdevjs](https://dev.to/maxdevjs) 。我们交流了几句，我在他的 dotfiles 里发现了 [`vim-devicons`](https://github.com/ryanoasis/vim-devicons) ！它与[书呆子](https://github.com/preservim/nerdtree)和[航空公司](https://github.com/vim-airline/vim-airline)一起开箱即用，我真的很喜欢它！ [@maxdevjs](https://dev.to/maxdevjs) 也有这个 [Github 问题](https://github.com/ryanoasis/vim-devicons/issues/106)的链接，关于人们如何能够添加 fzf 支持。我是在 fzf 踢，所以这让我感兴趣！

## 原解

Github 问题已经有了一个功能解决方案！以下是我用作此基础的评论，[https://github . com/ryano asis/vim-devicons/issues/106 # issue comment-354629715](https://github.com/ryanoasis/vim-devicons/issues/106#issuecomment-354629715)

以下是来自该评论的源代码:

```
" Files + devicons
function! Fzf_dev()
  let l:fzf_files_options = '--preview "rougify {2..-1} | head -'.&lines.'"'

  function! s:files()
    let l:files = split(system($FZF_DEFAULT_COMMAND), '\n')
    return s:prepend_icon(l:files)
  endfunction

  function! s:prepend_icon(candidates)
    let l:result = []
    for l:candidate in a:candidates
      let l:filename = fnamemodify(l:candidate, ':p:t')
      let l:icon = WebDevIconsGetFileTypeSymbol(l:filename, isdirectory(l:filename))
      call add(l:result, printf('%s %s', l:icon, l:candidate))
    endfor

    return l:result
  endfunction

  function! s:edit_file(item)
    let l:pos = stridx(a:item, ' ')
    let l:file_path = a:item[pos+1:-1]
    execute 'silent e' l:file_path
  endfunction

  call fzf#run({
 \ 'source': <sid>files(),
 \ 'sink':   function('s:edit_file'),
 \ 'options': '-m ' . l:fzf_files_options,
 \ 'down':    '40%' })
endfunction 
```

Enter fullscreen mode Exit fullscreen mode

上面做了三件基本的事情:

1.  使用 [`rouge`](https://github.com/rouge-ruby/rouge) gem 在 fzf 中创建语法高亮预览
2.  将 devicon 前置到由`$FZF_DEFAULT_COMMAND`生成的每一行，并将其用作`fzf`源
3.  设置`sink`,以便当您选择一个选项时，图标被去掉，您可以编辑目标文件

当我把它复制到我的 dotfiles 中，并摆弄它的时候，我有了一些我想改进的地方！

## 预览

最初的解决方案使用`rogue`来创建语法高亮预览。我喜欢预览，但觉得流氓渲染花了太长时间。我对 fzf 的使用完全是为了速度，所以渲染滞后于我选择的选项是显而易见的，也是令人讨厌的。
最近我(重新)发现了 [`bat`](https://github.com/sharkdp/bat) ，是`cat`的替换，还包括语法高亮！

所以我做的第一件事就是把`rougify`替换成`bat`,得到了如下一行。

```
let l:fzf_files_options = '--preview "bat {2..-1} | head -'.&lines.'"' 
```

Enter fullscreen mode Exit fullscreen mode

这成功了！我得到了几乎即时的结果🎉。不幸的是，它没有突出语法。我意识到这是因为`bat`试图与`cat`兼容，如果它检测到你正在通过管道输出(就像预览版中发生的那样)，它默认表现得像一个`cat`克隆体。幸运的是，解决这个问题就像把一个选项传给`bat`，`--color always`成功了！`bat`默认还包括行号和表头。我喜欢行号但不喜欢标题，所以我通过明确地说我只想要带有`--style numbers`的行号来隐藏它。现在我的预览线看起来如下:

```
let l:fzf_files_options = '--preview "bat --color always --style numbers {2..} | head -'.&lines.'"' 
```

Enter fullscreen mode Exit fullscreen mode

在这之后，我对 fzf 提供的预览感到兴奋。

## 前置图标

我喜欢 fzf，因为它是一个流式模糊查找器，这意味着你甚至可以在收到所有输入之前就开始模糊查找。这很重要，因为我将我的默认 fzf 命令(`$FZF_DEFAULT_COMMAND`)设置为如下，它包括大多数隐藏的文件并考虑符号链接。因此，在一个典型的 Rails 项目中，我经常会有数万个结果，并有一个`node_modules`目录。

```
rg --files --no-ignore --hidden --follow --glob "!.git/*" 
```

Enter fullscreen mode Exit fullscreen mode

当我第一次开始测试这些解决方案时，我使用的目录只有有限数量的文件。在这些目录中，我没有注意到添加图标时速度变慢。但是当我移动到一些实际的项目目录时，我意识到 fzf 在一次填充所有结果之前会打开空的。这种延迟让我很烦，我有心情去优化！

## 串流痛击

我从最近在 VIM 中与 fzf 的合作中了解到，它为其源代码接受了一些东西。两个大的是 VIM 列表或指示要调用的 shell 命令的字符串。在最初的解决方案中，我们采用 fzf 默认命令，并在 vim 中用`system`执行它。这意味着我们将获取 fzf 默认命令的全部输出，并在 vim 中处理它。然后我们在新行上分割它，得到一个 vim 列表。我们遍历列表中的每个文件名，并预先设置正确的 devicon。然后我们将这个列表传递给`fzf`作为源。
这工作得非常好，但是这就是我注意到的滞后的来源。在 fzf 向我显示任何结果之前，rg 命令必须结束，然后文件名必须加上图标，最后，fzf 可以呈现列表。这意味着 fzf 不再能够“流式传输”,必须等待所有输入准备就绪后才能开始显示结果。

如果使用 bash 命令作为 fzf 源代码的字符串，默认情况下使用流。我意识到要实现流，我需要一个 VIM 之外的解决方案。由于输入是一个 bash 命令，bash 显然是我的第一选择，我正在进行概念验证！基本的想法是，我将有一个脚本，通过 STDIN 接收行，并为每一行预先添加正确的 devicon。然后，它会将每一行输出到 STDOUT。这样，它将“流”出带有前置 devicons 的新行。

我决定快速查看，以确保有人还没有这样做，并发现了 [ryanoasis/devicons-shell](https://github.com/ryanoasis/devicons-shell) ，它与 [`vim-devicons`](https://github.com/ryanoasis/vim-devicons) 插件出自同一个人之手！它并不完全符合我的要求，但也很接近了。我用它的大部分来写我的初始原型，最终看起来如下:

```
function devicons_get_filetype_symbol {
  declare -A extensions=(
    [ai]=
    ... (Shortened for brevity)
    [zsh]=
  )

  local folder=""
  local filetype
  local default=
  local exist_check=1
  local input=$1
  local filename="$1"
  # using ## for possibly more than one "." (get after last one):
  local filetype="${filename##*.}"

  if [ -d "$filename" ]; then local symbol=$folder
  elif [ ! -z "$filetype" ] && [ ${extensions[$filetype]+$exist_check} ]; then local symbol=${extensions[$filetype]}
  else local symbol=$default
  fi echo "$symbol"
  return 0
}

while IFS= read -r line; do echo -e "$(devicons_get_filetype_symbol $line)  $line"
done 
```

Enter fullscreen mode Exit fullscreen mode

我用`ls | ./protype_script`测试了这个，对结果非常满意！现在只需将它集成到 VIM 中，这非常容易。我们实际上已经删除了一些`.vimrc`代码！

现在，为了利用 fzf 流，我们传递一个字符串作为 fzf 的源。我们将默认命令的结果通过管道传输到新脚本中。这允许我们删除`files`和`prepend_icon`函数，因为我们用脚本替换了它们的功能。

```
call fzf#run({
 \ 'source': $FZF_DEFAULT_COMMAND.' | prototype_script',
 \ 'sink':   function('s:edit_devicon_prepended_file'),
 \ 'options': '-m ' . l:fzf_files_options,
 \ 'down':    '40%' }) 
```

Enter fullscreen mode Exit fullscreen mode

我在我的小样本目录中尝试了一下，看起来效果很好！我转到了更大的目录，一切看起来都很好！我可以看到结果正在流入，因为 fzf 中的结果总数一直在上升，但我仍然可以同时使用它！

在文件之间跳了几分钟后，我意识到有时候我会搜索一个还没有添加到 fzf 的文件，在我等待的结果出现之前会有一个延迟。在添加 devicons 之前，我没有经历过这种情况，所以我怀疑这就是问题所在。我用股票`:Files`命令做了一个并排比较，结果很清楚。默认完成的速度要快几个数量级。我可以看到 fzf 的总负载，但它是在几分之一秒。使用我的 bash 解决方案，加载所有文件只需要几十秒钟。还有改进的空间！

## 铁锈实现

我几乎没有使用 Rust 的经验，但是我对它感兴趣已经有一段时间了。巧合的是，`bat`也是用 Rust 编写的，它在预览部分的速度让我对它在这里的帮助寄予厚望。

由于对 Rust 相当陌生，我的第一步是弄清楚如何最好地使用散列映射来扩展 devicon 映射。我偶然发现的第一个东西是 [`lazy_static`](https://github.com/rust-lang-nursery/lazy-static.rs) ，它似乎做了我想要的事情。这在运行时是静态的；我在寻找一些编译时静态的东西，但是从我非常快速的一瞥来看，Rust 似乎不那么容易支持 <sup id="fnref1">[1](#fn1)</sup> ，所以运行时静态就足够好了！

```
lazy_static! {
  static ref SYMBOL_MAP: HashMap<&'static str, &'static str> = {
    let mut m = HashMap::new();
    m.insert("ai", "");
    ...
    m.insert("zsh", "");

    m
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

在我设置好 HashMap 之后，我需要做实际的文本流部分。我发现[这个栈溢出答案](https://stackoverflow.com/a/30186553)，是 80%的工作！这是他们简单的文本流例子。

```
fn main() {
    let stdin = io::stdin();
    for line in stdin.lock().lines() {
        println!("{}", line.unwrap());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我只需要解析扩展并在散列表中查找符号。Rust 标准库再次派上了用场，因为解析扩展是一行程序！

```
Path::new(filename).extension().and_then(OsStr::to_str) 
```

Enter fullscreen mode Exit fullscreen mode

加入一点铁锈色的魔法🎉，铁锈版本已经准备好测试了！

```
#[macro_use]
extern crate lazy_static;

use std::collections::HashMap;
use std::ffi::OsStr;
use std::io::{self, BufRead};
use std::path::Path;

static DEFAULT_SYMBOL: &str = & "";
lazy_static! {
  static ref SYMBOL_MAP: HashMap<&'static str, &'static str> = {
    let mut m = HashMap::new();
    m.insert("ai", "");
    ...
    m.insert("zsh", "");

    m
  };
}

fn get_extension_from_filename(filename: &str) -> Option<&str> {
  Path::new(filename)
    .extension()
    .and_then(OsStr::to_str)
}

fn main() {
  let stdin = io::stdin();
  for line in stdin.lock().lines() {
    let filename = line.unwrap();
    let extension = get_extension_from_filename(& filename);
    let symbol = match extension {
      Some(extension) => SYMBOL_MAP.get(& extension).unwrap_or(& DEFAULT_SYMBOL),
      None => DEFAULT_SYMBOL
    };
    println!("{} {}", symbol, filename);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我将这个新版本插入到 vim 中，只是简单地将源代码改为我的新 Rust 构建的二进制文件，而不是原型 bash 脚本。并且:drumroll:，它工作了，并且比 bash 版本快得多！它甚至没有明显慢于默认的`:Files`命令！

## 表现

这是一个非常不科学的单次测试，我在一个大目录中对三个不同的命令计时。

1.  单独使用我默认的 FZF 命令
2.  在原型 bash 实现中使用它
3.  在我的 Rust 实现中使用它

按照这个顺序，这是我的结果！Rust 版本仅仅比不做任何 devicons 慢一点点。它彻底摧毁了 bash 实现！

```
coreyja in ~/Projects on ca/master/devicons
★  time rg --files --no-ignore --hidden --follow > /dev/null

real    0m0.302s
user    0m0.627s
sys     0m1.515s

coreyja in ~/Projects on ca/master/devicons
★  time rg --files --no-ignore --hidden --follow | add_devicon_before_each_file > /dev/null

real    3m55.226s
user    2m8.614s
sys     1m45.380s

coreyja in ~/Projects on ca/master/devicons
★  time rg --files --no-ignore --hidden --follow | devicon-lookup > /dev/null

real    0m0.409s
user    0m0.743s
sys     0m1.535s 
```

Enter fullscreen mode Exit fullscreen mode

我用 VIM 中的原始实现和最终 rust 版本进行了一个更不科学的测试。因为我是在 VIM 内部进行测试，并且在一个窗口出现之前一直在计时，所以没有一种简单的方法来自动计时。所以我拿出了我的秒表。使用我的 rust 实现`:call Fzf_files_with_dev_icons($FZF_DEFAULT_COMMAND)`，fzf 窗口几乎瞬间出现，所有的结果都已经被填充了！使用最初的 VIM 实现`:call Fzf_dev()`，加载窗口大约需要 4.5 秒。当它加载时，它被所有的文件完全填充。

这是一个巨大的改进，性能现在对我来说肯定是可以接受的，并且已经添加到我的点文件中了！

## 最终版本

在我让这个 Rust 版本正常工作后，我还花了一些时间来重构重用同一个函数，只搜索 git 下的文件。这就像提取调用参数的函数一样简单。我也将相同的 devicons 集成到了`:GFiles?`中，它只显示在 git 中发生变化的文件。这值得自己的博客帖子，因为它需要一点欺骗！

在这里，我们有它，我的最终(目前)fzf 文件模糊的发现与装置供电的 Rust！

```
" Files + devicons
function! Fzf_files_with_dev_icons(command)
  let l:fzf_files_options = '--preview "bat --color always --style numbers {2..} | head -'.&lines.'"'
   function! s:edit_devicon_prepended_file(item)
    let l:file_path = a:item[4:-1]
    execute 'silent e' l:file_path
  endfunction
   call fzf#run({
 \ 'source': a:command.' | devicon-lookup',
 \ 'sink':   function('s:edit_devicon_prepended_file'),
 \ 'options': '-m ' . l:fzf_files_options,
 \ 'down':    '40%' })
endfunction
 function! Fzf_git_diff_files_with_dev_icons()
  let l:fzf_files_options = '--ansi --preview "sh -c \"(git diff --color=always -- {3..} | sed 1,4d; bat --color always --style numbers {3..}) | head -'.&lines.'\""'
   function! s:edit_devicon_prepended_file_diff(item)
    echom a:item
    let l:file_path = a:item[7:-1]
    echom l:file_path
    let l:first_diff_line_number = system("git diff -U0 ".l:file_path." | rg '^@@.*\+' -o | rg '[0-9]+' -o | head -1")
     execute 'silent e' l:file_path
    execute l:first_diff_line_number
  endfunction
   call fzf#run({
 \ 'source': 'git -c color.status=always status --short --untracked-files=all | devicon-lookup',
 \ 'sink':   function('s:edit_devicon_prepended_file_diff'),
 \ 'options': '-m ' . l:fzf_files_options,
 \ 'down':    '40%' })
endfunction
 " Open fzf Files " Open fzf Files
map <C-f> :call Fzf_files_with_dev_icons($FZF_DEFAULT_COMMAND)<CR> " :Files
map <C-d> :call Fzf_git_diff_files_with_dev_icons()<CR> " :GFiles?
map <C-g> :call Fzf_files_with_dev_icons("git ls-files \| uniq")<CR> " :GFiles 
```

Enter fullscreen mode Exit fullscreen mode

我还发布了 Rust 代码作为一个[机箱](https://crates.io/crates/devicon-lookup)，你可以用
安装它

```
cargo install devicon-lookup 
```

Enter fullscreen mode Exit fullscreen mode

这将为您提供可以在 VIM 设置中使用的`devicon-lookup`二进制文件！

## 鸣谢

当我准备发表这篇博文时，我意识到我根本没有提到 [`fzf.vim`](https://github.com/junegunn/fzf.vim) 插件！我称之为 fzf `:Files`视图的“默认”实现，这是一个令人惊奇的插件！我基本上试着尽可能地复制这个插件，尤其是在尝试重新实现`:GFiles?`支持的时候。

编辑:其中有些信息略有错误。我又发了一个帖子，在那里我再次尝试了`phf`！看看这里的！

* * *

1.  之后，我找到并研究了 [phf](https://github.com/sfackler/rust-phf) ，它确实提供了编译时静态映射。它不使用 Rust HashMap，而是实现自己的散列功能。他们的哈希实现也不同于 Rust HashMap，对这个项目没有意义。为此我想使用稳定的 Rust，这意味着我不能使用语法更漂亮的`phf_macros`。处于稳定的 rust 意味着我将被迫使用 codegen 选项，这需要一个初步的“编译”步骤，在这个步骤中，您运行一个构建脚本，输出一个包含哈希代码的 rust 源文件。这看起来像是很多额外的工作，根据[这个博客](http://siciarz.net/24-days-rust-static-initialization/)的说法，升级的速度优势可能存在，但不会有数量级的增加，我已经对性能感到满意了。 [↩](#fnref1)