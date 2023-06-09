# Bash 命令完成——找到$PATH 中的所有猫！

> 原文：<https://dev.to/rrampage/bash-command-completion---finding-all-the-cats-in-your-path-2am2>

今天，我很好奇我的系统里有多少以`cat` 结尾的程序。记得用的是`cat`、`zcat`，想知道有没有类似的节目。在`cat`后按 TAB 键只会给出以`cat`开头的程序。

### 1。`apropos`

在我的[上一篇文章](https://dev.to/rrampage/getting-help-in-the-terminal-4cmo)中，我提到过 apropos 是一种在你的系统中搜索可用程序的方法。`apropos`的搜索字符串可以是任何正则表达式。所以，`apropos 'cat$'`应该解决问题。`cat$`指所有以“猫”结尾的单词

输出有

```
STAILQ_CONCAT (3)     - implementations of singly-linked lists, singly-linked tail queues, lists and tail queues
OPENSSL_strlcat (3ssl) - Memory allocation functions
..
bzcat (1)            - decompresses files to stdout
cat (1)              - concatenate files and print on the standard output
fc-cat (1)           - read font information cache files
gencat (1)           - Generate message catalog 
```

Enter fullscreen mode Exit fullscreen mode

显然，前两者看起来不像程序。为什么`apropos`要归还它们？
让我们看看`apropos`手册

```
man apropos
# apropos - search the manual page names and descriptions 
```

Enter fullscreen mode Exit fullscreen mode

所以`apropos`搜索`man`页面。看起来不仅仅是程序，还有其他东西的`man`页面...

深入挖掘，让我们试试手动操作`man`页！

```
man man
# The table below shows the section numbers of the manual followed by the types of pages they contain.
#       1   Executable programs or shell commands
#       2   System calls (functions provided by the kernel)
#       3   Library calls (functions within program libraries)
#       4   Special files (usually found in /dev)
#       5   File formats and conventions eg /etc/passwd
#       6   Games
#       7   Miscellaneous (including macro packages and conventions), e.g. man(7), groff(7)
#       8   System administration commands (usually only for root)
#       9   Kernel routines [Non standard] 
```

Enter fullscreen mode Exit fullscreen mode

好的。我们感兴趣的是可执行程序，即手册页的第 1 部分。`apropos`有办法使用`-s`标志来限制我们搜索的部分。
**`apropos -s 1 'cat$'`** 为我们提供了所有以名称`cat`结尾的程序，这些程序在手册页中有一个条目，但是它不显示任何没有手册页的程序。

### 2。列出路径上的所有程序

Bash 知道哪些程序可以通过名字直接调用(例如`ls`)而不是通过完整路径(例如`/usr/bin/ls`)的方法是查看`$PATH`环境变量。

**列出路径**上的所有可执行文件

这里有一个小的 bash 片段，它列出了 PATH 中的可执行文件(姑且称之为`paths.sh` )

```
#!/bin/bash
# The directories in $PATH are separated by ":", so we split by it to get individual directories
for pdir in $(echo "$PATH" | tr ":" "\n")
do
    # We `find` all files in the directory which are executable and print the filename
    find "$pdir" -maxdepth 1 -executable -type f -printf "%f\n"
done 
```

Enter fullscreen mode Exit fullscreen mode

如果你更喜欢 Python，这里有一个同样的小 Python 程序(姑且称之为`paths.py` )

```
from itertools import chain
import os
path_dirs = os.environ['PATH'].split(':') # Split PATH by ':' print(path_dirs)
all_files = chain(*(os.walk(p) for p in path_dirs)) # Iterable of all files in the directories contained in PATH is_exec = lambda x : os.access(x, os.X_OK) # Function to check if a filename is executable execs = chain(*([f for f in fs if is_exec(os.path.join(r,f))] for r,_,fs in all_files))
for x in execs:
    print(x) 
```

Enter fullscreen mode Exit fullscreen mode

运行我们的 Bash 或 Python 脚本将会给出正确的输出！

```
sh paths.sh | grep 'cat$'
python3 paths.py | grep 'cat$' 
```

Enter fullscreen mode Exit fullscreen mode

### 3。Bash 完成的力量！

当我在键入一封信后按 TAB 键时，我得到一个建议列表。Bash 是怎么做到的？ [Bash 手册](https://www.gnu.org/software/bash/manual/html_node/Programmable-Completion.html)说它使用内置的`complete`和`compgen`来建议完成。

使用单词列表(-W)或命令列表(-c)生成补全。我们对后者特别感兴趣。`compgen -c`打印路径上的每个可执行文件以及所有 shell 内置和 shell 函数。

`compgen --help`打印以下消息:
`compgen: compgen [-abcdefgjksuv] [-o option] [-A action] [-G globpat] [-W wordlist] [-F function] [-C command] [-X filterpat] [-P prefix] [-S suffix] [word]
Display possible completions depending on the options.`

这些选项代表:

*   答:别名
*   b:外壳内置
*   **c:可执行命令**
*   d:当前目录中的目录
*   e : **导出**变量
*   当前目录中的文件
*   g:系统中的组
*   j:挂起的作业(后台/已停止)
*   k : Bash **关键词**
*   系统服务
*   用户
*   v:所有外壳变量

所以 **`compgen -c | grep 'cat$'`** 应该给我们每一个以`cat`结尾的可执行文件。

### 后记

深入这个兔子洞让我更好地理解了 Bash 补全是如何工作的，apropos 是如何找到相关程序的，以及为什么手册页被组织成不同的部分。