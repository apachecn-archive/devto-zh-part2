# 在命令行上运行“cp”会发生什么？

> 原文：<https://dev.to/captainsafia/what-happens-when-you-run-cp-on-the-command-line-7ae>

不管这是什么，是时候再版了！在我深入 Git 代码库之后，我再也没有精力为这些代码人类学时刻编造有趣的名字了。

在这篇博文中，我正在考虑深入研究`cp`命令是如何工作的。如果你是个老读者，你可能知道我通常在 Mac 上阅读代码。自从我周一开始全职工作以来，我一直在上下班的路上用 Chromebook 写这些博客(我在上下班途中还会做其他各种事情，比如盯着交通，承认希望永远不必驾驶机动车是多么美好)。因为我在使用 Chromebook，所以出门之前，我通常会在 Mac 上运行`dtruss`命令，将堆栈跟踪的输出复制并粘贴到支持云的文本编辑器上，然后在上班的路上通读并研究堆栈跟踪的部分内容。

侧边栏:我想在某个时候，我会用这些咔嚓咔嚓的声音来激怒这个 rideshare 上的某人！

这就是说，由于我在新的限制下写作，这些博客文章可能会比平时更短更跳跃。姑且称之为极限写作吧。呵。明白了吗？比如极限编程？好的。我会继续白天的工作。

说了这么多，是时候了解这篇文章的真正目的了。运行`cp`命令会发生什么？`cp`命令通常用于复制目录和文件。但是在引擎盖下发生了什么呢？

```
$ sudo dtruss cp file.txt file-2.txt

SYSCALL(args) = return open("/dev/dtracehelper\0", 0x2, 0xFFFFFFFFE1B5FB00) = 3 0
ioctl(0x3, 0x80086804, 0x7FFEE1B5FA60) = 0 0
close(0x3) = 0 0
access("/AppleInternal/XBS/.isChrooted\0", 0x0, 0x0) = -1 Err#2
thread_selfid(0x0, 0x0, 0x0) = 6158775 0
bsdthread_register(0x7FFF56790BEC, 0x7FFF56790BDC, 0x2000) = 1073742047 0
issetugid(0x0, 0x0, 0x0) = 0 0
mprotect(0x10E1AB000, 0x1000, 0x0) = 0 0
mprotect(0x10E1B0000, 0x1000, 0x0) = 0 0
mprotect(0x10E1B1000, 0x1000, 0x0) = 0 0
mprotect(0x10E1B6000, 0x1000, 0x0) = 0 0
mprotect(0x10E1A9000, 0x88, 0x1) = 0 0
mprotect(0x10E1B7000, 0x1000, 0x1) = 0 0
mprotect(0x10E1A9000, 0x88, 0x3) = 0 0
mprotect(0x10E1A9000, 0x88, 0x1) = 0 0
getpid(0x0, 0x0, 0x0) = 8855 0
stat64("/AppleInternal/XBS/.isChrooted\0", 0x7FFEE1B5F138, 0x0) = -1 Err#2
stat64("/AppleInternal\0", 0x7FFEE1B5F1D0, 0x0) = -1 Err#2
csops(0x2297, 0x7, 0x7FFEE1B5EC70) = 0 0
dtrace: error on enabled probe ID 2190 (ID 557: syscall::sysctl:return): invalid kernel access in action #10 at DIF offset 28
csops(0x2297, 0x7, 0x7FFEE1B5E560) = 0 0
sigaction(0x1D, 0x7FFEE1B602E8, 0x7FFEE1B60310) = 0 0
stat64("file-2.txt\0", 0x7FFEE1B607D8, 0x0) = -1 Err#2
lstat64("file.txt\0", 0x7FFEE1B60868, 0x0) = 0 0
umask(0x1FF, 0x0, 0x0) = 18 0
umask(0x12, 0x0, 0x0) = 511 0
fstatat64(0xFFFFFFFFFFFFFFFE, 0x7FFB66D001C8, 0x7FFB66D001D8) = 0 0
stat64("file-2.txt\0", 0x7FFEE1B608F8, 0x0) = -1 Err#2
open("file.txt\0", 0x0, 0x0) = 3 0
open("file-2.txt\0", 0x601, 0x81A4) = 4 0
fstatfs64(0x4, 0x7FFEE1B5FA88, 0x0) = 0 0
fstat64(0x4, 0x7FFEE1B5FA88, 0x0) = 0 0
fchmod(0x4, 0x8180, 0x0) = 0 0
dtrace: error on enabled probe ID 2166 (ID 159: syscall::read:return): invalid kernel access in action #12 at DIF offset 68
dtrace: error on enabled probe ID 2164 (ID 161: syscall::write:return): invalid kernel access in action #12 at DIF offset 68
dtrace: error on enabled probe ID 2166 (ID 159: syscall::read:return): invalid kernel access in action #12 at DIF offset 68
fchmod(0x4, 0x81A4, 0x0) = 0 0
fstat64_extended(0x3, 0x7FFB66D00288, 0x7FFB66D003C0) = 0 0
fstat64(0x4, 0x7FFEE1B5F950, 0x0) = 0 0
fchmod(0x4, 0x1A4, 0x0) = 0 0
__mac_syscall(0x7FFF564ACD02, 0x52, 0x7FFEE1B5F8D0) = -1 Err#93
flistxattr(0x4, 0x0, 0x0) = 0 0
flistxattr(0x3, 0x0, 0x0) = 0 0
fchmod(0x4, 0x1A4, 0x0) = 0 0
close(0x3) = 0 0
close(0x4) = 0 0 
```

