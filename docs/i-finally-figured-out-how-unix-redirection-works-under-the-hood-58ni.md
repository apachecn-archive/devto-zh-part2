# 我终于明白了 Unix 重定向在幕后是如何工作的

> 原文：<https://dev.to/captainsafia/i-finally-figured-out-how-unix-redirection-works-under-the-hood-58ni>

在上一篇博文中，我决定研究 Unix 重定向。在文章的结尾，我对堆栈跟踪的一个方面感到困惑，并声明我将在以后研究它。

好吧，我撒谎了。

今天，我将使用不同的堆栈跟踪来探究相同的概念。正如我在以前的博客中提到的，自从我开始全职工作，我就在上班的路上每周写三次博客。通常，我会在家里的 Mac 上运行`dtruss`，复制堆栈跟踪，然后在那里进行分析。最近，我浏览了一些我收到的谷歌云电子邮件，意识到我的收件箱里有一个为期一年的谷歌云免费试用版。我用它设置了一个 Debian 虚拟机，我可以通过 SSH 连接到这个虚拟机，并使用它来发布这些博客文章。所以从现在开始，我正在查看的堆栈跟踪是在 Debian 机器上运行的。

侧边栏:许多黑客新闻的学究喜欢指出，我一直在 Mac 上阅读堆栈跟踪，而不是一个正确的*nix 系统。我知道它们是从哪里来的，但我尽量让这些博客帖子尽可能地针对特定的操作系统。我的分析通常指的是在*nix 生态系统中用于类似目的的系统调用。这一切都是想说，不要再迂腐了，让一个女孩子活下去吧。如果你真的关心我写的东西，投入你自己的时间，按照你希望的方式写你自己的博文。相信我；那样你会更开心。

