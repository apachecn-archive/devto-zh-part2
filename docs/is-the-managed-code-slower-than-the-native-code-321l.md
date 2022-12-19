# 托管代码比本机代码慢吗？

> 原文：<https://dev.to/elemarjr/is-the-managed-code-slower-than-the-native-code-321l>

[![](../Images/9cd8c31462907cb0177ac68d685df384.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aYewyEuP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.elemarjr.com/wp-content/uploads/2018/04/evan-dennis-75563-unsplash-300x200.jpg)

这是我多年来一直在回答的问题。在大多数情况下，答案是坚决的“不”。

事实是。NET 使得编写缓慢的代码变得非常容易。更友好的是，它是。NET 开发人员对性能的草率和不加批判，我认为这是一些。网络应用程序的性能糟透了。

RavenDB 团队做了大量工作，一次又一次地证明托管代码可以像本机代码一样快。为什么？因为我们从[架构](https://www.youtube.com/watch?v=eRjWYjEf8n4)到[实现](https://www.youtube.com/watch?v=eRjWYjEf8n4)开始关注性能。

当你建立你的。NET 代码，编译器将其翻译成中间语言和元数据。然后，当您运行代码时，每当第一次执行某个方法时，即时编译器会将其转换为汇编代码。大部分代码优化都发生在这个阶段(如果你想了解这个过程，我推荐你[阅读这篇文章](http://www.elemarjr.com/en/2018/04/trying-to-improve-performance-in-net-here-are-the-basics-you-need-to-know-about-intermediate-language-jit-windbg-and-assembly/))。

JITted 代码的质量非常好。微软已经改进了 JIT，尤其是最近。

事实上，在管理的世界里有优势:

1.  因为代码在执行时被 JIT 化，所以它在内存中的位置比本机代码更好。相关的代码通常更接近，通常在单个内存页面或缓存中。所以我们有更少的页面错误；
2.  与本机代码不同。NET 应用程序不太容易受到内存碎片的影响。发生这种情况时，垃圾收集会压缩堆来解决问题。
3.  内存分配不会发生争用。

请记住:垃圾收集在。NET 是确定性的。我们可以通过控制内存分配模式、对象范围等来显著影响它的运行频率。

[托管代码比本机代码慢吗？](http://www.elemarjr.com/en/2018/04/is-the-managed-code-slower-than-the-native-code/)最早出现在 [Elemar JR](http://www.elemarjr.com/en) 。