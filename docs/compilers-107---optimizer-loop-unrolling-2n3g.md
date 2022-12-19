# 编译器 107 -优化器循环展开

> 原文：<https://dev.to/lefebvre/compilers-107---optimizer-loop-unrolling-2n3g>

上一篇文章讨论了一般的优化。在这篇文章中，你将看到一个叫做“循环展开”的特殊优化。

编译器系列:

*   [编译器 101–概述和词法分析器](https://dev.to/lefebvre/compilers-101---overview-and-lexer-3i0m)
*   [编译器 102-解析器](https://dev.to/lefebvre/compilers-102---parser-2gni)
*   [编译器 103-语义分析器](https://dev.to/lefebvre/compilers-103--semantic-analyzer-540k)
*   [编译器 104–IR 生成](https://dev.to/lefebvre/compilers-104---ir-generation-39e8)
*   [编译器 105–后端概述](https://dev.to/lefebvre/compilers-105---back-end-overview-15nj)
*   [编译器 106 -优化器](https://dev.to/lefebvre/compilers-106---optimizer--ig8)

## 循环展开

循环是一个关键的优化点。展开循环意味着多次重复循环的代码内容。这实际上正是你在编写代码时被教导不要做的事情。

循环展开避免了代价高昂的分支。这不一定会展开整个循环，使代码重复 100 次，但可能会展开一点，使代码重复几次。

现代硬件讨厌分支，因为它使其他优化更加困难，所以展开循环可以为编译器本身和 CPU 带来额外的优化。

下面是一个简单的循环:

```
Dim i As Integer = 0
While i < 2
  i = i + 1
Wend
Beep 
```

Enter fullscreen mode Exit fullscreen mode

这被建模为[控制流图](https://en.wikipedia.org/wiki/Control_flow_graph):

[![](../Images/d4212fec3895b5d212f5f39caf0ba0c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4VLzTs8n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.xojo.com/wp-content/uploads/2018/03/2018-03-15_09-24-11.png)

控制流图是一个[有向图](https://en.wikipedia.org/wiki/Directed_graph)。

看上面，while 被转换成 if。在整数赋值之后，有一个分支返回到 if。如果条件为假，则分支到蜂鸣声。对于老前辈来说，这基本上是一个[到](https://en.wikipedia.org/wiki/Goto)的过程。

但是编译器也可能代表你插入一些东西。例如， [Xojo](http://www.xojo.com) 添加了 Yield 函数调用来支持协作线程，这些函数调用不能被优化过程删除。

[![](../Images/4678e1d690ede9279f7c5a8734b03275.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pp4mLqCD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.xojo.com/wp-content/uploads/2018/03/2018-03-15_09-29-59.png)

所以要展开循环，思想是编译器只能确定循环的两次迭代。

展开的结果是这样的:

[![](../Images/2551dd6684071a3fff311f24837454c0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LjmvGToq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.xojo.com/wp-content/uploads/2018/03/2018-03-15_09-32-16.png)

在这个简短的例子中，变量“I”的值从来没有被使用过，所以它可以被丢弃，从而节省迭代时间和存储空间。