无论如何，抛开愤怒的边栏，让我们回到故事的核心。在你进一步阅读之前，我推荐阅读[的最后一篇博文](https://dev.to/captainsafia/reveling-in-redirects-exploring-unix-inputoutput-redirection-389k-temp-slug-1889246)。它包含了对`fork`和`exec`系统调用的一些解释，以及进程执行的方式如何支持重定向。

你看了吗？好的。我们继续前进！

因此，和上次一样，我在一个示例命令上运行了`strace`,以便在它运行时调用系统调用。

```
execve("/bin/echo", ["echo", "New addition."], [/* 16 vars */]) = 0
brk(NULL) = 0x55c3a89fa000
access("/etc/ld.so.nohwcap", F_OK) = -1 ENOENT (No such file or directory)
mmap(NULL, 12288, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f3a4faf9000
access("/etc/ld.so.preload", R_OK) = -1 ENOENT (No such file or directory)
open("/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=13435, ...}) = 0
mmap(NULL, 13435, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f3a4faf5000
close(3) = 0
access("/etc/ld.so.nohwcap", F_OK) = -1 ENOENT (No such file or directory)
open("/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\0\4\2\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0755, st_size=1689360, ...}) = 0
mmap(NULL, 3795296, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f3a4f53a000
mprotect(0x7f3a4f6cf000, 2097152, PROT_NONE) = 0
mmap(0x7f3a4f8cf000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x195000) = 0x7f3a4f8cf000
mmap(0x7f3a4f8d5000, 14688, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f3a4f8d5000
close(3) = 0
mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f3a4faf3000
arch_prctl(ARCH_SET_FS, 0x7f3a4faf3700) = 0
mprotect(0x7f3a4f8cf000, 16384, PROT_READ) = 0
mprotect(0x55c3a7464000, 4096, PROT_READ) = 0
mprotect(0x7f3a4fafc000, 4096, PROT_READ) = 0
munmap(0x7f3a4faf5000, 13435) = 0
brk(NULL) = 0x55c3a89fa000
brk(0x55c3a8a1b000) = 0x55c3a8a1b000
open("/usr/lib/locale/locale-archive", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=1679776, ...}) = 0
mmap(NULL, 1679776, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f3a4f958000
close(3) = 0
fstat(1, {st_mode=S_IFREG|0644, st_size=0, ...}) = 0
write(1, "New addition.\n", 14) = 14
close(1) = 0
close(2) = 0
exit_group(0) = ? 
```

为了稍微解码这个堆栈跟踪，我从弄清楚从下到上发生了什么开始。

最后一个系统调用`exit_group`，负责“关闭进程中的所有线程”我想知道这在当前正在执行的命令的上下文中意味着什么。特别是，我认为如果`exit_group`负责关闭一个进程中的所有线程，那么在堆栈跟踪中应该有一些对`clone`的调用(`clone`创建一个新线程)。最终，我找到了为什么调用`exit_group`的答案，尽管看起来没有线程被创建。根据[这个](https://stackoverflow.com/questions/46903180/syscall-implementation-of-exit)，在 Linux 的最近版本中，对`exit`函数的调用是在引擎盖下隐式调用`exit_group`。似乎这样做是为了无论何时你使用一个进程，它都会关闭线程，而不是潜在地让它们打开。

它前面的两个命令非常简单明了。`close(1)`和`close(2)`负责关闭与传递的参数相关的文件描述符。正如我在上一篇博文中提到的，看起来它正在关闭与那个过程中的标准输入和输出相关联的文件描述符。

紧接在此之前的函数调用实际上打印出了我们回显到文件描述符 1 所引用的文件中的字符串。在这一点上，我很确定在堆栈跟踪的某个地方我应该看到一个打开`file.txt`的函数调用，但是我没有看到。此时，我怀疑`strace`没有调用文件输出之前发生的 fork 调用。我试图带着`-f`旗跑`strace`去追那些叉子，但是没有任何运气。

最终，我明白了是怎么回事。与上次类似，为了捕获与重定向相关的系统调用，我必须在执行命令的 shell 进程上运行`strace`。一旦我这样做了，我得到了我期望的输出。

```
open("file.txt", O_WRONLY|O_CREAT|O_TRUNC, 0666) = 3
fcntl(1, F_GETFD) = 0
fcntl(1, F_DUPFD, 10) = 10
fcntl(1, F_GETFD) = 0
fcntl(10, F_SETFD, FD_CLOEXEC) = 0
dup2(3, 1) = 1
close(3) = 0
write(1, "New addition.\n", 14) = 14
dup2(10, 1) = 1
fcntl(10, F_GETFD) = 0x1 (flags FD_CLOEXEC)
close(10) 
```

这还差不多！在这个堆栈跟踪中，我可以看到打开我们正在写的`file.txt`的`open`系统调用。这似乎是我真正想要读取的堆栈跟踪。

我会从头到尾看一遍。堆栈跟踪中的第一行打开`file.txt`文件进行写入。返回代码`3`告诉我们与这个打开的文件相关的文件描述符。

接下来的四个系统调用都调用`fcntl`命令，该命令根据其第二个参数操作文件描述符。对`fnctl`的第一次调用获取与标准输出文件描述符相关的标志。第二个函数将文件描述符 1 引用的文件复制到文件描述符 10 中。接下来的两个系统调用负责设置新文件描述符上的标志，以匹配旧文件描述符。所以本质上，这些代码所做的就是创建一个与标准输出相关的文件描述符的副本。

下一个系统调用`dup2`，是我以前没有遇到过的。查看文档，看起来这个函数将文件描述符 3 复制到文件描述符 1 中。这有效地将标准输出重新映射到`file.txt`。

之后我们把想要的文本直接写到文件描述符 1，也是`file.txt`。

最后几个函数调用撤销了标准输出文件描述符的重映射，并关闭了所有过时的文件描述符。

这就说得通多了！本质上，我们制作了一个通常与标准输出相关联的文件描述符的备份副本，并将其存储在一个新的文件描述符中。然后，我们将与标准输出相关联的文件描述符映射到我们的文件。然后在写入之后，我们撤销我们所做的重新映射。

我想我之前没有正确地调用`strace`命令，这就是为什么我得到了那些奇怪的输出。一旦我想出了正确调用`strace`以正确处理重定向的方法，我得到了更清晰的堆栈跟踪！

万岁。