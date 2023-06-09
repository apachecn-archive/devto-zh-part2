# 沉迷于重定向:探索 Unix 输入/输出重定向

> 原文：<https://dev.to/captainsafia/reveling-in-redirects-exploring-unix-inputoutput-redirection-1hfc>

我最喜欢的 Linux 特性之一是重定向。重定向使您能够将一个命令的输出直接发送给另一个命令。例如，下面是我如何使用管道重定向将一个文件的内容复制到我的计算机的剪贴板中。

```
$ cat file.txt | pbcopy 
```

我还可以使用重定向向文件中添加一些文本。

```
$ echo "New stuff." > file.txt 
```

所有这些都让我好奇:重定向是如何工作的？从我的大学计算机科学课程中，我找到了这个问题的部分答案。它与 Unix 执行进程的方式有关。在这种情况下，将使用两个系统调用:`fork`和`exec`。

`fork`是一个命令，复制当前运行过程的所有细节。进程是代表当前正在运行的程序的实体。在 Linux 中，一个进程的细节被存储在`task_struct`中，你可以在 Linux 代码库中通读这个文件中`task_struct`的定义。当一个流程被分叉时，与该流程相关联的`task_struct`会被复制。如果通读 struct 定义，您会注意到它存储了诸如进程的`state`或进程的`pid`之类的东西。

`exec`是一个命令，用与程序相关的细节覆盖当前运行过程的属性(如`ls`或`cat`程序)。通常情况下，系统调用`fork`会创建当前运行进程的新副本。这个副本现在是当前正在运行的新进程。然后进行`exec`调用，一个新程序被加载到该进程中。

创建一个新进程和开始执行一个程序是不同的步骤，这一事实允许我们在`fork`和`exec`步骤之间做一些有趣的事情。例如，实现从一个`echo`命令到一个文件的重定向，如上面的例子所示。

1.  打开文件描述符为 1 的文件`file.txt`。
2.  分叉当前正在运行的进程。
3.  执行命令`echo "New stuff.`

这里的关键是我们打开了文件`file.txt`，文件描述符设置为 1。文件描述符是表示任何输入/输出资源(如文件)应该如何被访问的一种抽象方式。通常，所有进程都可以访问编号为 0、1 和 2 的三个文件描述符。这些数字似乎是任意的(这是因为它们确实是)。引用它们的更好的方法是通过它们被赋予的常量，分别是`STDIN_FILENO`、`STDOUT_FILENO`和`STDERR_FILENO`。

所以本质上，在上面的步骤中，我们将与标准输出相关联的文件描述符映射到一个文件(而不是控制台上的输出)。当我们分叉流程时，这个文件描述符映射也被复制，并在我们执行`echo`命令时使用。

好的。既然我已经解释了所有这些，我想试着实际看看它的运行情况。为了做到这一点，我使用了好的 ol' `dtruss`工具，试图找出命令`echo "New text." > file.txt`的堆栈跟踪可以告诉我什么。

侧边栏:因为我试图找到包含重定向的命令的堆栈跟踪，所以我不能像往常一样运行`dtruss <command here>`。相反，我最终将`dtruss`连接到一个 bash 会话，并查看与该会话中执行的命令相关的堆栈跟踪。下面，我提取了我认为与重定向相关的系统调用。

