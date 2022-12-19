# 对初学者有帮助的终端命令！

> 原文：<https://dev.to/milkstarz/helpful-terminal-commands-for-beginners-5cjh>

这张小抄最初发布在[malikbrowne.com](https://www.malikbrowne.com/blog)上。

我喜欢在 Mac 上学习新的命令和工具来优化我的工作流程。因为我已经开始使用更多面向终端的应用程序，所以我想与初学者和终端爱好者分享我使用的几个命令。

**注意:**本文假设您正在使用某种类型的 **UNIX shell** ，偏好 Mac。其中一些命令可能无法在 Windows CMD/Powershell 上运行。

## 基本命令

shell 中的命令可以有很多不同的用法——但是我要说的是，需要学习的重要命令分为三大类:

1.  导航和使用文件和目录
2.  为输入操作输出
3.  寻找东西

### 导航和处理文件&目录

在计算机上，文件和目录(也称为文件夹)负责管理信息。以下是一些命令，可以让您在处理文件时更加轻松:

*   `cd` -导航至不同的目录
*   `pwd` -查看当前目录的名称
*   `ls` -列出当前目录下的所有文件
*   `mkdir` -新建一个目录
*   `touch` -创建一个新文件
*   `cp` -复制文件
*   `mv` -移动文件或目录
*   `rm` -删除文件或目录
*   `zip` -将文件压缩成 zip 档案
*   从 zip 存档中提取文件
*   `chmod` -允许你使一个文件可执行，并改变你的机器授予它的权限。
    *   为了使一个文件可执行，你可以键入`chmod +x [name of file]`
*   允许你在 Linux 命令行中使用 tarballs。它有很多用途，包括系统的包管理。
    *   `tar -cvf`允许你创建一个**。塔尔**档案馆
    *   `tar -xvf`允许您“解开”一个**。塔尔**档案馆
    *   `tar -tvf`列出一个**的内容。塔尔**档案馆

**荣誉提及:`sudo`**

`sudo`是 bash 接口中一个非常广泛使用的命令，它允许您使用管理权限或 root 权限运行命令。

例如，如果您在没有`sudo`权限的情况下编辑机器根目录(也称为路径`/`)中的任何文件，您将被拒绝编辑该文件的权限。

为了您的安全，有几件事情您的计算机不允许您作为一个`sudo`d 命令来做，例如运行 bash 脚本。

### 操纵输出进行输入

通常，编写脚本时，您试图实现的目标是通过文本运行过滤器，检查服务器的输出日志，或者对多个文件运行批处理命令。

以下是一些有助于操作文件输出的命令:

*   `cat` -显示文件的内容
*   `head` -显示文件的前几行
*   `tail` -显示文件的最后几行
    *   使用带有`tail`的`-f`标志将允许您在文件更新到来时看到它们。这对于跟踪服务器的日志输出非常有帮助。
*   `|` -管道字符获取一个命令的输出，并将其用于另一个命令的输入
*   `*` -匹配文件名中的零个或多个字符
    *   实际上有一整套方法可以通过 globs 来匹配多个文件和名称！
*   `?` -匹配文件名中的任意单个字符
*   `>` -将命令的输出存储到文件中，如果文件不存在，则创建一个新文件
    *   **警告:**这将覆盖并替换原始文件中的所有内容。
*   `>>` -将一个命令的输出连接到一个文件，如果没有找到，则创建一个新文件

### 寻找事物

*   `echo` -显示文本或其他命令的编译文本。非常适合测试命令输出。
*   `grep` -允许您使用某种模式或正则表达式逐行查找结果。
*   类似于 grep，但是有更好的代码搜索，因为它知道在你希望它搜索的地方搜索。(忽略版本控制目录等。)

## 我发现的其他有用的命令

*   `pbcopy` -将所选文本复制到剪贴板缓冲区
*   `history` -打印会话的命令历史
    *   这对于 grep 和管道符号非常有用，它将允许你搜索你的历史:`history | grep [part of command]`
*   `pbpaste` -将剪贴板缓冲区中的选定文本粘贴到终端中
*   `curl` -允许你进行 HTTP 调用 RESTful 端点
*   `kill` -终止给定进程 id (pid)的正在运行的进程
*   `killall` -终止某一类型的所有正在运行的进程
*   `lsof -i tcp:[port number here]` -列出特定端口上所有正在运行的进程
    *   当您遇到类似以下的错误时，这一条特别有用:

```
 Error: listen EADDRINUSE :::3005
        at Object.exports._errnoException (util.js:1023:11)
        at exports._exceptionWithHostPort (util.js:1046:20)
        at Server._listen2 (net.js:1261:14)
        at listen (net.js:1297:10)
        at Server.listen (net.js:1375:9)
        at Object.<anonymous> (/path/to/node/server/server.js:15:34)
        at Module._compile (module.js:571:32)
        at loader (/path/to/node/modules/node_modules/babel-register/lib/node.js:144:5)
        at Object.require.extensions.(anonymous function) [as .js] (/var/www/html/gcsbpo/rocc/node_modules/babel-register/lib/node.js:154:7)
        at Module.load (module.js:488:32) 
```

Enter fullscreen mode Exit fullscreen mode

它提供了一种简单的方法来找出 pid 来终止正在运行的进程。

### 快捷键/提示&招数

*   `!$`展开前一条命令的最后一个参数。对于在同一文件上链接命令非常有用
*   `!!` -重复上一次执行的命令
    *   这些都要感谢托马斯
    *   ！[number]:从您的历史记录中重复命令(您可以使用 fc -l 查看可用的命令历史记录(或使用 fc -l 1 查看您的历史记录的所有内容)
    *   ！！[附加内容]:重新执行上一个命令，在命令的末尾进行定位。
    *   [额外的东西]！！:重新执行上一个命令，在命令的开头加上[额外的东西]。对于作为普通用户执行需要用`sudo`运行的东西来说非常方便
    *   ！！:s/[SEARCH]/[REPLACE]:重新执行最后一个命令，用[REPLACE]替换第一个子字符串[SEARCH]
    *   ！！:s/[SEARCH]/[REPLACE]:重新执行上一个命令，用[REPLACE]es 替换所有子字符串[SEARCH]
*   `clear` -清除终端的所有输出，旧输出-仍可通过向上滚动来访问
*   `CMD + k` -清除会话的所有输出，仅保留先前调用的命令
*   `CTRL + c` -中止当前正在运行的进程并关闭。
*   `CTRL + z` -暂停(`SIGSTP`)任何当前正在运行的进程
    *   **注意:** `CTRL + C`中止进程，但是`CTRL + z`让它在内存中空闲。
*   `CTRL + a` -带您到 bash 输入的开始
*   `CTRL + e` -将您带到 bash 输入的末尾
*   `CTRL + u` -清除光标前的所有输入
**   `CTRL + r` -打开一个提示符，从您的会话中搜索以前的命令*

 *## 结论

学习如何导航终端在一开始肯定是令人畏惧的，但是在花一些时间编写脚本之后，你会发现这些命令可以为你的日常工作节省大量的时间。

就我个人而言，我开始达到通过终端导航比使用 Finder 更快的程度。

如果你有任何我应该添加到这篇文章中的命令、快捷键或提示，请给我留言！很乐意补充更多。*