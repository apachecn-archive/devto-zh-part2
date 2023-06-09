# 基于 x86-TSO 内存模型的 Linux 内核自旋锁的 SyncStitch 安全性验证

> 原文：<https://dev.to/hatsugai/safety-verification-of-a-spin-lock-used-in-linux-kernel-based-on-x86-tso-memory-model-by-using-syncstitch-4d56>

# 记忆模式

内存模型描述了线程通过内存的交互以及它们对数据的共享使用。它通常在编程语言规范或 CPU 手册中定义。

# x86-TSO

当您在 x86 处理器上编写多线程程序时，您应该假设 x86 手册中描述的内存模型，而不应该基于您使用的 CPU 的实际行为，因为它因 CPU 模型而异，将来可能会发生变化。

然而，不幸的是，记忆模型是用自然语言描述的，它经常是模糊的，甚至是不一致的。

为了解决这个问题，研究人员建立了一个程序员模型。有一篇论文标题为:

x86-TSO:面向 x86 多处理器的严谨且可用的程序员模型

在本文中，记忆模型用数学语言严格地形式化了。

我用一个叫做 [SyncStitch](http://www.principia-m.com/syncstitch/index.html) 的模型检查器(更严格地说，是一个精化检查器)通过建模(建模一个模型！)中的存储器模型，并将其与执行一系列读/写指令的线程模型组合在一起。

x86-TSO 存储器模型由存储器、用于锁前缀的锁状态、以及对于每个处理器的一组寄存器和写入缓冲器(其元素是一对地址和要写入的值的队列)组成。

[![x86blockdiagram](img/186d697f2ed10b000186bbe9bbc5d949.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WtHxQE0Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r135uc0bcwth6bu69x69.png)

这个模型翻译成 SyncStitch 模型是这样的(只是一个草图):

[![SyncStitch model](img/71c21d6e0a72a3f2c3f231665d16f824.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YUmG-4VI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/go7fmgfu0ggs51fcxk40.png)

一旦您对 CPU 建模，您就可以像这样调查代码的所有可能行为:

(来自报纸)

[![n6](img/7b1d022d2f082ce5f2ba3058797e049d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--p6O-ZBRm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hfbtsov3f0jm5lkl7gh9.png)

或者这个:

(来自报纸)

[![n5](img/32d08ca5f53faa19740308424b5bc66a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---zFwX0Yw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/plp6gxienn3msterw4xc.png)

结果是这样显示的。

[![assertion1](img/0aac903e19db3e706494a8a9a7a394f7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JCD_MDdo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xdnm1o4seev9gzzltv3g.png)

当您找到一个允许的最终状态时，您可以通过查看内存、寄存器和写缓冲区的值来查询从初始状态到最终状态的详细路径，这在实际的 CPU 上是无法观察到的。

[![process explorer](img/79da3ad7cea0b575a74331835dc860ab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--f6UmK1mY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wirhvwnlifog7ledzh39.png)

# Linux 内核中的自旋锁

根据该论文，存在关于在 Linux 内核中实现自旋锁代码的正确性和安全性的讨论。代码如下:

[![spin lock](img/f84a1a419bb64f12a8c39079cb1c7c97.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xQErQDXm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5gj432mixmaiz9ug7i8l.png)

我用 x86-TSO 模型在 SyncStitch 上建模了这段代码，还为它建立了一个安全规范。然后检查工具的安全性。

[![assertion2](img/321630d6f262ac7ec035bd92f5e3d590.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jP8RgMnV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yksw18g7on3my7zd7lg5.png)

我成功地确认了安全。这是精化检查器可以做的事情(其中之一)。

因为我这次构建的模型支持锁前缀和 MFENCE 指令，所以您可以研究面向多线程的广泛类型的 x86 指令序列。我猜在操作系统和/或嵌入式系统上工作的程序员对这个模型和工具感兴趣。