```
29949/0x6d9b91: fork() = 0 0
29949/0x6d9b91: thread_selfid(0x0, 0x0, 0x0) = 7183249 0
29949/0x6d9b91: issetugid(0x0, 0x0, 0x0) = 0 0
29949/0x6d9b91: csrctl(0x0, 0x7FFEE483CF7C, 0x4) = -1 Err#1
29949/0x6d9b91: csops(0x0, 0x0, 0x7FFEE483D890) = 0 0
29949/0x6d9b91: shared_region_check_np(0x7FFEE483CDD8, 0x0, 0x0) = 0 0
29949/0x6d9b91: stat64("/private/var/db/dyld/dyld_shared_cache_x86_64h\0", 0x7FFEE483CD20, 0x0) = 0 0
29949/0x6d9b91: csrctl(0x0, 0x7FFEE483CADC, 0x4) = -1 Err#1
29949/0x6d9b91: getpid(0x0, 0x0, 0x0) = 29949 0
29949/0x6d9b91: proc_info(0x2, 0x74FD, 0x16) = 1272 0
29949/0x6d9b91: ioctl(0x3, 0x80086804, 0x7FFEE483CE50) = 0 0
29949/0x6d9b91: close(0x3) = 0 0
29949/0x6d9b91: access("/AppleInternal/XBS/.isChrooted\0", 0x0, 0x0) = -1 Err#2
29949/0x6d9b91: thread_selfid(0x0, 0x0, 0x0) = 7183249 0
29949/0x6d9b91: bsdthread_register(0x7FFF56790BEC, 0x7FFF56790BDC, 0x2000) = 1073742047 0
29949/0x6d9b91: issetugid(0x0, 0x0, 0x0) = 0 0
29949/0x6d9b91: mprotect(0x10B3CB000, 0x1000, 0x0) = 0 0
29949/0x6d9b91: mprotect(0x10B3D0000, 0x1000, 0x0) = 0 0
29949/0x6d9b91: mprotect(0x10B3D1000, 0x1000, 0x0) = 0 0
29949/0x6d9b91: mprotect(0x10B3D6000, 0x1000, 0x0) = 0 0
29949/0x6d9b91: mprotect(0x10B3C9000, 0x88, 0x1) = 0 0
29949/0x6d9b91: mprotect(0x10B3D7000, 0x1000, 0x1) = 0 0
29949/0x6d9b91: mprotect(0x10B3C9000, 0x88, 0x3) = 0 0
29949/0x6d9b91: mprotect(0x10B3C9000, 0x88, 0x1) = 0 0
29949/0x6d9b91: getpid(0x0, 0x0, 0x0) = 29949 0
29949/0x6d9b91: stat64("/AppleInternal/XBS/.isChrooted\0", 0x7FFEE483C528, 0x0) = -1 Err#2
29949/0x6d9b91: stat64("/AppleInternal\0", 0x7FFEE483C5C0, 0x0) = -1 Err#2
29949/0x6d9b91: csops(0x74FD, 0x7, 0x7FFEE483C060) = 0 0
dtrace: error on enabled probe ID 2190 (ID 557: syscall::sysctl:return): invalid kernel access in action #11 at DIF offset 28
29949/0x6d9b91: csops(0x74FD, 0x7, 0x7FFEE483B950) = 0 0
29949/0x6d9b91: writev(0x1, 0x7FE5BE601210, 0x2) = 13 0 
```

所以第一个系统调用就是我之前讨论过的`fork`系统调用。根据我的知识，接下来的几个系统调用应该与将标准输出文件描述符映射到`file.txt`文件有关。我试图找出可能与此相关的系统调用，并分离出以下内容。

```
29949/0x6d9b91: ioctl(0x3, 0x80086804, 0x7FFEE483CE50) = 0 0
29949/0x6d9b91: close(0x3) = 0 0 
```

正如在以前的博客文章中所讨论的，系统调用与管理文件等 it I/O 资源的交互有关。传递给这个函数的第一个参数是相关的文件描述符。这种情况下就是`0x3`。我认为这是与打开`file.txt`文件相关的调用，因为前 0-2 个文件描述符由操作系统使用，如上所述。

另一个重要的函数调用在底部。

```
29949/0x6d9b91: writev(0x1, 0x7FE5BE601210, 0x2) = 13 0 
```

我以前从未遇到过这个`writev`系统调用，所以我决定进一步研究它。当我阅读[文档](https://linux.die.net/man/2/writev)时，我为系统调用找到的最容易理解的定义如下。

> writev()系统调用的工作方式与 write(2)类似，只是写出了多个缓冲区。

这是我的直觉，但我对文件描述符不匹配的事实感到困惑。如果我们在文件描述符`0x3`打开文件并写入文件描述符`0x1`，那么实际的重定向发生在哪里？我想知道传递给`ioctl`的请求代码是否连接了两个文件描述符。或者可能有一些代码负责执行重定向，但不会出现在系统调用中？

无论如何，这篇博文有点长了，我的上班之旅也快结束了(我在上班的路上写了这些博文)，所以我必须在另一篇博文中继续这个特别的调查。

到时候见！希望那时我会对整个事件有更清晰的了解。