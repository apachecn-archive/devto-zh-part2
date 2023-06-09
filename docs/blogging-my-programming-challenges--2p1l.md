# 写博客我的编程挑战

> 原文：<https://dev.to/dellward/blogging-my-programming-challenges--2p1l>

最近，我申请了几十份开发人员的工作，但都没有成功。要么我会收到那封臭名昭著的“我们决定不再继续你的申请”的邮件，要么什么都没有。虽然我知道这个过程需要惊人的耐心，但如果我说我对自己的现状不生气，那我是在撒谎。我的简历够好了该死的！

但是当我回想起那些拒绝，我开始怀疑，我到底能不能通过第一次面试？这些创业公司和公司可能会让我免于尴尬。我一直专注于构建我的 React 组合，摆弄 X 和 Y 框架，以至于忽略了提高我的问题解决和算法设计技能。

我们大多数人都知道白板不是评估开发人员/软件工程师的最佳方式，有很多公司都不在乎。但事实是，有很多伟大的公司仍在使用这种方法。目前是这样的。

这就是为什么我决定每天进行一次代码挑战，并时常记录我的过程，与 dev.to 社区分享。我可以列出所有的理由来说明为什么这是有益的，但是阿里·斯皮特尔可以做得更好。

我不怀疑自己最终获得面试机会的能力，但我不确定自己是否有机会通过面试。这也很重要，因为我有计算机科学硕士学位，我觉得我应该比实际上更擅长算法。因此，尽管我知道如今学位有多不重要，但我想让它对我有意义。

说让我们开始编码吧！

## 两集之间

我的第一个挑战是在两局之间。给定两个数组，目标是创建一个返回整数的函数，该整数表示第一个数组中所有数字的倍数和第二个数组中所有数字的因数。这些数字将在两个集合之间

 *例如:

```
a1 = [2,4];
a2 = [16, 32, 96];
getNumbersBetween(array1, array2); // returns 3
// the three numbers, 4, 8, 16, are between the arrays a1 and a2, because they 
// are multiples of 2 and 4\. And they are also factors of 16, 32 and 96. 
```

Enter fullscreen mode Exit fullscreen mode

我决定解决这些挑战的方法是，如果我不能在 20 分钟内想出一个可行的解决方案，我会寻找解决方案，因为在现实世界中，我可能已经面试失败了。在接近我的时间限制之前，我至少知道我需要找到最小公倍数(LCM)。我只是不知道如何用算法找到它们。

我在 HackerRank 的讨论中看到了一个答案，完成了我的思考过程。解决方案是找到 LCM(是的，我很接近了！)和第二个数组的最大公约数(GCD)。

求最大公约数:

```
function gcd(a, b){
    while(b > 0){
        var temp = b;
        b = a % b;
        a = temp;
    }
    return a;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数连续寻找两个数的余数， **a** 和 **b** 。当 **b** 大于 0 时， **b** 的当前值将成为 **a** 的新值，余数将成为 **b** 的新值。一旦循环中断，该函数将返回 GCD，这将是用于计算余数的最后一个已知除数。

下面是这个函数如何工作的一个例子:

```
gcd(35, 14):

1st loop: 35 % 14 = 7
2nd loop: 14 % 7 = 0 //loop ends because the remainder is not greater than 0;

7 is the greatest common divisor 
```

Enter fullscreen mode Exit fullscreen mode

然而，我们有一个数字数组，你不可能一次找到两个以上数字的最大公约数。因此，您必须创建一个函数，将数组作为输入来迭代查找 GCD，一次两个元素:

```
const gcdOfArray = array => {
    result = array[0]
    for(var i = 1; i < array.length; i++){
        result = gcd(result, array[i])
    }
    return result;
} 
```

Enter fullscreen mode Exit fullscreen mode

既然我们知道了如何获取最大公约数，那么寻找最小公倍数就不那么痛苦了。LCM，相对于 **a** 和 **b** ，就是 **a** 和 **b** 的乘积除以它们的 GCD:

```
const lcm = (a, b) => {
    return (a * b) / gcd(a, b);
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们不只是处理两个数字，所以我们需要一个函数来寻找整个数组的 LCM:

```
const lcmOfArray = array => {
    result = array[0]
    for(var i = 1; i < array.length; i++){
        result = lcm(result, array[i])
    }
    return result;
} 
```

Enter fullscreen mode Exit fullscreen mode

既然我们能够找到 LCM 和 GCD，为了找到它们之间的所有数字，我们需要遍历 LCM 的倍数，直到最后一个倍数等于或大于 GCD:

```
var lcm = lcmArray(a1); //lcm of first array
var gcd = gcdArray(a2); //gcd of second array

for(var i = 1; i*lcm <= gcd; i++){
    /*
        loop though multiples of our least common multiples
        until it reaches the greatest common divisor
    */
} 
```

Enter fullscreen mode Exit fullscreen mode

使用这个循环，我们可以通过执行模表达式来检查当前倍数是否是 GCD 的一个因子。如果这个陈述是真的，那么把它算作两个集合 :
之间的一个数

```
var lcm = lcmArray(a1); //lcm of first array
var gcd = gcdArray(a2); //gcd of second array
var count = 0;

for(var i = 1; lcm * i <= gcd; i++){
    if(gcd % (lcm * i) == 0){
        count++
    }
}
return count // the number of integers between two sets 
```

Enter fullscreen mode Exit fullscreen mode

给你！

就这样，你可以在我的要点[这里](https://gist.github.com/DellWard/f2ad079dd9f20d723b33a2539ffb1f7e)检查整个功能。起初，看完整的代码对我来说很难理解。但是一旦分解，它实际上变得非常简单。如果你看到任何错误或知道更好的算法，我很想知道！直到下一次挑战...✌️🏾**