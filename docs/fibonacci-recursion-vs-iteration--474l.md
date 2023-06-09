# 斐波那契:递归 vs 迭代

> 原文：<https://dev.to/khalilsaboor/fibonacci-recursion-vs-iteration--474l>

[![Alt:](img/58fdee6a47a4d10be109d5472b8678dd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OcnjYiFT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5ph41rti8egm6w8ne0lh.jpg)

一个常见的白板问题，我已经被要求解决好几次了，就是**“写一个函数来生成从 0，1 开始的第 n 个斐波那契数”**。然而，在这篇文章中，我想解决这个问题的一个常见的后续问题，那就是什么方法更有效地解决这个问题递归或迭代。

# 注

在进一步阅读本文之前，最好尝试解决这个问题。所以我列出了几个解决这个问题的常用工具。

*   钢笔/记号笔
*   笔记本/白板
*   VS 代码
*   互联网

斐波那契数列是
中的一个整数序列，其中前两个元素是 0 & 1，每个后面的
元素是前两个元素的和:

0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, ..., 233

# 问题

***写一个函数，生成第 n 个斐波那契数。
第 n 个斐波那契数由:*T4 给出**

```
Fn = Fn-1 + Fn-2
The first two terms of the series are 0, 1.
For example: fib(0) = 0, fib(1) = 1, fib(2) = 1 
```

# 使用递归的解决方案 1

```
 public static int fibonacciRecursion(int nthNumber) {
        //use recursion
        if (nthNumber == 0) {

            return 0;

        } else if (nthNumber == 1) {

            return 1;
        }   
     return fibonacciRecursion(nthNumber - 1) + fibonacciRecursion(nthNumber - 2);
    } 
```

# 分析

通过使用递归来解决这个问题，我们得到了一个简洁的函数。如果给定的数字等于 0 和 1，我们返回两个给定的数字。

如果我们传递一个大于 0 和 1 的数。然后，我们进行两次递归调用，在这两次调用中，我们将第 n 个数字减去 1 和 2。

传递这些整数 0，1，2，3，4，5，很可能会及时地给出- > 0，1，1，2，3，5。

但是如果我们传递更高的数字，比如 50，67，100，会发生什么呢？如果你想用 IDE 中给定的数字运行这个函数。你会开始注意到，这个方法需要多长时间才能给出我们的斐波那契数。现在，尝试运行空间复杂性分析将是一件棘手的事情，因为在这个递归函数的幕后发生了许多事情。性能差的原因是每次递归调用时堆栈内存的大量 push-pop。

现在有一种方法可以解决这个问题，那就是记忆和存储每个斐波纳契公式。但是现在，我将继续讨论迭代法，以及为什么它会更快地计算我们的第 100 个斐波那契数。

# 使用迭代求解#2

```
 public static int fibonacciLoop(int nthNumber) {
        //use loop
        int previouspreviousNumber, previousNumber = 0, currentNumber = 1;

        for (int i = 1; i < nthNumber ; i++) {

            previouspreviousNumber = previousNumber;

            previousNumber = currentNumber;

            currentNumber = previouspreviousNumber + previousNumber;

        }
        return currentNumber;
    } 
```

# 分析

迭代方法将是解决我们问题的首选和更快的方法，因为我们将 Fibonacci 数的前两个存储在两个变量中(previouspreviousNumber，previousNumber ),并使用“CurrentNumber”来存储我们的 Fibonacci 数。存储这些值可以防止我们不断地使用堆栈中的内存空间。因此我们的时间复杂度为 O(n)。

[关于 Java 环境下堆栈和堆内存的更多信息](https://medium.com/java-vault/java-memory-allocation-stack-and-heap-3e25867baa7e)

[有关动态编程方法的更多信息](https://www.youtube.com/watch?v=vYquumk4nWw&t=23s)