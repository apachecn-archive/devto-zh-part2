# 简单的技术问题

> 原文：<https://dev.to/diaakhateeb/simple-technical-questions-5dic>

简单的技术问题，显示你对自己编写的代码理解有多深。

我总是相信用非传统的方式来寻找有才能的人加入我的 team.ِ。同样，当我参加面试来选择我要加入的团队时，我会遵循我的一些技巧。

面试候选人的一种方式是问一些简单的技术问题，这些问题都有哲学的答案。甚至，如果候选人正确地回答了这个问题，我会问他为什么这个问题天生就存在。这样，我可以确保开发人员很好地理解他选择编写代码。我始终相信，这个宇宙上的一切都有一个哲学和一个把它带到现实的规则。

这类问题的一个 C#示例是:

下面每行代码在性能和执行时间上有什么区别？

```
 var i = 7;
        int j = 8;
        var x = new int[] { 9 };
        object y = new int[] { 10 };
        int[] z = new int[] { 11 };

        Console.WriteLine("i " + i);
        Console.WriteLine("j " + j);
        Console.WriteLine("x " + x[0]);
        Console.WriteLine((y as int[])[0]);
        Console.WriteLine(z[0]); 
```

Enter fullscreen mode Exit fullscreen mode

考生需要解释这里的区别，哪个更好，编译器是如何执行的。假设，所有这些都应该给出相同的结果，但是哪个开发者会在他的代码中使用。在这里，他应该理解 var、object 和显式类型化数组声明。

另一个例子是给他一个 10 个元素的数组，其中一个元素重复。工作是找到只有一个循环的重复元素。举个例子，

```
var arr = new int[] {1, 4, 8, 30, 4, 77, 5, 0, 10, 9} 
```

Enter fullscreen mode Exit fullscreen mode

他只需要用一个循环就能找到(4)。

这些简单的问题并不能衡量他对 C#作为一种编程语言的能力，因为我不相信他会坚持使用特定的语言。他们衡量他的天赋、他的思维方式以及他对编程语言的掌握程度。