# 智能计算，不费力

> 原文：<https://dev.to/awwsmm/compute-smart-not-hard-3gfo>

可能最著名的编码面试问题与斐波那契数列有关:

> `Given a number F(0) = 0, and a number F(1) = 1`
> `Write a program to find F(N) for any N > 2, given:`
> T2】

有各种不同的解决方案，使用迭代、递归、[甚至矩阵乘法](https://www.geeksforgeeks.org/program-for-nth-fibonacci-number/)。“更好”的解决方案谈论尾部调用优化和其他高级编程概念。但是这些解决方案中的大多数仍然慢得让人无法忍受。[这里发布的 JavaScript 解决方案](https://medium.com/quick-code/fibonacci-sequence-javascript-interview-question-iterative-and-recursive-solutions-6a0346d24053)花了将近一秒半的时间找到第 15 个斐波那契数。想象一下计算`F(1000000)`要花多少时间！

许多人没有意识到的一个捷径是，根据规则，第`N`个斐波那契数与黄金分割率相关:

> `F(N) = floor(pow(g,N) / sqrt(5) + 1/2)`
> `for any N > 0`
> T2】

最佳、最有效(就空间*和*时间而言)的解决方案是使用这个关系来计算第`N`个斐波那契数。但是大多数程序员的下意识反应是，在事先没有完全理解问题的情况下，就强行使用它。做一点研究可以节省大量 CPU 周期和浪费的 RAM。

关于这一点，这里有一个问题可能是蛮力的，但也可以用纸笔来解决。做一些假设，使用一些逻辑将问题的参数空间缩小到几个可能的解决方案，然后找到正确的解决方案！

* * *

弗朗西斯科出生在一个年份，这一年的数字乘积等于某个自然数的平方。现在是 2005 年，他在等待他的年龄等于那个自然数`n`的那一年。弗朗西斯科是哪一年出生的，他在等哪一年？

* * *

祝你好运！期待你下面的解决方案。请随意询问任何与提示相关的问题。