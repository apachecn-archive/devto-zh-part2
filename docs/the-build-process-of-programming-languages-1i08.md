# 编程语言的构建过程

> 原文：<https://dev.to/pmihaylov/the-build-process-of-programming-languages-1i08>

*这篇文章是《基础知识中你学不到的基础知识》系列文章的一部分，旨在帮助渴望深入理解编程和计算机科学的人们。*

当我写第一个程序时，我的教练告诉我，我只是写源代码。现在，我必须把它翻译成计算机能理解的语言。我们称之为编译。这是通过单击 Ctrl+F5(在 Visual Studio 中)实现的。就这样，你的程序现在是可执行的了。

这就是他们最初对我的想法，一个项目的构建过程是什么。对于初学者来说，这是一个足够好的解释。但是在某一点上，我意识到当我点击 Ctrl+F5 时，一些过程发生在我们看不到的幕后。我们将在今天的文章中探讨这些过程。

你知道吗，当你点击 Ctrl+F5 时，不同的语言使用不同的过程。你有没有想过为什么用 C++编程比用 C#难？

嗯，我们将无法探究最后一个问题的全部细节。这与语言设计和多年来的决策有很大关系。但是我们将探索这些语言之间的根本差异。区别在于它们的构建过程。

## 编译成本机代码的语言

这种语言最流行的例子是 C 和 C++。

编译成本机代码是什么意思？