首先，我做栈跟踪已经有一段时间了，我对这一点很好奇。

```
syscall::read:return): invalid kernel access in action #12 at DIF offset 68 
```

这似乎可能与一个试图访问它无权访问的内存块的进程有关。我决定去谷歌一下，看看互联网对此会有什么说法。看了几个论坛帖子，苹果支持帖子，博客帖子，栈交换帖子，看来这个错误和苹果的系统完整性保护有关。这是 El Captain 中发布的一个特性，它限制了 root 用户对操作系统受保护部分的访问权限和操作权限。你可以在这里阅读更多相关信息[。这实际上是一个非常好的概念。](https://support.apple.com/en-us/HT204899)

不可否认，我很轻松地浏览了这个堆栈跟踪，并弄清楚发生了什么。使用的大多数命令都是我已经熟悉的，通常很容易绘制出程序的流程。

有两个部分引起了我的注意。这:

```
__mac_syscall(0x7FFF564ACD02, 0x52, 0x7FFEE1B5F8D0) = -1 Err#93 
```

还有这个:

```
flistxattr(0x4, 0x0, 0x0) = 0 0
flistxattr(0x3, 0x0, 0x0) = 0 0 
```

我认为从研究第二个系统调用开始会很容易，因为`__mac_syscall`位对我来说是完全不透明的。我开始研究`flistxattr`命令，这里的“研究”指的是“谷歌”。我在这里找到了一些关于那个[的有用文档。所以看起来这个命令添加了一组新的属性来表示每个文件。这个函数的函数声明是这样的。](http://man7.org/linux/man-pages/man2/listxattr.2.html) 

```
ssize_t flistxattr(int fd, char *list, size_t size); 
```

所以从上面的跟踪来看，看起来两个系统调用之间不同的 pare 参数是`fd`，它是对文件描述符的引用。在这种情况下，我假设它在原始文件和副本上设置了`name:value`属性。

现在到了有趣的一点。我发现有趣的是这个函数调用的结果导致了一个错误。为了调查这个电话是做什么的，我做了一些谷歌搜索。在阅读了一篇博客文章、几篇 StackOverflow 文章和一篇论文之后，我最终在 Darwin 代码库中找到了这个函数的实现。你可以在这里阅读。我发现的代码片段中最重要的部分是对它的注释。

```
/*
 * __mac_syscall: Perform a MAC policy system call
 *
 * Parameters: p Process calling this routine
 * uap User argument descriptor (see below)
 * retv (Unused)
 *
 * Indirect: uap->policy Name of target MAC policy
 * uap->call MAC policy-specific system call to perform
 * uap->arg MAC policy-specific system call arguments
 *                
 * Returns: 0 Success
 * !0 Not success
 *
 */
int
__mac_syscall(proc_t p, struct__mac_syscall_args *uap, int *retv __unused) 
```

我做了一些研究来弄清楚什么是 MAC 策略，并发现它们是与安全相关的构造。你可以在这里阅读更多关于他们的信息。在阅读了引用的链接后，上面的系统调用更有意义了。有了这一点，堆栈跟踪也更加清晰了。

直到下一个帖子！