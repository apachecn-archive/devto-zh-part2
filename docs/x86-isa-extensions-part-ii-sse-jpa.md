# x86 ISA 扩展第二部分:SSE

> 原文：<https://dev.to/ilevex/x86-isa-extensions-part-ii-sse-jpa>

欢迎回到本系列，探索 x86 体系结构在过去几十年中的许多扩展。在本系列的这一期中，我们将关注 MMX 的继任者:流式 SIMD 扩展，简称 SSE。这些指令大多是 SIMD(顾名思义)，代表**S**single**I**construction**M**multiple**D**ATA。简而言之，SIMD 指令类似于我们在 MMX 的文章中提到的指令:一条指令可以处理多个数据组。

SSE 于 1999 年在英特尔看到 AMD 的“3DNow！”后不久与英特尔的奔腾 III 一起推出扩展(我们将在以后的文章中讨论这个扩展，但是现在我缺少可以使用的 AMD 机器🙂).一个问题自然产生了:SSE 不是英特尔引入 x86 处理器家族的第一个 SIMD 集，那么英特尔为什么要创建一个新的扩展集呢？不幸的是，MMX 当时有两个主要问题。首先，它“引入”的寄存器是以前存在的寄存器的别名(有趣的是，由于更容易的上下文切换，这一度被吹捧为一种优势)，这意味着浮点和 MMX 操作不能共存。第二，MMX 只处理整数，它不支持浮点，而浮点是 3D 计算机图形中越来越重要的一个方面。SSE 增加了几十条在独立寄存器集上操作的新指令，以及一些继续在旧的 MMX 寄存器上操作的整数指令。

(开始之前有一点需要注意:在本文中,“SSE”指的是英特尔推出的首个 SSE 扩展。在本系列的后续文章中，我们将探讨 SSE2、SSE3、SSSE3、SSE4 和 SSE4.1，但这里我们将重点讨论“SSE1”。)

## 有 SSE 吗？

与所有指令集扩展一样，您的 CPU 有可能没有它。鉴于 SSE 的年龄，使用 SSE 的机会再次变得相当渺茫，但是看到人们如何确信其 CPU 对 SSE 的支持总是很有趣的。

在 Linux 上:

```
$ cat /proc/cpuinfo | grep -wq sse && echo “SSE available”  || echo “SSE not available” 
```

在 OS X/macOS 上:

```
$ sysctl machdep.cpu.features | grep -wq SSE && echo “SSE available”  || echo “SSE not available” 
```

或者，CPUID 提供了一种在裸机上或以与操作系统无关的方式收集这些信息的方法。SSE 由 CPUID 叶 1、EDX 位 25 指示:

```
.text
.globl _is_sse_available
_is_sse_available:
    pushq   %rbx

    movq    $1, %rax
    cpuid
    movq    %rdx, %rax
    shrq    $25, %rax
    andq    $1, %rax

    popq    %rbx
    ret 
```

一旦您对您的 CPU 允许 SSE 指令感到满意，就该深入研究 SSE 的细节了！

## 寄存器

由于 SSE 引入了实际的、新的寄存器(与其前身相比)，我认为快速浏览一下它们是有用的。SSE 增加了 8 个 128 位寄存器，命名为:`%xmm0, %xmm1, ..., %xmm7`。(有趣的是，`xmm`是`mmx`的反义词，后者是 MMX 寄存器的名称，我假设这是一个双关语，但我找不到来源证实)与 MMX 形成鲜明对比的是，SSE 不允许多种数据类型。每个 XMM 寄存器可以保存四个 32 位的单精度浮点，而 MMX 可以保存不同宽度的整数。

```
%xmm0, %xmm1, ..., %xmm7:
*- - - - - - - - -*- - - - - - - - -*- - - - - - - - -*- - - - - - - - -*
| 32-bit SP float | 32-bit SP float | 32-bit SP float | 32-bit SP float |
*- - - - - - - - -*- - - - - - - - -*- - - - - - - - -*- - - - - - - - -*
|                            128-bit value                              |
*- - - - - - - - -*- - - - - - - - -*- - - - - - - - -*- - - - - - - - -* 
```

