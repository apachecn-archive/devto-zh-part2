# “cp”和“mv”在引擎盖下做什么？

> 原文：<https://dev.to/captainsafia/what-do-cp-and-mv-do-under-the-hood-3k2a>

又是一个星期五，又是一篇博文！你能相信我在过去的四个月里写了大约 50 篇博文吗？我当然不能！我觉得每一次都变得更容易，所以那句谚语是对的:熟能生巧。

今天，我想让另一个无助的 Unix 命令成为我的受害者。嗯，实际上是两个，我最近对`mv`命令和`copy`命令之间的区别很感兴趣。我有一种模糊的预感，他们实际上大多做着同样的事情，但这个博客不是关于未经证实的预感。让我们看看是否能从堆栈跟踪中看出差异。

我在下面的命令上运行了`strace`。`mv test-1.txt test-2.txt`和`cp test-3.txt test-4.txt`。

`mv`命令的堆栈跟踪实际上变得更短，也更容易处理。前几行负责检查是否已经存在一个文件，其文件名是我们想要`mv`到的。

我不确定`stat`和`lstat`之间有什么区别。我知道那是什么，但那是很多个月前的事了，我需要唤起我的记忆。事实证明，除了 lstat 不在符号链接指向的原始`inode`上运行`stat`，而是在符号链接本身上运行之外，它们本质上做的是相同的事情(返回一个 inode 的属性)。这是一种获取引用而不是实际文件的统计数据的方式。

最后一次调用是对负责更改文件名称或位置的`rename`系统调用( [documentation](http://man7.org/linux/man-pages/man2/rename.2.html) )在这里完成了大部分繁重的工作。

```
stat("test-2.txt", 0x7ffd03574f90) = -1 ENOENT (No such file or directory)
lstat("test-1.txt", {st_mode=S_IFREG|0644, st_size=13, ...}) = 0
lstat("test-2.txt", 0x7ffd03574c70) = -1 ENOENT (No such file or directory)
rename("test-1.txt", "test-2.txt") = 0 
```

命令有更多的功能。事实证明，他们完全不同。我不知道为什么我认为他们会很相似。事后看来，那没有意义。我想这就是你在没有喝咖啡或茶的情况下，和一群陌生人一起以每小时 50 英里的速度写博客时所做的假设。

`cp`命令的堆栈跟踪从在`mv`命令中使用的相同的`stat`命令开始。

然后打开两个文件，现有文件用于读取，新文件用于写入。我发现引用她的`fstat`调用很有趣。我脑海深处又一次模糊地回忆起`fstat`做了什么。在[查阅文档](https://linux.die.net/man/2/fstat)之后，我意识到查看系统调用中使用的参数实际上已经揭示了它的用途。参数`3`和`4`是对被打开进行读写的文件的文件描述符的引用。因此，`fstat`用于在写入这些文件之前获取它们的文件属性。

之后有一个看似随机的对系统调用`fadvise64`的调用。我从来没有遇到过这个命令，在[查找了](https://linux.die.net/man/2/fadvise64)之后，我意识到它非常漂亮。显然，这就是程序如何通知操作系统其访问特定文件的意图。这样，操作系统可以根据这些意图进行适当的优化。例如，如果计划复制数据，操作系统可能会将复制的数据和复制的位置放在彼此更靠近的位置。

之后，从旧文件中读取数据并写入新文件。这里没有太多有趣的东西，我认为系统调用是不言自明的。

```
stat("test-4.txt", 0x7ffd15bf1830) = -1 ENOENT (No such file or directory)
stat("test-3.txt", {st_mode=S_IFREG|0644, st_size=13, ...}) = 0
stat("test-4.txt", 0x7ffd15bf15a0) = -1 ENOENT (No such file or directory)
open("test-3.txt", O_RDONLY) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=13, ...}) = 0
open("test-4.txt", O_WRONLY|O_CREAT|O_EXCL, 0644) = 4
fstat(4, {st_mode=S_IFREG|0644, st_size=0, ...}) = 0
fadvise64(3, 0, 0, POSIX_FADV_SEQUENTIAL) = 0
mmap(NULL, 139264, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f875a6b600
0
read(3, "Lorem ipsum.\n", 131072) = 13
write(4, "Lorem ipsum.\n", 13) = 13
read(3, "", 131072) = 0
close(4) = 0
close(3) = 0 
```

下一篇博文再见！