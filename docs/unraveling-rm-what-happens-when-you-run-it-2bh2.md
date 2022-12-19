# 解开“rm ”:当你运行它时会发生什么？

> 原文：<https://dev.to/captainsafia/unraveling-rm-what-happens-when-you-run-it-2bh2>

又是一个周一，又是一篇博文！

在过去的几篇博客文章中，我一直在钻研 curl 代码库，但是今天有一些别的东西引起了我的兴趣，所以我想我还不如趁着好奇心还在的时候深入研究一下。

老实说，我不愿意深入讨论这个问题，因为上一次我写了一篇关于 Unix 相关主题的博文，让我们称他们为一群有太多空闲时间和太多琐碎事情的人，以一种不太好的方式对博文中的一些内容进行了批评。我说的“不太好”是指一个种族主义者和性别歧视者。无论如何，我认为总会有讨厌的人(以及对操作系统有不健康依恋并在互联网上骚扰陌生人的人)，所以我还是继续下去吧。

好的。说够了。我一直在解决 [Zarf](https://zarf.co/) 应用积压的问题。因此，我花了很多时间在命令行上。积压涉及删除大量代码(在此插入满意的叹息)，有时这涉及删除整个源代码文件(在此插入双重满意的叹息)。这让我想知道:当你在命令行上运行`rm`时会发生什么。我经常运行的`rm`命令有几种变体。

```
$ rm settings.json
$ rm -rf config/ 
```

无论如何，我想深入了解一下`rm`到底发生了什么，所以我决定从确定由`rm`命令调用的系统调用开始。

```
captainsafia@eniac ~/zarf> sudo dtruss /tmp/rm History.md
dtrace: system integrity protection is on, some features will not be available

SYSCALL(args) = return open("/dev/dtracehelper\0", 0x2, 0xFFFFFFFFE9A3EB10) = 3 0
ioctl(0x3, 0x80086804, 0x7FFEE9A3EA70) = 0 0
close(0x3) = 0 0
access("/AppleInternal/XBS/.isChrooted\0", 0x0, 0x0) = -1 Err#2
thread_selfid(0x0, 0x0, 0x0) = 3765033 0
bsdthread_register(0x7FFF56790BEC, 0x7FFF56790BDC, 0x2000) = 1073742047 0
issetugid(0x0, 0x0, 0x0) = 0 0
mprotect(0x1061CA000, 0x1000, 0x0) = 0 0
mprotect(0x1061CF000, 0x1000, 0x0) = 0 0
mprotect(0x1061D0000, 0x1000, 0x0) = 0 0
mprotect(0x1061D5000, 0x1000, 0x0) = 0 0
mprotect(0x1061C8000, 0x88, 0x1) = 0 0
mprotect(0x1061D6000, 0x1000, 0x1) = 0 0
mprotect(0x1061C8000, 0x88, 0x3) = 0 0
mprotect(0x1061C8000, 0x88, 0x1) = 0 0
getpid(0x0, 0x0, 0x0) = 77384 0
stat64("/AppleInternal/XBS/.isChrooted\0", 0x7FFEE9A3E148, 0x0) = -1 Err#2
stat64("/AppleInternal\0", 0x7FFEE9A3E1E0, 0x0) = -1 Err#2
csops(0x12E48, 0x7, 0x7FFEE9A3DC80) = 0 0
dtrace: error on enabled probe ID 2190 (ID 557: syscall::sysctl:return): invalid kernel access in action #10 at DIF offset 28
csops(0x12E48, 0x7, 0x7FFEE9A3D570) = 0 0
geteuid(0x0, 0x0, 0x0) = 0 0
ioctl(0x0, 0x4004667A, 0x7FFEE9A3F954) = 0 0
lstat64("History.md\0", 0x7FFEE9A3F8F8, 0x0) = 0 0
access("History.md\0", 0x2, 0x0) = 0 0
unlink("History.md\0", 0x0, 0x0) = 0 0 
```

侧栏:通常，您可以通过使用`strace`来确定一个命令所使用的系统调用。我在 Mac 上，所以`strace`不可用。相反，我使用了一个叫做`dtrace`的工具。为了允许它处理`rm`命令，我必须将可执行文件复制到一个临时目录中并执行它。总而言之，这就是为什么我要执行上面的`dtrace /tmp/rm`而不是`strace rm`。

所以，无论如何，让我们看看上面是怎么回事。跟踪中的前几行似乎非常清楚地与设置命令的`sudo`部分相关。我对调用`mprotect`命令很感兴趣。我猜想这可能与内存地址有关，因为传递给`mprotect`函数的第一个参数看起来像内存地址。我决定去 Google 看看是否能找到这个函数的文档并确认这一点。我在这里找到了文档，我的怀疑得到了证实。该函数负责设置调用进程对内存的访问权限。函数声明看起来像这样`int mprotect(void *addr, size_t len, int prot)`，其中`addr`是内存范围的开始，`len`是将要改变的内存地址范围的长度，`prot`是一个整数，表示内存应该如何被保护。我研究了传递给上面的`mprotect`函数调用的`prot`的不同值，得出了以下结论。

1.  `0x0`是`PROT_NONE`的代码，表示存储器不能被写入或读取。
2.  `0x1`是表示可以读取存储器的`PROT_READ`代码。
3.  `0x3`是`PROT_READ`和`PROT_WRITE`的值的按位“或”,这意味着它允许内存被读取或写入。

我不确定在`mprotect`调用中引用的内存地址实际上对应的是什么，或者确定它的最佳方式是什么。

`getpid`命令有一个不言自明的名字，但是我想知道传递给该函数的参数是什么。事实证明，`getpid`函数应该不带参数，所以在上面的调用中包含参数让我很困惑。

我也不确定`csops`和`ioctl`调用做了什么。事实证明，这实际上是很有道理的。一些调查[显示](https://github.com/axelexic/CSOps)`csops`是苹果操作系统特有的系统调用，可以用来检查操作系统写入内存页面的签名。这似乎是检查特定代码块有效性的某种方式。我对此不太确定，也没有太多关于苹果特定系统调用的信息，所以我不能像我希望的那样深入了解细节。

syscall 是一个非常通用的调用，负责所有与输入/输出相关的交互。根据[的联机帮助页](http://man7.org/linux/man-pages/man2/ioctl.2.html)，第一个参数代表一个文件描述符，第二个是一个“依赖于设备的请求代码”的引用，第三个是一个指向内存的指针。我研究了一下请求代码“0x4004667A ”,发现它通常与调用`dtrace`命令有关，所以我认为这个调用与删除引用文件的任务无关。

我感兴趣的是这个脚本中最后调用的 syscall，`unlink`。我回到谷歌了解更多相关信息，并看到了[这个联机帮助页](http://man7.org/linux/man-pages/man2/unlink.2.html)。`unlink`命令是真正负责删除文件的命令。

总而言之，痕迹中与`rm`最相关的部分都在最后几行。其他一切似乎都是与设置内存权限、确保相关文件的状态以及设置`dtrace`命令相关的设置。

原来如此！我承认，我不确定我对这种反拓扑学的追踪方法有什么感觉。这有点太直接了，我几乎喜欢沉迷于代码，并最终陷入其中的复杂性。它有治疗作用。我会看看在以后的代码阅读中，我是否对这种技术更加熟悉。在那之前，下次见！