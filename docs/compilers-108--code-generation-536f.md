# 编译器 108–代码生成

> 原文：<https://dev.to/lefebvre/compilers-108--code-generation-536f>

代码生成是编译器的最后一步。这是编译器为先前创建的 IR 发出实际机器代码的地方。

编译器系列:

*   [编译器 101–概述和词法分析器](https://dev.to/lefebvre/compilers-101---overview-and-lexer-3i0m)
*   [编译器 102-解析器](https://dev.to/lefebvre/compilers-102---parser-2gni)
*   [编译器 103-语义分析器](https://dev.to/lefebvre/compilers-103--semantic-analyzer-540k)
*   [编译器 104–IR 生成](https://dev.to/lefebvre/compilers-104---ir-generation-39e8)
*   [编译器 105–后端概述](https://dev.to/lefebvre/compilers-105---back-end-overview-15nj)
*   [编译器 106 -优化器](https://dev.to/lefebvre/compilers-106---optimizer--ig8)
*   [编译器 107 -优化器循环展开](https://dev.to/lefebvre/compilers-107---optimizer-loop-unrolling-2n3g)

这是我们在[编译器 101 博客文章](https://dev.to/lefebvre/compilers-101---overview-and-lexer-3i0m)中开始的简单代码:

```
sum = 3.14 + 2 * 4 // calculation 
```

Enter fullscreen mode Exit fullscreen mode

其结果是常量值 11。在 IR 生成并优化后，它可以归结为一行机器代码，这将因处理器和架构而异。机器码只是二进制的，不可读，所以下面是汇编代码的样子。

这是 32 位 ARM 的汇编代码:

```
movs r0, #11 
```

Enter fullscreen mode Exit fullscreen mode

这是 ARM64 的汇编代码:

```
movz w0, #11 
```

Enter fullscreen mode Exit fullscreen mode

x86 和 x86-64 使用此汇编代码:

```
movl $11, %eax 
```

Enter fullscreen mode Exit fullscreen mode

显然，这是制作多平台编译器的棘手部分，因为处理器和体系结构之间的汇编代码是不同的。

一旦你有了计算机可以运行的机器码，最后一步就是把所有的部分连接起来，这样你就有了一个操作系统可以运行的应用程序。