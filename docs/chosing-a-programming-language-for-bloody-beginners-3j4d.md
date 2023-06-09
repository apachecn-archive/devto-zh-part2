# 为该死的初学者选择编程语言

> 原文：<https://dev.to/hepisec/chosing-a-programming-language-for-bloody-beginners-3j4d>

这篇文章是我的文章《编程新手》的续篇，将会给你一个关于编程语言类型的概述，它们的目的，以及最后如何选择语言来学习。

[![hepisec](img/3acf26df7cd1abc9258ac046a6059ec8.png)](/hepisec) [## 为该死的初学者编程

### 和平证券 5 月 18 日 183 分钟读取

#beginners #learn #programminglanguage](/hepisec/programming-for-bloody-beginners-45ia)

# 概述

在维基百科上，你可以找到一个包含数百种编程语言的列表。其中一些你可能听说过，另一些有个有趣的名字，比如“脑残”,可能大多数你都不知道。

但是所有这些语言之间的区别是什么呢？

你要知道编程语言基本上有两种。这些是编译语言和解释语言。在运行用编译语言编写的程序之前，你必须先编译这个程序。这意味着你的程序的人类可读的源代码被翻译成你的计算机可以理解的机器代码。我们称这种机器码为可执行代码(例如。Windows 系统上的 exe 文件)。

另一方面，解释语言需要一个额外的程序来执行你的代码。这个程序叫做解释程序。解释器读取程序代码并执行其语句。

您可能会听到术语脚本语言是解释型语言的同义词，而术语脚本是用脚本语言编写的程序的同义词。

那么为什么会存在这两种编程语言呢？

两种类型各有利弊。

用编译语言编写的程序必须针对每个目标计算机平台进行编译(例如，针对 Windows、macOS 和 Linux)。这是因为这些平台上的可执行文件使用了不同的文件格式。可能还需要根据目标硬件编译程序(例如，笔记本电脑中的 x86 CPU 需要另一个可执行文件，而不是手机中的 ARM CPU)。所以我们程序的分发可能会变得复杂。但是当我们掌握了这种复杂性，我们的程序就会获得令人难以置信的性能。通常，当您编写程序来解决某个特定的问题时，您不会针对所有的平台和硬件配置，这可能只存在于某个特定的平台上。

因此，当我们确切地知道程序的执行环境时，我们应该选择一种编译语言来获得最佳性能。

对于解释语言，有人已经努力为许多不同的平台编译解释器，你只需要分发你的源代码文件。但是每次程序执行时读取和解释文件的过程不可能像编译后的程序那样快。虽然由于解释器中的一些优化，在正常的程序使用过程中你不会注意到太多的不同，但在程序启动时你至少会注意到一点不同。这时解释器必须读取你所有的程序代码，这一步对于编译后的程序来说是不必要的。

你不知道你的程序将在哪个平台上使用，或者你想面向广泛的受众？解释型语言是你的正确选择。

# 类型安全与弱类型语言

如果程序员只需要在编译语言和解释语言之间做出选择，今天就不会有这么多语言了。语言之间的另一个重要区别是类型系统。您基本上可以区分两种类型系统:类型安全和弱类型。

在类型安全语言中，你的变量、函数参数和返回值是用某种类型声明的。这有助于计算机以高效的内存管理运行程序。有了类型安全，你就不容易出现某些类型的软件错误。但这是另一个增加复杂性的特征。为了选择正确的类型，您需要知道要存储在变量中的值的范围。例如，大多数语言具有不同的整数类型，在内存中具有不同的大小，因此可以存储不同范围的整数。通常整数类型有 8 位(称为一个字节)、16 位(小)、32 位(int)和 64 位(long)。在某些语言中，您可能还需要声明该类型是有符号的(支持负值)还是无符号的(仅支持正值)。由于符号(+或-)存储在变量内存的最左边，所以你丢失了一位来存储你的实际数字。下表显示了典型的整数类型和范围。

[![typical integer types and ranges](img/e8c3fa053d6d2fa82c9b16d60940ec0a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tKmGrnAv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vrm9cueskb42wvs58670.png)

浮点数(称为 float 或 double 的类型)和其他类型也是如此。类型安全通常需要不同类型之间的转换，例如，要比较两个值必须来自同一类型。在弱类型语言中，你可以做类似
的事情

```
if (1 == "1") then [...] 
```

Enter fullscreen mode Exit fullscreen mode

这实际上比较了整数 1 和字符串“1”，它们在内存中的存储方式不同。但是弱类型语言会自动执行到通用类型的转换。在类型安全语言中，代码应该是这样的

```
if (1 == parseInt("1")) then [...] 
```

Enter fullscreen mode Exit fullscreen mode

本例中的函数`parseInt`接受一个字符串参数，并在转换后返回一个整数，然后与左边的整数进行比较。

如果你想要一种简单易学的语言，你应该从弱类型语言开始。但是如果你不害怕复杂的类型系统，那就使用类型安全的语言。

(除了必需的类型转换，弱类型语言通常还有其他缺陷)

# 内存安全与内存不安全语言

另一个需要检查的特性是内存安全。内存安全语言负责管理程序运行所需的所有内存。这意味着您不必太关心类型和数据的大小，它们在程序执行期间存储在内存中。这使得学习语言非常方便。使用内存不安全的语言，你必须自己编写内存管理代码。这可能对性能关键的程序有帮助，但在大多数情况下，这种自编码内存管理容易出现安全漏洞，并且需要一些经验。

# 编程范例

只有 3 个选项可供选择，我们仍然不会有几百种编程语言。但是编程语言不仅仅是考虑到前面讨论的 3 个特性而创建的。编程语言的另一个非常重要的特征是编程范式。它定义了你的代码是如何组织的。例如，你可以编写面向对象的代码，用方法和属性来定义类，或者你可以编写或多或少“从上到下”执行的过程代码，或者编写函数代码，其中每个语句都是一个函数。有许多不同的编程范例，但我更习惯于面向对象的代码。在开发人员的职业生涯中，你会建立自己的偏好。

# 例子

现在我们已经听说了编程语言的足够多的特性来创建各种各样的语言。下面是一些流行语言的例子以及它们使用的特性。

## PHP

PHP 是我目前用于 web 项目的首选语言。它是一种解释语言，解释器几乎可以在任何平台上使用。它通常用于服务器端应用程序。PHP 是弱类型的，内存安全的，支持过程式编程风格和面向对象的代码。随着新版本的出现，PHP 社区在语言中加强了更多的类型安全。

## Java

Java 是我首选的客户端应用程序语言。当代码被编译时，它是一种混合语言，但是它仍然需要一个主机应用程序来运行程序(这被称为 JVM - Java 虚拟机)。因此，您可以编译一次应用程序，然后在任何有 JVM 的地方(几乎任何地方)运行它。Java 是类型和内存安全的，并且是严格面向对象的。JVM 抽象了系统特定的行为，因此 Java 非常适合于平台无关的应用程序。

## JavaScript

JavaScript 和 Java 没有关系。从名字就能猜到，它是一种脚本语言。您目前使用的是任何桌面系统的默认 JavaScript 解释器，即您的 web 浏览器。因此，开始用 JavaScript 编写“Hello World”应用程序所需要的只是一个文本编辑器和 web 浏览器。它是一种弱类型的内存安全语言，支持不同的编程范例，包括过程式的、函数式的和面向对象的。

## C

c 是一种编译语言，它是内存不安全和类型安全的。如果你想为普通用户编写具有图形用户界面的应用程序，你通常不会选择 C。但是如果你想开发硬件驱动程序或对性能非常关键的算法，C 是你的选择。它是一种过程语言，有一个面向对象的后代，叫做 C++。

# 结论

这篇文章应该给你一个关于编程语言之间基本差异的概述，希望你现在能够选择一种语言来学习。