# 以斐波那契为例学习动态编程

> 原文：<https://dev.to/rattanakchea/dynamic-programming-in-plain-english-using-fibonacci-as-an-example-37m1>

说实话，**动态编程** (DP)是一个让我难以琢磨的话题。我认为原因之一是我没有以正确的方式学习它，也没有充分理解它的概念来建立一个如何正确解决它的心智模型。通常，要么我花很长时间解决一个 DP 问题，然后第二天就把它忘了，要么我就是做不到。

学会如何学习对我来说很重要，要尽可能多地保留。我认为斐波那契数列是学习 DP 的一个很好的例子。我将向您展示 4 种不同的解决方法:递归、使用递归的 DP、DP 自下而上方法(优化运行时)、DP 自下而上方法(优化空间)。

## 声明

给定斐波那契数列
`1, 1, 2, 3, 5, 8, 13, 21, 34, 55, ....`中的一个数字 n，求索引值

斐波纳契数列是一个数字序列，其中当前数字是前两个数字相加的结果。例如，fib(5)应该返回 5。

# 方案

## 1。使用递归

```
var fibRecursive = function(n) {
    if (n<=2) return 1;
    return fibRecursive(n-1) + fibRecursive(n-2);
} 
```

Enter fullscreen mode Exit fullscreen mode

这个实现简洁易懂。
我们只需要在`n <=2`时有基本用例，并在`n-1` & `n-2`上做递归调用。
缺点是 1 个电话变成了 2 个电话。两个电话变成了四个。等等。它是指数级的。
时间复杂度 O(2^n)和空间复杂度也是 O(2^n)。

## 2。DP =递归+记忆

简而言之，DP 是一种有效的方式，我们可以使用 memoziation 来缓存访问过的数据，以便以后更快地检索。

```
var mem = [];
var fibRecursiveMem = function (n) {
    if (mem[n]) return mem[n];
    if (n<=2) mem[n] = 1;
    else {
        mem[n] = fibRecursiveMem(n-1) + fibRecursiveMem(n-2);
    }
    return mem[n];
} 
```

Enter fullscreen mode Exit fullscreen mode

这个实现使用`mem`作为一个数组(或散列)来存储已经计算的 num 的值。这将大大减少调用堆栈的数量和调用堆栈中的重复计算。
例如

```
fib(4) = fib(3) + fib(2)
fib(2), fib(3) were already saved into mem, so will fib(4) 
```

Enter fullscreen mode Exit fullscreen mode

```
fib(5) = fib(4) + fib(3)
The previously saved fib(3) and fib(4) will be used to avoid duplicated calculation and call stacks 
```

Enter fullscreen mode Exit fullscreen mode

时间复杂度 O(N)，空间 O(N)

## 3。DP 自下而上方法(优化运行时)

```
var fibBottomUp = (n) => {
    var mem = [];
    for (var i=0; i <=n; i++) {
        if (i == 0) mem[i] = 0;
        else if (i <=2) mem[i] = 1;
        else {
            mem[i] = mem[i-1] + mem[i-2];
        }
    }
    return mem[n];
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过使用带有`for`或`while`循环的自底向上的解决方案来进一步优化运行时。我们仍然使用记忆化，但是不再有递归调用。
时间复杂度 O(n)，空间 O(n)

## 3。DP 自下而上方法(优化空间)

```
var fibBottomUp2 = (n) => {
    var first, second = 0;
    for (var i=1; i <=n; i++) {
        if (i == 1) second = 1
        else {
            var temp = second;
            second = first + second;
            first = temp;    
        }
    }
    return second;
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，我们只需要最后两个数来计算下一个斐波那契数列。记住这个逻辑，我们可以用两个变量来存储最后两个斐波那契数列。时间 O(N)，空间 O(1)

留下下面的评论，以获得更多的讨论和有用的资源。

延伸阅读:
源代码:
[https://github . com/rattanakchea/coding _ interview/blob/master/src/fibona ii . js](https://github.com/rattanakchea/coding_interview/blob/master/src/fibonacii.js)

这篇博文包括一个测试用例以及运行时性能
[https://medium . com/developers-writing/Fibonacci-sequence-algorithm-in-JavaScript-b 253 DC 7 e 320 e](https://medium.com/developers-writing/fibonacci-sequence-algorithm-in-javascript-b253dc7e320e)