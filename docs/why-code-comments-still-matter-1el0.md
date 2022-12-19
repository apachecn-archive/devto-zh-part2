# 为什么代码注释仍然重要

> 原文：<https://dev.to/realedwintorres/why-code-comments-still-matter-1el0>

*我最初在博客@CACM 上发布了这篇[文章](https://cacm.acm.org/blogs/blog-cacm/225574-why-code-comments-still-matter/fulltext)。*

在计算机科学中，你被教导要注释你的代码。当你学习一门新的语言时，你学习的是该语言的注释语法。尽管编译器或解释器会忽略程序中的所有注释，但注释是有价值的。然而，最近有一种观点认为注释代码是不好的，你应该在你的程序中避免所有的注释。在 2013 年题为[无评论:为什么评论代码仍然是个坏主意](https://visualstudiomagazine.com/articles/2013/07/26/why-commenting-code-is-still-bad.aspx)的文章中，Peter Vogel 继续了这个讨论。

那些认为注释代码是个坏主意的人认为注释增加了不必要的维护。当代码更改时，您还必须修改注释以使它们与代码保持同步。他们认为程序员有责任编写真正显而易见的代码，这样就不需要注释了。尽管这些都是避免注释代码的正当理由，但这些论点过于简单和笼统。评论是*必要的*因为种种原因:

1.  不是所有的程序员都能够/愿意编写真正显而易见的代码。初学程序员只是乐于写一个正确的程序。他们仍在掌握这门手艺。即使是有经验的程序员也会写出草率的代码，有时是由于懒惰(我一直为此感到内疚)。程序像指纹一样是唯一的，所以判断代码是否明显是一个主观的调用。

2.  过多的评论可能会令人不知所措，令人厌烦，但有些评论就像文章中的标题和副标题。它们引导你，提供背景，并传达整体意义。

3.  注释不只是针对代码。他们可以记录重要的程序信息，如作者、日期、许可证和版权细节。

4.  有些编程语言晦涩难懂。一个例子是玻璃编程语言。这个[样本程序](http://esolangs.org/wiki/Glass#Fibonacci_sequence)很难破译，但是它打印了一个斐波那契数列。你清楚这个节目的意思吗？也许可以用一种更显而易见的方式来编写这个程序，但是在程序的顶部加上一个简单的注释就可以快速简单地表达出它的意思。

5.  一些公司要求他们的员工注释他们的代码。谷歌有编程风格指南，规定如何写评论。这包括流行的编程语言，如 [Java](https://google.github.io/styleguide/javaguide.html#s4.8.6-comments) 、 [JavaScript](https://google.github.io/styleguide/jsguide.html#jsdoc) 和 [C++](https://google.github.io/styleguide/cppguide.html#Comments) 。

6.  专门的注释允许像 [javadoc](http://www.oracle.com/technetwork/java/javase/tech/index-137868.html) 、 [JSDoc](http://usejsdoc.org/) 和 [apiDoc](http://apidocjs.com/) 这样的工具自动为程序生成专业、全面和一致的文档。

7.  注释可以是未来工作的占位符。这是一种为大型程序创建大纲的有用方法。事实上，Eclipse 集成开发环境(IDE)在生成 main 方法时会自动创建一个 TODO 注释。这是一个添加程序启动代码的提醒。

评论你的节目有很多重要的原因。虽然评论可能是乏味的或令人不知所措的，但它在许多情况下是有价值的。即使你认为你写了非常明显的代码，试着在几个月或几年后阅读你的代码。这对你来说仍然是显而易见的吗，或者你希望得到评论？

感谢阅读。😃

*在 Twitter 上关注我 [`@realEdwinTorres`](https://twitter.com/realEdwinTorres) 获取更多编程技巧和帮助。*

<sub><sup>图片鸣谢:加博尔赫贾，[链接](https://www.freeimages.com/photo/php-code-1242330)</sup></sub>