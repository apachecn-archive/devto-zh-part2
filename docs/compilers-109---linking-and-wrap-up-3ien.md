# 编译器 109 -链接和总结

> 原文：<https://dev.to/lefebvre/compilers-109---linking-and-wrap-up-3ien>

链接器在技术上不是编译器的一部分，但它是制作一个完整的应用程序所必需的。链接器的目的是组合(链接)编译器创建的各种机器代码以及必要的信息，为操作系统创建一个可运行的应用程序。

编译器系列:

*   [编译器 101–概述和词法分析器](https://dev.to/lefebvre/compilers-101---overview-and-lexer-3i0m)
*   [编译器 102–解析器](https://dev.to/lefebvre/compilers-102---parser-2gni)
*   [编译器 103-语义分析器](https://dev.to/lefebvre/compilers-103--semantic-analyzer-540k)
*   [编译器 104–IR 生成](https://dev.to/lefebvre/compilers-104---ir-generation-39e8)
*   [编译器 105–后端概述](https://dev.to/lefebvre/compilers-105---back-end-overview-15nj)
*   [编译器 106 -优化器](https://dev.to/lefebvre/compilers-106---optimizer--ig8)
*   [编译器 107 -优化器循环展开](https://dev.to/lefebvre/compilers-107---optimizer-loop-unrolling-2n3g)
*   [编译器 108 -代码生成](https://dev.to/lefebvre/compilers-108--code-generation-536f)

每个操作系统都有一种定义可执行文件的方法，这样它就可以运行该文件。这通常涉及某种头部和描述各种机器代码二进制组件如何组合的格式。这称为可执行类型、可执行格式或目标文件格式。

这是 Xojo 在使用 [LLVM](https://en.wikipedia.org/wiki/LLVM) 构建 64 位 x86 和 ARM 应用时使用的可执行格式:

*   对于 Linux，Xojo 使用 [ELF](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format) (可执行可链接格式)。这是许多类 Unix 系统使用的通用标准。
*   对于 macOS 和 iOS，Xojo 使用 [Mach-O](https://en.wikipedia.org/wiki/Mach-O) 。这是为 Cocoa 应用程序引入的格式，可以追溯到第二天。
*   对于 Windows，Xojo 使用 [PE](https://en.wikipedia.org/wiki/Portable_Executable) (可移植可执行文件)格式。

链接器负责组合编译器为您的项目生成的所有机器码，添加库并生成适当的可执行格式。当链接完成后，你就有了一个在操作系统上运行的本地应用。

对于使用 LLVM 的 64 位 x86 和 ARM 应用，Xojo 使用 [lld 链接器](https://en.wikipedia.org/wiki/LLVM#Linker)。

## 总结

我希望编译器系列对您有所帮助。当你需要为 Windows、macOS、Linux、Raspberry Pi、iOS 或 web 轻松创建自己的跨平台和多平台应用时，一定要看看 [Xojo](http://www.xojo.com) ！