在该图中，每一行代表一种可以在上交所 XMM 寄存器中的数据类型。我将“128 位值”放在图中，因为如果您只将数据加载到寄存器中，而不发出任何浮点运算，那么它可能是任何非结构化数据。然而，当使用浮点时，寄存器中的数据只支持四个单精度浮点。非结构化数据可能会导致异常发生。

为了控制某些操作的状态，增加了一个额外的控制和状态寄存器，称为`MXCSR`。该寄存器不能使用`mov`系列指令访问，SSE 增加了两条新指令，允许加载和存储寄存器，`LDMXSCR` & `STMXSCR`。该图显示了它的布局，然后解释了它在 SSE 环境中的用法。

[![The MXCSR register](../Images/3620dd4bc40d2fe36b2118a880381039.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Z0vD41sg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0v95lbtolwb47b0fgs7y.png)

MXCSR 中的位 0-5 是标志，表示发生了某种类型的浮点异常，它们也是粘性的，意味着用户(或操作系统)必须在异常后手动重置它们，否则它们将永远保持设置。第 7-12 位是屏蔽位，当满足与特定异常相关的特定条件时，它们可用于阻止 CPU 发出异常，在这种情况下，处理器将返回值(qNaN、sNaN、确定的整数或源操作数之一；详见[1])。

有关寄存器具体含义的更多信息，参见第 10.2.3 章[1]。

# 指令

现在我们已经介绍了 SSE 扩展中引入的寄存器，让我们看看英特尔添加了哪些新指令及其含义。为了最大限度地利用 SSE，首先要做的是将数据移入新的 XMM 寄存器，SSE 提供了几个指令，其中最常见的有以下指令(`movaps` & `movups`):

```
# Create a memory location with four single-prec floats
vector0: .dq 3.14, 2.71, 1.23, 4.56
scalar0: .dd 1234
vector1: .dq 3.62, 6.73, 8.41, 9.55

movaps vector0, %xmm0
movups vector1, %xmm1 
```

`movaps`代表 **MOV** e **A** 木质 **P** acked **S** 单一精密浮动，`movups`代表相同，但 **U** 未对齐。对齐访问和非对齐访问之间的区别很重要，一般来说，开发人员应该尽可能使用对齐访问，以获得更好的整体性能。

既然我们已经成功地将数据转移到了 XMM 寄存器中，那么让我们对它做点什么吧。我们之前探索过的一个简单的例子是一些简单的向量操作:

```
# assuming vector0 and vector1 from the previous snippet

movaps vector0, %xmm0
movups vector1, %xmm1

addps %xmm0, %xmm1 # ADD Packed Single precision float
subps %xmm0, %xmm1 # undo previous operation
maxps %xmm0, %xmm1 
```

`maxps`是一条非常方便的指令:它比较 XMM 寄存器中四个单精度浮点中的每一个，然后将较大的浮点移入目标操作数(它可以是像`%xmm1`这样的寄存器，也可以是 128 位内存位置)。通过避免循环和许多`cmp`和分支指令，这条指令可以节省大量周期。

SSE 扩展的另一个有趣的方面是可缓存性控制。应用程序程序员现在可以告诉 CPU，一些内存是*【非临时】*，也就是说，在不久的将来不会需要它，所以不要用它来污染缓存，就像这样:

```
movntps %xmm0, vector0 
```

相反(即，如果程序员知道在不久的将来将需要某个存储位置)，也可以使用`PREFETCH`系列指令通知处理器:

| 指令 | 奔腾 III | 奔腾 4/至强 | 世俗的？ |
| --- | --- | --- | --- |
| `prefetch0` | L2 还是 L1 | L2 | 暂时的 |
| `prefetch1` | L2 | L2 | 暂时的 |
| `prefetch2` | L2 | L2 | 暂时的 |
| `prefetchnta` | 腰神经 2 | L2 | 非时间的 |

## 结论

我们将关注的下一个扩展是 SSE2 扩展集，它建立在 SSE 和 MMX 的基础之上，可提供更好的性能。从新一期开始，我们也将引入基准测试。与此同时，请看一下这个系列的 [GitHub Repo 中的一些例子吧！](https://github.com/levex/x86-isa-extensions)下次见！

## 参考文献

[1]: [英特尔 IA-32 软件开发手册，第 11.5.2 章:SIMD 浮点异常条件](https://software.intel.com/en-us/articles/intel-sdm)