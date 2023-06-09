# 使用 CMake 生成 Eclipse 项目

> 原文：<https://dev.to/zhu48/alphaautomata---2018-11-09-3j3p>

# 事情

作为一个[问题](https://github.com/AlphaAutomata/AlphaRTOS/issues/6)-关闭[提交](https://github.com/AlphaAutomata/AlphaRTOS/commit/0a0165c3c98175a44c7848c156de1f5678cc3e18)今天，[阿尔法 RTOS](https://github.com/AlphaAutomata/AlphaRTOS) 有一个构建过程，我很乐意用它来开发。

使用 CMake [项目脚本](https://github.com/AlphaAutomata/AlphaRTOS/blob/master/build/CMakeLists.txt)和相关联的[工具链脚本](https://github.com/AlphaAutomata/AlphaRTOS/blob/master/build/toolchain.cmake)，我现在能够在以下环境中运行 Alpha 内核的编译:

*   Windows 上的 Xilinx SDK
*   Ubuntu(在 Windows 子系统中用于 Linux 版本)

我很难过 Visual Studio 2017 不在那个名单上，但出于某种原因，CMake 拒绝发现微软编译器。

# 背景

在我的第一篇文章中，我总结了[阿尔法自动机](https://github.com/AlphaAutomata)的起源，它当时的状态，以及我想把它发展成什么样子。

由于这个项目涉及 VHDL 模块，我的许多开发将在 Xilinx Zynq SoC 上进行。我当前的首要任务是让阿尔法 RTOS 内核运行起来，原因有几个:

*   我想加深对实时操作系统的了解。
*   我想设计和实现一个有点复杂，高度模块化的系统，我可以感到自豪。阿尔法自动机的其他组件都没有 RTOS 复杂。
*   一个 [C++Now 2018 演讲](https://www.youtube.com/watch?v=c9Xt6Me3mJ4&t=2342s)第一次给了我一个如何使用 CMake 工具链文件的好例子。这启发我为在[阿尔法自动机](https://github.com/AlphaAutomata)中使用 CI/CD 打下基础。
*   拥有一个工作的 RTOS 使得为 Alpha 自动机的其他部分编写和运行测试代码变得更加容易。

列表中的最后两个要点让我现在为阿尔法 RTOS 创建 CMake 脚本，而不是只使用在 Xilinx SDK 中手动设置的项目，并留待以后使用 CMake。

# 制造的

[C++Now 2018 演讲](https://www.youtube.com/watch?v=c9Xt6Me3mJ4&t=2342s)展示了在任何 ide 之外编译裸机可执行映像的 CMake 示例。对于[阿尔法 RTOS](https://github.com/AlphaAutomata/AlphaRTOS) ，我想在 Xilinx SDK 中编译一个裸机静态库。使用 Xilinx SDK 编译一个静态库使我能够跳过研究 Zynq 内存映射以创建链接器脚本以及试图找出如何编译初始化特定于 Zynq 的寄存器的 Xilinx 库的枯燥工作。使用 Xilinx SDK 迫使我学习如何使用 CMake 生成 Eclipse 项目。

我很高兴有机会学习如何使用 CMake 生成 Eclipse 项目。当我在[威斯康星机器人](https://github.com/WisconsinRobotics)的时候，我们创建了一个[包库](https://github.com/WisconsinRobotics/BadgerCommandLibrary)，它是使用 CMake 针对 Linux 和 Windows 构建的。我希望嵌入式目标构建系统(我们当时使用 Code Composer Studio)也使用 CMake，但是我们一直没有时间来实现这一点。

用于[阿尔法·RTOS](https://github.com/AlphaAutomata/AlphaRTOS)的 CMake 脚本的[第一版](https://github.com/AlphaAutomata/AlphaRTOS/commit/98c2bcadda4a50ea58bc27492a7510c52723986c#diff-58b29f322932c0c2e13d8ead532526bb)紧密地反映了来自 [C++Now 2018 演讲](https://www.youtube.com/watch?v=c9Xt6Me3mJ4&t=2342s)的例子。这是我第一次尝试编写交叉编译的 CMake 脚本，我只想让它正常工作。这些脚本的第一个版本可以针对主机系统运行编译，并在 Linux 上交叉编译。然而，我无法使用 CMake 提供的 eclipse CDT 生成器生成 Eclipse 项目。即使我给了 CMake 到 ARM 目标编译器的正确路径，CMake Eclipse CDT 生成器还是不断抱怨:

```
CMake Error: CMake was unable to find a build program corresponding to "Unix
Makefiles".  CMAKE_MAKE_PROGRAM is not set.  You probably need to select a
different build tool. 
```

经过一些阅读和实验，我发现我需要将 CMake 指向一个正确的`make`可执行文件。在进一步挖掘之后，我发现 Xilinx 随 Xilinx SDK 发布了一系列 GNU 工具，包括`make`和`gcc`。有了这个新知识，我[对](https://github.com/AlphaAutomata/AlphaRTOS/commit/036c626aecb7e75f12e8ee96129ba7c6394552ae#diff-58b29f322932c0c2e13d8ead532526bb)[阿尔法 RTOS](https://github.com/AlphaAutomata/AlphaRTOS) CMake 脚本做了改进。如果给定 Xilinx SDK 版本和 Xilinx SDK 安装目录，工具链脚本现在可以使用 Xilinx 发布的工具。

这生成了我可以导入到 Xilinx SDK 中的 Eclipse 项目文件，但是由于一些奇怪的原因，该项目无法编译。编译器一直抱怨找不到任何`#include`文件。我在网上搜寻答案，但没有找到。最后，我从 SDK 控制台获取了 compile 命令，并尝试在 PowerShell 中使用它。我用了不到 10 分钟的时间就弄明白了谷歌搜索几个小时都无法告诉我的事情:Xilinx 发布的 GCC 版本不处理包含有`..`的路径。

使用此标志运行`gcc`有效:

```
-IC:/SVC/AlphaAutomata/AlphaRTOS/api 
```

使用此标志运行`gcc`失败:

```
-IC:/SVC/AlphaAutomata/AlphaRTOS/build/../api 
```

我的 CMake 脚本使用`set`和`..`特殊目录:
设置父目录

```
set(PARENT_DIR ${CURRENT_DIR}/..) 
```

使用`get_filename_component`修复了我的问题:

```
get_filename_component(PARENT_DIR ${CURRENT_DIR} DIRECTORY) 
```

有了[最新版本]中的这个补丁，我现在可以使用 CMake 生成的 Xilinx SDK 项目运行编译了。

# 阿尔法的状态

自上个月以来，情况没有太大变化。我已经定义了[阿尔法 RTOS](https://github.com/AlphaAutomata/AlphaRTOS) API 和内部架构，但是内核离可用状态还很远。现在 CMake 脚本正在工作，我希望取得更快的进展。

# 向前及向上

我将继续致力于[阿尔法 RTOS](https://github.com/AlphaAutomata/AlphaRTOS) 来让内核运行并巩固 API。

我承诺每周三在 Dev 上发帖，但我最后一次发帖是在一个月前。由于工作和其他生活压力，我不能坚持每周一次的时间表，但我会非常努力地每两周发布一次。