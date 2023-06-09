# 窥视“残疾人”

> 原文：<https://dev.to/captainsafia/peeking-into-pwd-4236>

我的天啊。我真的能跟上这个吗？我要诚实地告诉你，公平的读者，全职工作并维护这个博客被证明是相当具有挑战性的。

但是，唉，我要坚持。

我想我可能会延续我在上一篇博文中开始的趋势，深入一些常见命令行工具的底层。今天我感兴趣的是一个极其常见的:`pwd`。

```
$ pwd
/Users/captainsafia 
```

正如我在上一篇博文中所做的，我将使用 macOS 自带的`dtrace`工具开始深入研究。它本质上是一个命令行实用程序，允许您分析程序执行过程中系统调用的跟踪。下面是我在主目录下运行`pwd`时`dtrace`程序的输出。

```
sudo dtruss /tmp/pwd
dtrace: system integrity protection is on, some features will not be available

SYSCALL(args) = return
/Users/captainsafia
open("/dev/dtracehelper\0", 0x2, 0xFFFFFFFFE2CBAB20) = 3 0
ioctl(0x3, 0x80086804, 0x7FFEE2CBAA80) = 0 0
close(0x3) = 0 0
access("/AppleInternal/XBS/.isChrooted\0", 0x0, 0x0) = -1 Err#2
thread_selfid(0x0, 0x0, 0x0) = 6082815 0
bsdthread_register(0x7FFF56790BEC, 0x7FFF56790BDC, 0x2000) = 1073742047 0
issetugid(0x0, 0x0, 0x0) = 0 0
mprotect(0x10CF4D000, 0x1000, 0x0) = 0 0
mprotect(0x10CF52000, 0x1000, 0x0) = 0 0
mprotect(0x10CF53000, 0x1000, 0x0) = 0 0
mprotect(0x10CF58000, 0x1000, 0x0) = 0 0
mprotect(0x10CF4B000, 0x88, 0x1) = 0 0
mprotect(0x10CF59000, 0x1000, 0x1) = 0 0
mprotect(0x10CF4B000, 0x88, 0x3) = 0 0
mprotect(0x10CF4B000, 0x88, 0x1) = 0 0
getpid(0x0, 0x0, 0x0) = 8043 0
stat64("/AppleInternal/XBS/.isChrooted\0", 0x7FFEE2CBA158, 0x0) = -1 Err#2
stat64("/AppleInternal\0", 0x7FFEE2CBA1F0, 0x0) = -1 Err#2
csops(0x1F6B, 0x7, 0x7FFEE2CB9C90) = 0 0
dtrace: error on enabled probe ID 2190 (ID 557: syscall::sysctl:return): invalid kernel access in action #10 at DIF offset 28
csops(0x1F6B, 0x7, 0x7FFEE2CB9580) = 0 0
stat64("/Users/captainsafia\0", 0x7FFEE2CBB8B0, 0x0) = 0 0
stat64(".\0", 0x7FFEE2CBB940, 0x0) = 0 0
getrlimit(0x1008, 0x7FFEE2CBB6D0, 0x0) = 0 0
fstat64(0x1, 0x7FFEE2CBB6E8, 0x0) = 0 0
ioctl(0x1, 0x4004667A, 0x7FFEE2CBB734) = 0 0
dtrace: error on enabled probe ID 2165 (ID 947: syscall::write_nocancel:return): invalid kernel access in action #12 at DIF offset 68 
```

哦天啊！这里发生了很多事情。但是我们可以排除大部分可能性，因为它在上一篇博客文章中被探究过。堆栈跟踪中有几行引起了我的兴趣。第一行是这一行。

`getrlimit(0x1008, 0x7FFEE2CBB6D0, 0x0) = 0 0`

`getrlimit`是做什么的？我在互联网上做了一些调查，发现[负责获取资源的资源限制。在这种情况下，第一个参数(`0x1008`)是对我们试图修改限制的资源的引用。我想知道这个数字指的是什么资源。这将大大有助于我理解为什么在这段代码中调用这个函数。我最终在 Python 文档中找到了一些有用的东西。Python 有一个内置的`resource`模块，可以用来与系统上的资源接口。文档中有一段有趣的引用。](http://man7.org/linux/man-pages/man2/getrlimit.2.html)

> getrlimit(2)的 Unix 手册页列出了可用的资源。请注意，并非所有系统都使用相同的符号或相同的值来表示相同的资源。本模块并不试图掩盖平台差异—没有为平台定义的符号在该平台上的本模块中不可用。

所以看起来每个操作系统对第一个参数使用了不同的值。这使得很难弄清楚`0x1008`在 macOS 环境中的确切含义，因为许多文档都是围绕 Unix 的。也就是说，文档确实让我很好地了解了资源可能引用的内容。资源类似于堆中进程可用的字节数或进程可以使用的最大 CPU 时间。

我好奇的另一件事是这个`stat64`系统调用。

```
stat64(".\0", 0x7FFEE2CBB940, 0x0) = 0 0 
```

我认为这一行是关于分析`pwd`如何工作的最重要的一行，因为传递给它的参数是“.”参数，该参数通常用作对当前工作目录的引用。

侧栏:`\0`是空终止符，表示字符串已经结束。

如前一篇博文所述，大多数其他系统调用都与`dtrace`工具的实际操作有关，与`pwd`的功能无关。

那是一次有趣的探索！最吸引人的是我对操作系统中的资源限制所做的一些窥探。它向我展示了操作系统在底层不同的另一种方式:对资源使用不同的引用代码。当然，这种微小的差异使得使用低级编程语言构建跨平台的应用程序变得困难。

下次见！