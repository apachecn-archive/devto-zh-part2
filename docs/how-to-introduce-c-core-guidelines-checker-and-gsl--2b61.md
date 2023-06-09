# 如何开始使用 C++核心准则检查器和 GSL

> 原文：<https://dev.to/tomoyukiaota/how-to-introduce-c-core-guidelines-checker-and-gsl--2b61>

###### (此处有日文翻译[。)](https://qiita.com/TomoyukiAota/items/28b39d77646daa74291a)

# 注

在本文中，我描述了如何开始使用 C++核心指南检查器。然而，C++核心指南检查器有一些不成熟的地方，而 [clang-tidy](http://clang.llvm.org/extra/clang-tidy/) 似乎是一个更好的工具。因此，请考虑使用[铿锵](http://clang.llvm.org/extra/clang-tidy/)。( [@tenmyo](https://qiita.com/tenmyo) 指出 [clang-tidy](http://clang.llvm.org/extra/clang-tidy/) 有基于 C++核心准则的检查。)

我留下的关于 C++核心指南检查器的信息仅供参考。

# 简介

## 什么是 C++核心准则检查器？

[C++核心指南](http://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)主要是由
比雅尼·斯特劳斯特鲁普和 Herb Sutter 为了编写更好的 C++代码而创建的。指南中的大多数项目都被设计为由分析工具支持。

C++核心指南检查器是一个基于 [C++核心指南](http://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)的静态分析工具。微软正在开发这个工具，Visual Studio 2017 默认支持。当 VS 编译代码时，该工具会在错误列表窗口中发出违反代码的警告。[Visual Studio c++ Core Guidelines Checker Reference | Microsoft Docs](https://docs.microsoft.com/en-us/visualstudio/code-quality/code-analysis-for-cpp-corecheck)描述了针对何种违规发出何种警告。

截至 2018 年 5 月，C++核心准则检查器仅适用于 Windows 上的 Visual Studio。因此，本文的内容仅适用于此类环境。

## 什么是 GSL？

GSL(指南支持库)是一个包含 C++核心指南建议的类型和函数的库。以下文章介绍了可用的类型和功能。

*   [C++核心指南:指南支持库-ModernesCpp.com](http://www.modernescpp.com/index.php/c-core-guideline-the-guidelines-support-library)

GSL 有几种实现方式。在本文中，我将使用[微软/GSL](https://github.com/Microsoft/GSL) 。

GSL 支持[多平台/编译器](https://github.com/Microsoft/GSL#supported-platforms)。我希望 C++核心准则检查器也能支持多平台。

# 如何开始使用它们？

## 启用 C++核心准则检查器

必须在每个 VS 项目中启用 C++核心指南检查器。下面几页将介绍启用它的过程。此外，还描述了一些有用的东西(比如禁用特定的警告)。

*   [使用 C++核心指南检查器|微软文档](https://docs.microsoft.com/en-us/visualstudio/code-quality/using-the-cpp-core-guidelines-checkers)
*   [Visual Studio 2017 | Visual C++团队博客中的 c++核心准则检查器](https://blogs.msdn.microsoft.com/vcblog/2017/08/11/c-core-guidelines-checker-in-visual-studio-2017/)
*   [在 C++核心准则检查器| Visual C++团队博客中管理警告](https://blogs.msdn.microsoft.com/vcblog/2017/08/14/managing-warnings-in-the-c-core-guidelines-checker/)

## 安装 GSL 代码

将 [gsl](https://github.com/Microsoft/GSL/tree/master/include/gsl) 复制并粘贴到您的代码库中。像柯南这样的软件包管理器更好，但我找不到一个维护良好的。嗯（表示踌躇等）...

## GSL 的包装纸

目前，C++核心准则检查器向第三方库发出警告，并且没有设置来避免它。微软认识到了这个问题，并正在努力解决。)具体来说，当我们使用 GSL 时，它向 GSL 代码发出警告。这种行为导致了对不属于我的代码的警告泛滥。为了避免这种情况，我们需要一个 GSL 的包装器，我们通过包装器使用 GSL。

GSL 包装的代码如下所示。这里有些`_MSC_VER`的 ifdefs 是因为 1。`CppCoreCheck/warnings.h`在 VS 2017 安装过程中创建，2。`#pragma`禁止警告的指令是 MSVC 特有的。

```
#ifdef _MSC_VER
#if _MSC_VER >= 1910
#include <CppCoreCheck/warnings.h>
#pragma warning(disable: ALL_CPPCORECHECK_WARNINGS)
#endif // _MSC_VER >= 1910
#endif // _MSC_VER

#include <gsl/gsl>

#ifdef _MSC_VER
#if _MSC_VER >= 1910
#pragma warning(default: ALL_CPPCORECHECK_WARNINGS)
#endif // _MSC_VER >= 1910
#endif // _MSC_VER 
```

Enter fullscreen mode Exit fullscreen mode

# 代码示例

我创建了一个代码示例。

经过上述步骤，你应该会得到这样的东西。

# 后记

在这篇文章中，我描述了如何介绍 C++核心准则检查器和 GSL。我承认有些尴尬。C++核心准则检查器只能在 Windows 上的 VS 上工作。GSL 是通过复制和粘贴安装的，需要一个包装器来抑制警告。然而，我相信来自 C++核心指导原则检查器的警告允许我写更好的 C++代码，GSL 提供了更好的类型/函数，所以我希望有一天这样的尴尬被清除。