# 当你的调试器对你撒谎时该怎么办

> 原文：<https://dev.to/patricklafferty/what-to-do-when-your-debugger-lies-to-you-4bf0>

“我的程序不行，这是 GCC/Clang/$TOOL 的 bug 吗？”

每个人都有过这种时刻。你已经被一个问题难住了几个小时，并且*所以*确定你的工具坏了。你已经四倍检查了你的逻辑。我在$POPULAR_TOOL 中发现错误了吗？你在某个留言板上查询。然后一个头发花白的人出现了，他指出了你的逻辑中的缺陷，并评论说[这绝不是编译错误](https://blog.plover.com/prog/compiler-error.html)。一般都是这样，几乎[都是你的错](https://blog.codinghorror.com/the-first-rule-of-programming-its-always-your-fault/)。这并不是说编译器和调试器之类的没有 bug，但是在被成千上万的人使用了很多年之后，你真的不得不走寻常路去找一个。

我找到了一个。

# 人迹罕至的道路

我正在为一个 64 位的微内核写一个 32 位的加载程序。我在用 [QEMU](https://qemu.org) 模拟，用 [GDB](https://gnu.org/s/gdb) 调试。我关注的是健壮性和正确性，我希望在每个阶段都有一个完整的心理画面。所以我从一开始就调试一个简单的汇编函数，调用一个 C++函数:

```
boot:
    cli
    mov esp, stack_top
    push ebx ; grub stores an address to multiboot headers in ebx
    call startup 
```

Enter fullscreen mode Exit fullscreen mode

```
 extern "C"
void startup(uint32_t foo) {
    doStuffWithFoo(foo);
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们停止对 startup 的调用，检查一下寄存器。

[![Imgur](img/b297c45b854e28ad01bcecdae624eeca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_TlRsJ9R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/mvzcSRj.png)

EBX(但它叫 RBX...稍后提示)是 0x3087b0，我们也可以在栈顶看到它。现在让我们进入启动阶段...

[![Imgur](img/c045e9e043502934493ff20c63cc6ce3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7f14Am44--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/5kEHfUB.png)

呵...是的，嗯[我不得不继续下去，在这一点上我有点不同意你的观点](https://www.youtube.com/watch?v=J34UzHo4G5w)。

这里很不对劲，几条指令(确切地说是四条)之前我们有一个值，几条指令之后我们有一个完全不同的值。这四条指令不是罪魁祸首，它只是标准的函数序言。为什么 GDB 向我们展示 x86_64 寄存器？

# 使用(其他)力量，卢克！

由于 GDB 表现怪异，我决定尝试经典的 printf 调试。嗯，print-to-VGA-buffer-f，因为 printf 本身还没有构建。无论如何，该功能已经过测试，并被认为是正确的；无论它在屏幕上打印出什么，都是 foo 的真正价值。调查显示...

```
3087B0
```

这正是我所期待的。既然来了，我们就来问问 GDB 把 **foo** 打印到屏幕上取乐后是怎么想的吧:

[![Imgur](img/f7790787620b2b9d2e7937b0b1ccd028.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eNEXIP1w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/u2RO8te.png)

GDB，我该怎么说好呢...

[![Imgur](img/bb71e9192718810a46a4c2b07813c6ce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Xz5_8yNn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/3GmuJru.jpg)

# 揭露阴谋

我的朋友们，故事变得更糟了。通过无数分钟的谷歌搜索和#osdev 中的一些朋友，我发现 QEMU 和 GDB 已经密谋使混合模式调试不可行。版本 2.8 和 2.9 之间的一段时间 QEMU 添加了一个特定的补丁，这样无论 cpu 当前运行的是什么模式(实模式、受保护模式、长模式)，它都会通过 GDB 存根发送完整的 x86_64 寄存器文件。GDB 已被修补，启动后不会改变其寄存器文件大小。因此，当第一次将 gdb 连接到 x86_64 QEMU 实例时，GDB 看到 QEMU 发送了一个完整的 x86_64 寄存器文件，尽管 CPU 没有以长模式启动(但公平地说，它不知道它在启动时调试操作系统)。

同时，GDB 认为既然我们使用 64 位 RF，它应该使用 x86_64 位调用约定。至少这是我根据自己的观察做出的假设。System V i386 ABI 说，我们通过将参数压入堆栈来传递参数，我在调用启动函数之前就这么做了。但是 SYS-V *x86_64* ABI 说前几个参数在寄存器 rdi、rsi、rdx、rcx、r8、r9 和*中传递，然后*任何额外的参数被压入堆栈。因此，当向 GDB 索要 **foo** 的价值时，它找错了地方，因此得到了垃圾价值。

我们可以为此做个测试。在 boot.s 内部，就在 call 指令之前，我们可以将 EDI 设置为某个值，比如 0xcafebabe。猜猜 GDB 现在说 **foo** 是什么值？

[![Imgur](img/340157478ee8a3ba8da10544d8265ab9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ygn7B-75--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/grvVwCl.png)

*值*为 0，*地址*为 Cafebac6。我无法解释为什么要抵消 8，如果你能，请告诉我。棺材上的最后一颗钉子是尝试使用 qemu-system-i386 而不是 system-x86_64，而不做任何代码更改。毫不奇怪，GDB 打印出了 **foo** 的正确值。

现在需要注意的是 QEMU 仍然在正确地模拟这个程序。即使我们在 GDB 看不到它，程序也能得到正确的值并显示出来。问题只是 GDB 如何看待这个项目的状态。对于那些想调试操作系统启动代码的人来说，这只是一个问题，即使你自己做了操作系统，你也很少会这样做，而且只有当启动代码不是 64 位，而你的仿真器的 CPU 是 64 位的时候。

这影响到我们几十个人。几十个！

# 总结

当有些事情看起来不对劲，调试器似乎给了你不正确的结果时，就求助于调试工具箱中的其他工具。良好的旧的可靠的打印或日志记录，渲染一个中间或数据缓冲区的图像到屏幕上的 GPU 问题。或者创建一些测试，以一种独特的方式修改环境，证明某事是否发生。例子包括让一个线程休眠 X 秒以确定 X 是大还是小，闪烁灯，通过扬声器发出莫尔斯电码等等。

如果你有任何问题或意见，纠正或建议，批评等等，我很乐意通过电子邮件或在下面的评论中联系你。我总是乐于学习新事物，纠正不好的事情。如果你想看更多我写的文章，为什么不来看看我的博客呢。