# 简而言之就是记忆化

> 原文：<https://dev.to/pancy/memoization-in-a-nutshell-5072>

如果有人曾经看过[破解编码访谈](https://www.amazon.com/Cracking-Coding-Interview-Programming-Questions/dp/0984782850/ref=pd_lpo_sbs_14_t_0?_encoding=UTF8&psc=1&refRID=GDTYV5PQ4RGJS6X5YEAM)(或者任何其他关于算法的书)，你可能至少知道什么是记忆化，它有什么好处。这是一个更全面、更通俗的 JavaScript 版本的摘录。

没有任何问题能比找到`n`斐波纳契数更简单地解释(坏的)递归，所以这里是:

```
 function fib(n) {
    if (n == 0 || n == 1) return n;
    return fib(n - 1) + fib(n - 2);
} 
```

Enter fullscreen mode Exit fullscreen mode

> 哦不，语法高亮对你不起作用？别担心，那只是我。在我看来，高亮显示对于阅读代码来说是相当分散注意力的，尤其是这么小的代码。这对写作很有帮助，但你要把重点放在逻辑步骤上，并确保在阅读时得到它们。

这很简单。一个递归函数，具有一个或多个基本情况(在本例中，当`n == 0`和`n == 1`时)和一个递归情况。如果你完全不知道递归是如何工作的，我推荐这篇文章的软咨询:[递归变得简单](https://medium.com/code-zen/recursion-demystified-24867f045c62)。(还有别忘了鼓掌跟我来。写起来费时费力！)

上述`fib`函数的问题是它在指数时间`O(2^n)`运行。这就像(几乎)你能进入的最糟糕的运行时。具体有多糟？如果你调用`fib(50)`计算第 50 个斐波那契数，花了一分钟返回结果，那么调用`fib(100)`将花费你大约 1，125，899，906，000，000 分钟(四舍五入到百万分之一)，也就是大约 20 亿年(有趣的事实:到那时我们的地球和半个太阳系应该已经被正在增长的孙龙吞噬了)。

公平地说，这个问题是递归的一个有意的坏候选。本递归部分:

```
 fib(n - 1) + fib(n - 2); 
```

Enter fullscreen mode Exit fullscreen mode

意味着对于函数调用的每一个`N`节点，都会有两个分支出现。更糟糕的是，对于每个`N`调用，都有重复的工作。下面是一个精心创建的真实发生的 ASCII 树形图:

```
 fib(5)
                        /          \
                   fib(4)           fib(3)
                 /       \          /      \
             fib(3)   fib(2)       fib(2)  fib(1)
           /    \    /     \      /     \
          /      \ fib(1) fib(0) fib(1) fib(0)
       fib(2) fib(1)
      /      \
    fib(1) fib(0) 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，从`fib(3)`往下做的工作可以一次完成，而不是像现在这样重复。当`N = 5`时，你可以看到`fib(N - 2)`被计算了两次，`fib(N - 3)`被计算了三次。如果这种情况持续足够长的时间，说`N`是一个很高的数字，比如 100，你可以肯定

> 对于每一个`fib(N)`，`fib(N - (N - p))`将重复`p`次，其中`p ∈ {x | 2 < x < N}` ( `p`是`range (2..N-1)`的元素)。谈论行李！

## 记忆化=记忆过去

虽然听起来很夸张，但这总结了这项技术的定义。想象一下，你的代码配备了人工智能功能，不重复我们提到的工作量。尽管如此，人工智能需要有一种方法来记忆已经做过的事情。在这种情况下，虚拟的人工智能不会有太大的帮助。它能做的最聪明的事情是意识到`fib`行动是一个自杀任务，并从一开始就切换到记忆模式。

我们大脑快速记忆和回忆的最好方式是什么？把它和其他东西联系起来。这就是关联数组(哈希映射、哈希表、字典)和数组工作方式！

在我们的 fibonacci 例子中，我们可以使用任何一种数据结构，但是 array 更简单，因为键已经是整数了。

这个概念是让`fib`函数“携带”一个充满过去记忆的数组，这样在其迷人的递归生命中的任何时刻，它都可以回忆起它将要做的工作实际上已经完成了，并且应该对它漠不关心。下面是如何做到的:

```
 function fib(n, brain = []) {
    if (n == 0 || n == 1) return n;

    // If brain has no memory of the work for fib(n)
    if (brain[n] == 0) {

        // compute and then memorize it
        brain[n] = fib(n - 1, brain) + fib(n - 2, brain); 
    }

    // just returns what's already memorized
    return brain[n];
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，每当调用`fib`时，它都会携带一个带有过去工作记忆的`brain`,这样它就可以避免重复工作。这当然会为`brain`阵列牺牲线性空间，但现在你可以在几分之一毫秒内完成`fib(10000)`的计算，而不是等待宇宙消失两次。

附注:我会让你来计算我们的“正念”`fib`的新运行时间。

再见