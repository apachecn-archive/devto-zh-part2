# F#中的二分法

> 原文：<https://dev.to/eegodinez/bisection-method-in-f-4hdl>

数值方法是一种数学方法，用于寻找数学问题解的 ***近似*** 。与数值方法相对应的是分析方法。作为人类，我们很自然地求助于使用分析方法来解决数学问题(代数操作、逻辑、巧妙的思维)。然而，我们不太擅长快速做加法、减法或乘法(尝试将两个 20x20 的整数矩阵相乘，并测量完成乘法所需的时间)，所以我们求助于计算机来做这种繁重的、重复的工作。

# 二分法

二分法是一种基本的求根算法。为什么我们对寻根感兴趣？简单来说，当我们想在未知的 x 中求一个方程的解时，方程可以改写为 f(x) = 0。当函数在值 x 处求值时，根是满足方程 f(x) = 0 的 x 的值。换句话说，根的值给了我们方程的解。求根算法在许多工程领域中有大量的应用，在这些领域中，我们想要求解困难的方程(例如，物理学中的弹道学)。

该算法的第一步是定义一个区间[a，b],我们相信根将会在这里。定义第一个区间的一个好方法是绘制函数来直观地看到根(函数与 x 轴相交的点)。我们继续减少区间长度，直到按照我们期望的那样精确地分离出解(记住，我们不计算精确解，只计算近似值)。

在每次迭代中，我们在当前区间的中点评估函数，并将其与在当前点 a 评估的函数的符号进行比较。根据中点处函数的符号，我们可以丢弃区间的上半部分或下半部分。

*   如果中点处函数的符号等于当前点 a 处函数的符号，那么我们的区间就变成了
    *   (中点，b)
*   其他
    *   (a，中点)

我们重复这个过程，直到这个区间按照期望的那样精确地分离出方程的根。这种数值方法的主要优点是，如果一个函数在两次初始猜测之间是连续的，二分法保证收敛。但是，这种算法的主要缺点是收敛速度慢。

从图形上看，二分法是这样的:

[![bisection_graphical](img/f33dd5577ed54ae9acef38b042701232.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HnQAj4hG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x9hvxcxn5kzzhehsdiqg.gif) 
*(Imagen 摘自内布拉斯加大学林肯分校的计算机科学与工程网站。)*

一些读者可能已经注意到，这个算法类似于 ***二分搜索法算法*** 。

# F#实现

F#是一种多范例编程语言。它可用于函数式、命令式和面向对象的编程。这个实现是纯功能性的。

```
let rec Bisection_Method (f:float -> float) (a:float) (b:float) (tol:float) (iternum:int) :float =
    //assert that there is a root inside the interval
    if (sign(f a) * sign(f b) > 0) then 
        printfn "No root inside the interval! Returning 0."
        0.0
    else
        let m = (a+b)/2.0
        //if our approximation is not good enough, recursively iterate until it is.
        if (b-a >= tol ) then
            printfn "iternum: %d | midpoint: %f" iternum m
            (*if the sign of the function at the midpoint is equal to the sign of the function at our current point,
            discard the lower half of the interval 
            *)
            if sign(f a) * sign(f m) > 0 then
                Bisection_Method f m b tol (iternum+1)
            else
                Bisection_Method f a m tol (iternum+1)
        else
            printfn "\nfinal number of iterations: %d | root value: %f" iternum m
            m 
```

Enter fullscreen mode Exit fullscreen mode

用函数式方法编写二分法是一个挑战，因为我已经习惯了命令式和面向对象的编程范例。然而，我对可以用函数式语言实现的优雅的数学解决方案感到满意(试着用 C 实现这个方法)。

[Github 链接一个示例方程](https://github.com/eegodinez/numerical-methods/blob/master/Bisection_Method.fs)