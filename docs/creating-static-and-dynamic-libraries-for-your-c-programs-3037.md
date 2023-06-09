# 为 C 程序创建静态和动态库

> 原文：<https://dev.to/jakebloom/creating-static-and-dynamic-libraries-for-your-c-programs-3037>

你好，世界！这是我在 dev.to 上的第一篇帖子。我很高兴来到这里，今天，我要谈谈 C/C++世界中的代码重用。

比尔·盖茨实际上从未说过“雇用最懒的人来做这项工作，因为他们会找到最简单的方法”，但这种观点听起来确实如此。

如果你是一名土木工程师，你不能在新的建筑中重用以前桥梁上的桥塔，但是软件工程师可以重用以前项目中的代码。这是一个巨大的生产力胜利，我们应该尽可能地利用它。

在处理 C 或 C++时，有两种在项目间重用代码的通用方法——静态库和动态库。

## 静态库

静态库只是一系列的目标文件(。o 文件)一起归档。在 Windows 上，您会看到它们带有。lib 扩展，而在 linux 系统上它们有。a 分机。

例如，假设我们有一个名为“Wave”的项目，用 c 语言进行 web 请求。c 文件，`waveRequest.c`和`waveResponse.c`。为了创建和使用静态库，我们执行以下操作:

```
> # Create the object files
> gcc -c -o waveRequest.o waveRequest.c
> gcc -c -o waveResponse.o waveResponse.c
> # Create the Static Libaray
> ar rcs libWave.a waveRequest.o waveResponse.o
> # compile your code with the library
> gcc -o main main.c -lWave # The compiler adds the "lib" and ".a" itself 
```

现在，无论何时你想使用你的 Wave 库，你都可以复制`libWave.a`并编译到你的项目中。这有几个优点:

*   我们正在重用代码
*   我们可以为我们的 Wave 库编写单元测试，并且确信它在任何地方都可以工作
*   如果库获得了更新并且不是向后兼容的，旧版本仍然会编译到您的代码中

然而，使用静态库有一些缺点:

*   正在复制。不同项目的文件有点笨重，感觉不像一个好的工作流程
*   如果库被更新，并且您想要转移到新版本，您需要重新编译您的项目
*   您可能没有使用该库的所有功能，所以用您的代码编译所有这些功能可能会浪费空间

那么，如果我们不想在静态库中编译成我们自己的代码，我们该怎么办呢？

## 动态库

虽然您必须在静态库中编译，但动态库存储在一个地方，并在运行时加载到多个程序中(想想常见的 C 库，如 stdlib、stdio 和 assert)。他们通常会。所以文件扩展名(或。dll)并且不需要任何额外的编译标志。

使用我们的 Wave 示例，并假设我们有一个 Wave.h 文件，您可以创建一个动态库，如下所示:

```
> # Compile the object files
> gcc -fPIC -c -o waveRequest.o waveRequest.c
> gcc -fPIC -c -o waveResponse.o waveResponse.c
> # Create the dynamic library
> gcc -shared -o libWave.so waveRequest.o waveResponse.o
> # Copy the library to where it can be included
> cp libWave.so /usr/local/lib/libWave.so
> cp Wave.h /usr/local/include/libWave.h 
```

现在，这里发生了一点事情，让我们打开它。首先，我们用`-fPIC`标志编译我们的目标文件，这意味着“独立于位置的代码”——这将从代码中删除所有绝对地址引用，而使用相对地址，允许我们在多个项目中重用代码，而不必重新编译它。

我们还将库和头文件复制到一个地方，编译器知道在那里寻找库和头文件。这在你的机器上可能有所不同，关于 osx 的信息，[看这里](https://developer.apple.com/library/archive/documentation/DeveloperTools/Conceptual/DynamicLibraries/100-Articles/UsingDynamicLibraries.html)，对于 linux 你可以使用`ldconf`。

我们可以在代码中使用 wave 库，如下所示:

```
#include <Wave.h>

int main(int argc, char* argv) {
    wave_make_request("https://dev.to");
    return 0;
} 
```

现在我们正在使用动态库，我们获得了以下好处:

*   每当我们的库升级时，我们会自动获得新版本
*   我们不必在项目之间复制文件
*   我们不用记得在库中编译，直接收录就行了！但是也有一些缺点:
*   如果对库进行了不向后兼容的更新，您可能会以运行时错误而告终
*   在内存较低的机器上，动态库和应用程序代码之间的分页可能会影响应用程序的性能。

这就是我对静态库和动态库的总结！希望你觉得这有用，我们都可以继续偷懒。

干杯