当我们构建我们的程序时，代码会经历不同的阶段，最终会被转换成**机器代码**。这是处理器直接执行的代码。关于这个主题的更多信息，请查看[系列的前一篇文章](http://pmihaylov.com/languages-high-and-low/)。

那是高层的看法。但是我们先来偷窥一下源代码转化为机器码的内部过程。

为此，我们将探索构建 C 和 C++程序的过程，因为它们是使用这种方法的最流行的语言。

整个过程包括 4 个步骤- **预处理、编译、汇编、链接**。

### 预处理

在 C 和 C++程序中，有一些称为预处理指令的行，它们以 **#开头。**

预处理器使用这些指令以某种方式操作程序的文本。比如有一个 **#include** 指令。当预处理器看到它时，它获取由它指定的文件内容，并将其替换到第一次看到该指令的那一行。我们这样做是为了重用代码，而不是将代码复制粘贴到不同的文件中。

[![](img/5e5e5d9049b8e3a3cab15ca48ef03e5f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WgNx6UhL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://pmihaylov.com/wp-content/uploads/2017/11/preprocessing-no-title-1024x430.png)

还有其他指令，但我们不会在本文中探究它们。通常，预处理器将初始源代码转换为预处理代码，并应用所有文本转换。当然，结果存储在一个临时文件中，不会覆盖原始文件。该文件将在编译步骤中进一步使用。

### 编译和汇编

在这一步中，预处理代码被进一步转换成汇编。

然后，另一个程序开始发挥作用，称为汇编程序。它的目的是将汇编代码转换成机器代码。(什么是汇编，如何工作？再来看看这个。)

有可能跳过汇编步骤，因为有些编译器可能会直接将源代码翻译成机器码。但即便如此，这也意味着编译器中嵌入了汇编程序。所以这一步可能是隐含的，但它仍然存在。

产生的机器码被打包到一个名为**目标文件**的中间文件中。这些文件包含单个源文件中的所有数据。但是正如你所知道的，在一个程序中可能有多个源文件。这意味着一个目标文件本身不足以完成一个完整的程序。这是最后一步，叫做链接。

[![](img/ddbae2afe5a248c369d9daadca7935d5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EQMMicKu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://pmihaylov.com/wp-content/uploads/2017/11/compilation-linking-no-title-1024x488.png)

### 连接

现在，您的项目中有一打目标文件。这些是程序的独立模块。最后的链接阶段是将这些块组合成一个可执行文件。这也是解决文件之间各种依赖关系的阶段。

例如，如果文件 A 调用文件 B 中的一个函数，那么文件 A 的目标文件中会有调用该函数的指令。但是函数的指令不在同一个目标文件中。链接器负责检查该函数是否确实存在于程序的任何其他目标文件中。在这种情况下，文件 b。

[![](img/87ab995399468abbdea0920a911ade79.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zakJ0zOM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://pmihaylov.com/wp-content/uploads/2017/11/linking-no-title-1024x382.png)

## 基于虚拟机的语言

这一类中最流行的语言是 Java 和 C#。

这些语言不直接编译成特定硬件的本机代码。相反，它们编译成一种中间语言，由虚拟机执行。

虚拟机就像是您计算机上的模拟计算机。在我们的例子中，它的目的是执行生成的中间代码。它执行程序的方式是了解它运行的硬件类型，并给出该信息以进一步将其编译为机器的本机代码。

这个过程的想法是为一个抽象的虚拟机构建一次你的程序，而理解底层运行的实际硬件是虚拟机的工作。这样，您就获得了平台独立性。

我们看到，当构建编译成本机代码(特别是 C 和 C++)的程序时，我们要经历几个过程。

但是在这些语言中，整个构建过程仅限于将源文件编译成中间语言文件。将这些文件转换为计算机本机代码的过程是虚拟机的工作。

[![](img/91a4b2f196c17247671c7b25ddef6bb9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yUq3YkXZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://pmihaylov.com/wp-content/uploads/2017/11/virtual-machine-1024x492.png)

## 解释语言

这里比较流行的例子有 JavaScript、Python、Ruby 等。

这些根本缺乏编制。用这些语言编写的程序被执行的方式是有一个单独的程序，叫做解释器。它逐行执行其中的指令。相比之下，编译语言的源代码是作为一个整体来分析和执行的，而解释语言是逐行分析和执行的。

这意味着你可以在得到一个错误之前执行程序的一半，而在其他语言中，一个错误就足以阻止整个程序的执行(当然是考虑到这是一个编译错误)。

[![](img/31a7cb3f0f46107b1300760aff0f19c1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UGKjKYIF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://pmihaylov.com/wp-content/uploads/2017/11/interpreted-languages-1024x483.png)

## 每种语言都可以编译/解释吗？

在这篇文章的第一次修订中，一些人聪明地指出一些被解释的语言也可以被编译。反之亦然。

所以回答这个问题——是的，每一种可以被解释的语言和每一种可以被编译的语言。为了理解这一点，我们首先必须更深入地理解什么是编译和解释。

编译意味着我想把语言 X 翻译成语言 Y，如果运行的话，会得到和语言 X 一样的结果，但是更快。

解释意味着用 X 语言编写这个程序，并执行该语言定义的规则。换句话说，运行这个程序。

所以本质上，编译和解释都意味着对一种语言进行改造。这就是为什么每一种可以被编译的语言都可以被解释。

然而，一些语言被称为编译语言而另一些被称为解释语言的原因是因为这些语言传统上用于该过程，并且它们的规则针对该过程进行了优化。

例如，C++程序的编译是一个缓慢的过程，这可以导致编译时性能的极大提高。但是如果解释的话，同样的规则会导致程序运行得更慢。

另一方面，用解释型语言编写的程序有一些规则，这些规则使它很容易被快速解释，但是如果它被编译，在性能方面不会产生很大的好处。例如，将一个 JavaScript 程序转换成另一种语言很难提高性能。

当然，上面的陈述也有例外，因为有各种各样的现代技巧可以让你获得编译的一些好处(比如 JIT 编译)，但是在它们的传统形式中，那些语言不适合被编译。

## 那么为什么要选择一个而不是另一个呢？

我们已经了解了在构建过程中有哪些不同类型的语言。但是现在，让我们来探讨一下，在为我们的工作选择正确的语言方面，它们之间有什么不同。

### 易于编码

第一个重要的方面是用一种语言写作是否比另一种语言容易。而用 C 或 C++编写是迄今为止最难的。我相信**如果你学会了用 C 和 C++编程，你也可以学会用任何其他语言编程**。原因是它们最接近计算机的本质。正因为如此，它离我们人类的本性更远了。

另一方面，用 Java 或 C#编写比用 C 或 C++编写更容易，因为它们被编译成中间语言，这是比本地代码更高层次的抽象。此外，虚拟机的帮助让我们受益于更好的错误处理、垃圾收集和其他功能，这有助于我们编写更安全的代码。

解释语言是更高层次的抽象，允许你用更少的代码完成更多的事情。

### 轻便

计算机科学中一个很大的问题是你的程序是否能在许多平台上运行。对于编译成本机代码的语言来说，情况并非如此，因为生成的代码特定于构建程序的硬件。如果你想让你的 C 程序在 Windows 和 Linux 上运行，你必须为不同的平台重新编译两次。如果它使用特定于平台的指令，那么维护该程序将更加困难。

另一方面，虚拟机在 Java 和 C#中的目的是了解底层硬件是什么，并进一步将代码编译成特定于平台的机器代码。用 Python 或 JavaScript 编写的程序也可以在任何机器上运行，只要有支持这些语言的解释器。

### 表演

这是编译成本机代码的程序擅长的领域。他们没有启动大型虚拟机或运行几兆字节解释器的开销。他们只是跑。

然而，仅仅用 C 写程序并不意味着它会比基于虚拟机的语言更快。Java 开发人员受益于虚拟机对代码运行时的优化。当你用 C 写程序时，你有责任自己优化你的程序。当然，编译器将为您完成大部分工作，但是在某些情况下，这可能还不够。

例如，如果您编译 Java 程序，并且它运行在四核处理器上，虚拟机可能会检测到这一点，并相应地优化您的程序。当然，假设你正在创建一个多线程程序。例如，如果您标记了几个要异步执行的函数，JVM 可以决定在您有一个四代码处理器的情况下可以使用的线程的最佳数量。

在 C 语言中，你必须自己做跑腿的工作，并为使用四核处理器编写具体的指令。

所以最终，像 C 和 C++这样的低级编程语言给了你优化程序的自由，但这仍然是你的工作。

### 记忆

C 和 C++在这里也是赢家。同样，在运行虚拟机或解释器时，会有内存开销。

这对于服务器来说可能不是问题，但是对于嵌入式设备来说却是一个重要的限制。例如，如果您想用 Python 对具有 8 KB 外部存储器的微控制器进行编程，您首先必须在设备上嵌入 2 MB Python 解释器。

至于虚拟机类型的语言，有一些版本更简洁，是为嵌入式环境设计的，但是编译成本地代码的语言在内存使用方面仍然胜过它们。

## 结论

现在你了解了我们编写的程序的构建过程的基础。

总而言之。根据构建过程，有三种类型的语言。那些编译成本机代码的。那些编译成中间语言代码或虚拟机代码的代码。还有那些被解读的。

此外，鉴于这一简要概述，我们了解了为什么一些语言比其他语言快，以及为什么我们选择不同的语言用于不同的目的。

下一次，我们将通过探索什么是栈和堆以及我们的程序如何使用它们来再次深入我们程序的内部。