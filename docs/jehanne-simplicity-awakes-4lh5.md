# 珍妮:简单觉醒了

> 原文：<https://dev.to/shamar/jehanne-simplicity-awakes-4lh5>

抢占式多任务处理已经有 50 多年的历史了，最初是在 1964 年在 PDP-6 Monitor 和 MULTICS 中引入的。他们注册了[定时器中断处理程序](https://en.wikipedia.org/wiki/Interrupt)来将控制转移回内核代码，而不是依赖进程来协作地将 CPU 释放给内核。

尽管如此，通过调用任何一个[系统调用](https://en.wikipedia.org/wiki/System_call)，进程仍然能够在[时间片](https://en.wikipedia.org/wiki/Preemption_(computing)#Time_slice)内将 CPU 的控制权让给内核。

系统调用通常被分为阻塞或非阻塞，这取决于内核在下一个时钟周期之前将控制权返回给调用进程代码的概率。

在现实世界中，非阻塞的系统调用可能会阻塞进程，而阻塞的系统调用可能根本不会阻塞:这完全取决于实现细节和运行时的系统状态。

阻塞系统调用的经典例子有

*   这应该会阻塞一定的秒数
*   这可能会阻塞，直到子进程退出
*   这将一直阻塞，直到有一大块数据可用
*   那会阻塞管道，没有足够的空间容纳新数据

## CPU 控制和时间

Sleep 是一个非常有趣的系统调用，因为它的全部目的只是暂时让出 CPU，以便其他进程可以使用它。它的主要用例是轮询某个资源变得可用，而不减少可以用来产生这种资源的计算能力:它是支持抢占式多任务处理的操作系统中的一个协作多任务处理工具。

其他阻塞系统调用(如 wait、read 和 write)依赖外部事件将控制返回给进程。很快，程序员意识到外部事件可能永远不会发生，并设计了减轻风险的方法。

Unix 引入了[信号](https://en.wikipedia.org/wiki/Signal_(IPC))和类似[警报](http://pubs.opengroup.org/onlinepubs/9699919799/functions/alarm.html)和[设置定时器](http://pubs.opengroup.org/onlinepubs/9699919799/functions/setitimer.html)的服务，在特定事件或特定时间后将控制权交还给调用进程。

后来，像 [select](https://idea.popcount.org/2016-11-01-a-brief-history-of-select2/) 、 [poll](http://pubs.opengroup.org/onlinepubs/7908799/xsh/poll.html) 、 [kqueue](https://www.freebsd.org/cgi/man.cgi?query=kqueue&sektion=2) 或 [sigtimedwait](http://pubs.opengroup.org/onlinepubs/9699919799/functions/sigtimedwait.html) 这样的新系统调用从一开始就引入了超时参数。

## 来自贝尔实验室的 9 号计划

与 Linux 的 [400 系统调用](http://man7.org/linux/man-pages/man2/syscalls.2.html)相比，Plan 9 的 [API 要简单得多](http://aiju.de/plan_9/plan9-syscalls)，但它仍然支持一些时间控制风格:

*   [sleep](http://man.9front.org/2/sleep) 将调用进程挂起一段由参数指定的毫秒数。
*   [报警](http://man.9front.org/2/sleep)导致[报警通知](http://man.9front.org/2/notify)在几毫秒后被发送到调用进程。
*   tsemaquire 只等待几毫秒来获取一个信号量。

按照设计，Plan 9 只为非阻塞 I/O(通过`alarm`)提供非常有限的支持，不支持 [I/O 多路复用](https://notes.shichao.io/unp/ch6/):

*   如果一个程序需要**等待几个资源**，它通常会调用 [rfork](http://man.9front.org/2/fork)
*   如果一个程序想要**服务几个并发客户端**，它通常会暴露一个 [9P2000 文件系统](http://man.9front.org/5/intro)

此外，通过 [libthread](http://man.cat-v.org/9front/2/thread) ，Plan 9 提供了霍尔的[通信顺序进程](http://www.usingcsp.com/cspbook.pdf)的实现，其中专用(抢先调度的)进程用于发出任何阻塞系统调用，几个协作调度的线程在单个进程中共享内存，用于访问全局状态。

# 科学怪人的诅咒

Unix 中处理超时的不同方法之间的复杂交互是其 T2 设计理念的直接影响。

正如 Richard P. Gabriel 在他的著名文章中所解释的，“Unix 和 C 是终极计算机病毒”。

## 越差越好，不是吗？

“越差越好”是一种软件设计和实现模型，它具有以下特征(按重要性降序排列):

*   简单:设计必须简单，包括实现和界面。实现简单比接口简单更重要。简单是设计中最重要的考虑因素。
*   **正确性**:设计应该在所有可观察到的方面都是正确的，但是简单一点总比正确一点好。
*   一致性:设计不能过于不一致。在某些情况下，为了简单性可以牺牲一致性，但最好是放弃那些处理不常见情况的设计部分，而不是在实现中引入复杂性或不一致性。
*   完整性:设计必须涵盖尽可能多的重要情况。应涵盖所有合理预期的案例。

为了任何其他质量，完整性可以被牺牲掉。事实上，只要实现的简单性受到危害，完整性就必须被牺牲。如果保持简单性，可以牺牲一致性来实现完整性；尤其不值钱的是界面的一致性。

在某种程度上，所谓的“新泽西风格”是对一种最小可行产品的追求，这种产品能够最大限度地缩短 T2 的上市时间并获得 T4 的先发优势。

引用加布里埃尔的话

> 早期的 Unix 和 C 编译器都具有简单的结构，易于移植，运行时需要很少的机器资源，并且提供了操作系统和编程语言所需的 50%-80%的功能。
> 
> […]
> 
> 重要的是要记住，最初的病毒必须基本上是好的。如果是这样的话，只要它是可携带的，病毒的传播是确定的。**一旦病毒扩散**，就会有**的压力来改进它**，可能是通过增加其功能**到接近 90%** ，但是**用户已经习惯于接受比正确的事情更差的**。

到目前为止一切顺利。

早期的 Unix，如 Plan 9，并不追求完美，而是足够好地满足大多数用户的需求。

## 不过是“足够”，够吗？

Unix 哲学传播到了 Unix 之外，它成为了迄今为止软件工程的普遍智慧。

对于早期的 Unix 和 Plan 9，适可而止。请看 9front，它是一个始终遵循这一理念的现代系统。

然而，有一个问题。

Plan 9(像早期的 Unix 一样)，是一个整体进化的系统。这在[9 号前储存库](http://code.9front.org/hg/plan9front/)中尤为明显。整个系统一起进化，所有的程序都被不断修改，以满足[用户](http://cat-v.org/)不断变化的需求。

计划 9 是计算机的一个应用。一个操作系统分裂成几个可执行文件，但仍然**一件事**。

因此，在它走向完整的渐进过程中，所有的部分都在一起进步。当他们提供所需功能的 90%时，这实际上意味着 90%。或者 95%。或者 97%。或者 99%。

但是，如果我们将系统分成独立的组件，并按照相同的理念将它们分配给不同的团队，会怎么样呢？

这就是主流操作系统的遭遇。

随着它们的广泛采用，人们的各种需求再也不能由一家公司来满足了。因此，良好的 API 和开发工具为构建应用生态系统提供了战略优势，通过在特定操作系统上运行，应用生态系统将扩大其市场份额。

Unix 特别适合应用程序开发人员的这种竞争。

然而，这个过程有一个明显但被忽视的缺点:运行的操作系统失去了它的统一性和一致性。操作系统变成了由各种不同的团队开发的杂牌软件的集合，每个团队都有自己的目标、爱好和一套最佳实践。

## 更多的碎片，更多的裂缝！

拿一个在 99%的情况下都能正常工作的应用程序来说，你在 99%的情况下都不会有问题。

但是，如果您有 10 个应用程序，每个都在 99%的情况下正常工作，那会怎么样呢？

它们在每个计算机时钟上一起正确工作的概率是(99%)^10.)也就是说:他们的组合在 90%的情况下会做你所期望的事情。

在这样的假设下，系统正确运行的概率很快下降，接近公式

*p(n)=(0.99)*

[![Probability of whole system correctness.](img/f51e92a273616a7751257605abee3153.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VgSqKuXn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://jehanne.io/graphic/probability_of_correctness.png)

你现在运行多少程序？:-D

这就是弗兰肯斯坦的诅咒:更多的碎片，更多的裂缝。

## 单胞藻

贝尔实验室的 9 号计划从一开始就遵循了新泽西的风格，如果它是化身的话，在大多数情况下仍然如此。

但是梅乌奇实验室的约翰呢？

原来 Jehanne 的设计没有遵循新泽西风格。我不着急。然而，Jehanne 也没有遵循麻省理工学院/斯坦福大学的风格。

遵循加布里埃尔的计划，我们可以说，约翰是基于

*   **简单**:设计一定要简单。一些简单、易学和正交的抽象应该能够描述任何可以想象的用例。如果实现困难，说明设计不够简单。
*   **正确性**:设计应该在所有可观察到的方面都是正确的。不正确是不允许的。
*   **一致性**:设计不得不一致。任何不一致都揭示了设计问题:要么是缺少抽象，要么是抽象不够正交。
*   可组合性(Composability):完整性应该自然地作为一种(令人满意的)副作用出现。因此，它不可能是一个目标:设计必须涵盖尽可能少的实用的重要情况，并让用户组合所提供的抽象来构建他需要的东西。

我把这一套原理叫做*单纯信号素 veri* 。

深受其他意大利黑客作品的启发，比如[安东尼奥·梅乌奇](https://en.wikipedia.org/wiki/Antonio_Meucci)、[皮尔·乔治·佩罗托](https://en.wikipedia.org/wiki/Programma_101)、[古列尔莫·马可尼](https://en.wikipedia.org/wiki/Guglielmo_Marconi)、[伦佐·皮亚诺](https://en.wikipedia.org/wiki/Renzo_Piano)和[达芬奇](https://en.wikipedia.org/wiki/Leonardo_da_Vinci)。

# 唤醒系统调用

方案 9 支持 **39 个系统调用**(不算废弃的)。因为一些系统调用可以用其他的来表达，所以在 Jehanne 中，我将副本移到了用户空间中。

这个想法是，从长远来看，更小的内核表面更容易得到正确和简单的结果。

在这样的清理过程中，我意识到`sleep`、`alarm`和`tsemacquire`不知何故是“丑陋的”:我认为他们混合了正交的问题，并寻找他们隐藏的缺失的抽象。

这就是`awake`系统调用是如何诞生的:

```
long awake(long milliseconds); 
```

清醒是`sleep`的补充:它**预定了未来新的时间片**。它是一个基本的构建模块，可用于实现用户空间中的其他服务，如`sleep`、`alarm`和`tsemacquire`。

它在一定数量的毫秒后中断阻塞的系统调用。失败时，它返回`0`。如果成功，它将返回一个负 long 值，该值可用作释放预订时间片的标识符。

在唤醒时，没有通知或信号发送给进程，进程的错误字符串保持不变:阻塞的系统调用简单地返回给进程一个`~0ULL`结果。

一个进程可以注册它需要多少次唤醒(在一个全局系统上限内),每次唤醒都有机会中断一个系统调用。

唤醒分为两个不同的组，这两个组不会相互影响:正常流程执行和笔记处理程序。这些组分别在[出口](http://man.cat-v.org/9front/2/exits)和[标记](http://man.cat-v.org/9front/2/notify)时自动复位。

在 libc 中，[两个非常简单的函数](https://github.com/JehanneOS/jehanne/blob/master/sys/src/lib/c/9sys/awakened.c)包装`awake`以增强可读性。

`Awakened`告知调用进程某个唤醒是否已经发生。`Forgivewkp`告知内核移除某个唤醒。

有了这些原语，将 [sleep](https://github.com/JehanneOS/jehanne/blob/master/sys/src/lib/c/9sys/sleep.c) 和 [tsemaquire](https://github.com/JehanneOS/jehanne/blob/master/sys/src/lib/c/9sys/tsemacquire.c) 转移到 libc 就变得很简单了。

特别是，`tsemaquire`很好地展示了清醒的简单习语:

```
/* book a time slice in the future */
long wkup = awake(ms);
while(blocking_syscall() == -1){
    if(jehanne_awakened(wkup)){
        /* handle syscall timed out */
    }
    /* the syscall has been otherwise interrupted, you can
     * - try again
     * - fail with an error
     * - do whatever fit
     */
}
/* the syscall completed, release the booked time slice... */
jehanne_forgivewkp(wkup);

/* ...and enjoy */ 
```

(细心的读者会注意到 [`alarm`仍在等待被移动到用户空间](https://github.com/JehanneOS/jehanne/issues/2)...事实是这个任务太无聊了！)

公平地说，`awake`最初被设计成只中断`rendezvous`，以在 libc 中启用对 QLock、RWLock 和 Rendez 的[超时支持。](https://github.com/JehanneOS/jehanne/blob/master/sys/src/lib/c/9sys/qlock.c)

但是从一开始就很清楚，它可以被一般化并与其他系统调用组合，以提供其他服务。

随着 [libposix](https://github.com/JehanneOS/jehanne/tree/master/sys/src/lib/posix) 的出现，我用`awake`实现了对 POSIX [非阻塞 I/O](https://github.com/JehanneOS/jehanne/blob/master/sys/src/lib/posix/files.c) 、[信号](https://github.com/JehanneOS/jehanne/blob/master/sys/src/lib/posix/processes.c)和 [sigset 等待者](https://github.com/JehanneOS/jehanne/blob/master/sys/src/lib/posix/sigsets.c)的支持。

这反过来又使纽里布港和 T2 港成为杰汉内港。

这一切都用了大约 [600 行代码](https://github.com/JehanneOS/jehanne/blob/master/sys/src/kern/port/awake.c)。

两个内核进程，一个[定时器](https://github.com/JehanneOS/jehanne/blob/master/sys/src/kern/port/awake.c#L471)和一个[振铃器](https://github.com/JehanneOS/jehanne/blob/master/sys/src/kern/port/awake.c#L621)，通过一个由系统调用填充的唤醒链表合作，该链表按照到期的顺序保存[。在每个](https://github.com/JehanneOS/jehanne/blob/master/sys/src/kern/port/awake.c#L369)[时钟](https://github.com/JehanneOS/jehanne/blob/master/sys/src/kern/port/awake.c#L447)上，如果注册表的第一个元素到期，中断处理器唤醒定时器，准备一个到期定时器的集合。

然后振铃器开始一个循环，以适当地中断该过程。

这两个不同的过程使得在振铃器等待过程处于可中断状态的同时继续处理新的唤醒成为可能。

不是所有阻塞的系统调用都可以被中断。`Create`是一个显著的阻塞系统调用的例子，它被排除在可中断系统调用之外，以防止超时留下一个孤立文件。

# 问题和未来用途

Awake 将用于实现 Jehanne 系统调用的所有超时。

即使没有 libposix，如果需要的话，它也提供了一个简单的机制来实现非阻塞 I/O。

它也将用于在多路复用 I/O 中实现超时。

不幸的是，作为内核调度程序的接口，`awake`不能与控制自己任务的用户空间调度程序一起工作。现在，这只是意味着`qlockt`、`rlockt`、`wlockt`和`rsleept`在链接 libthread 时无法工作。从长远来看，移植虚拟机和在用户空间中提供自己的调度程序的编程语言(如 Go 或 Java)可能会稍微困难一些。

然而，libthread 使用定制的`rendezvous`已经概述了解决方案:我们将简单地覆盖系统调用，将控制权交给所需的调度程序。