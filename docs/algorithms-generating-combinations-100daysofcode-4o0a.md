# 算法:生成组合#100DaysOfCode

> 原文：<https://dev.to/rrampage/algorithms-generating-combinations-100daysofcode-4o0a>

我们可以用多少种不同的方法从一组`n`对象中选择`r`对象？在数学中，这被称为[组合](https://en.wikipedia.org/wiki/Combination)。

[![Combinations of 5 objects, taken 2 at a time](../Images/242919a630372836b81de6c5f3a4aba5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7zbXgEyv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w8utl55xbryxdvolbkdx.jpg)

组合数的公式为:
[![](../Images/f5fec2e8ec747c44a4041cbd07938430.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---_0kzL1U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wikimedia.org/api/rest_v1/media/math/render/svg/813f7124a61dac205542db3f8491b36cb306453a) 
其中，`n!`表示一个数的阶乘，该数是从 1 到 n(包括 1 和 n)的所有数的乘积。

### 前奏:计算一个数的阶乘的函数

```
public static long factorial(int n) {
    long res = 1L;
    for (int i = 1; i <= n; i++) {
        res *= i;
    }
    return res;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 计算组合

那很简单！现在让我们继续计算给定的`n`和`r`
的组合数

```
public static long combinations(int n, int r) {
    if (r < 0) {
        return 0;
    }
    long res = 1L;
    if (r > n - r) {
        r = n - r;
    }
    for (int i = 0; i < r; i++) {
        res *= (n - i);
        res /= (i + 1);
    }
    return res;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个算法是做什么的？回想一下，我们需要找到形式为`n(n-1)...(n-r+1)/1.2...r`的`n!/r!(n-r)!`。类似于`factorial`，我们将结果初始化为`1`并乘以`n-i`再除以`i+1`。这会导致分数吗？不。这是因为首先，我们乘以`n`并除以`1`。接下来，我们乘以`n-1`再除以`2`。现在，`n`或`n-1`必须是偶数(因为它们是连续的数字)。同样，接下来当我们除以 3 时，`n`、`n-1`和`n-2`中的一个必须能被 3 整除。

在上面的代码中，我们还利用了`combinations(n,r) = combinations(n,n-r)`的数学属性。这样，我们可以减少计算组合的运算次数。

### 生成组合

计算组合的数量并不困难！现在，让我们生成所有的组合。

*   给定`n`和`r`，我们将打印出所有的组合。
*   对于`n`对象，我们将使用从 0 到(n-1)的数字。
*   此外，我们将按照**字典顺序**生成它们，这是排序顺序的数学说法。
*   最后，在包含 a 和 b 的组合中，如果 a < b，我们将打印`a b`而不是`b a`。形式上来说，如果 a[k]和 a[k+1]是生成组合中的第 k 个和第(k+1)个元素，a[k] < a[k+1]对所有 k

例如，给定 n = 4，r = 2，我们有:

```
0 1
0 2
0 3
1 2
1 3
2 3 
```

Enter fullscreen mode Exit fullscreen mode

即 6 种组合。

注意，我们有`0 1`而没有`1 0`。这是因为我们是按字典顺序生成每个组合的，并且我们取每个组合的最小值。

#### 生成特定值的`r` (r = 2)

如果我们有一个特定的`r`值，比如说 2，代码将包含 2 个`for`循环，比如:

```
for (int i = 0; i < n-1; i++) {
    for (int j = i+1; j < n; j++) {
        println(i + " " + j);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们的第一个循环变量`i`从 0 到 n-2，下一个变量`j`从`i+1`到 n-1。为什么这样这是因为我们有取字典式最小组合的要求，所以`i < j`不受我们的约束。

如果`r`的值是固定的，我们可以简单地创建 r 个`for`循环。但是它不是固定的...

#### 概化！

现在，让我们继续讨论主要目标——生成一次获取`r`个`n`数字的组合。*这一部分会有点冗长，因为我已经概述了我是如何得到正确的代码的。如果你只对算法感兴趣，可以直接跳到文章的底部*

如果我们注意到前面的`r = 2`代码，我们的第一个组合总是`0 1`作为`i = 0, j = 1`。同样，如果`r`是 3，我们的第一个组合将是`0 1 2`。有模式！

**通过创建大小为`r`的数组`a`，我们可以生成第一个组合为`0 1 2 .. r-1`。**我们已经准备好了第一个组合。其余的呢？不知何故，如果我们增加数组中的元素，我们将生成组合...

再次查看`r = 2`案例，注意**的最后一个**组合是`n-2 n-1`。同样，对于`r = 3`，是`n-3 n-2 n-1`。因此，对于`r`元素，它将是`n-r+1 n-r+2 .. n-1`。还有一个洞察- **恰好有一个以`n-r+1`** 开头的组合。如果我们的数组的第一个元素到达`n-r+1`，我们就完成了！

**我们现在有一个函数的终止条件:`a[0] == n-r+1`**

到目前为止，我们得到的代码看起来像:

```
int n; // Given
int r; // Given
int[] a = new int[r]; // Initialize array of size r
for (int i = 0; i < r; i++) {
    a[i] = i; // Initialize array with first combination
}

while (a[0] < n-r+1) { // Our termination condition
    // DO SOMETHING!
} 
```

Enter fullscreen mode Exit fullscreen mode

我们有一个`while`循环来检查终止条件。为了终止循环，我们需要从第一个组合稳步前进到最后一个组合。因为我们是按字典顺序生成元素的，所以数组的最后一个元素必须先递增。然后是倒数第二个元素，依此类推。

在我们之前的`n = 4, r = 2`示例中，我们有

```
0 1
0 2
0 3
1 2
1 3
2 3 
```

Enter fullscreen mode Exit fullscreen mode

在`0 3`之后，我们得到`1 2`。这意味着一旦`r-1`元素(最后一个元素)达到最大值，我们将`r-2`元素从 0 递增到 1，并将`r-1`元素的值重置为`a[r-2]+1`，因为它必须总是比`r-2`元素至少大 1(根据我们的约束)。转到我们的伪代码，让我们把它添加到`while`循环

```
int i = r-1; // variable i keeps track of which element in array we are incrementing
while (a[0] < n-r+1) { // Our termination condition
    if (i > 0 && a[i] == n-r+1) {
        i = i-1; //If a[i] has reached the max allowable value, decrement i and move to next element in array
    }
    printArray(a); // pseudo-code to print out the combination
    a[i] = a[i]+1; // increment
    if (i < r-1) {
        a[i+1] = a[i]+1; // Reset `i+1` element as previous element + 1, according to our constraints
        i = i+1; // Once you have reset the i+1 element, it is no longer < n-r+i and hence, we can move it back to old value
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们有一个索引变量`i`,我们用它来检查数组中哪个元素要增加。在上面代码的第一个`if`中，我们检查`a[i]`是否达到了它的最大值`n-r+i`。如果是，我们减少`i`，因为`a[i]`不能再增加。移出`if`，然后我们打印组合和增量`a[i]`。现在，如果`i`不再是`r-1`，即不再是`a`的最后一个元素，我们必须将其重置为`r-1`，并将`a[r-1]`的值设置为`a[r-2]+1`。这适用于`r=2`。万岁！我们已经将前面章节中的`for`循环抽象成一个带有一些条件的`while`循环。

但是这对`r > 2`有用吗？不...我们需要一个小的改变来使它工作！将循环中的`if`语句改为`while`循环，我们就完成了！在第一次循环的情况下，我们需要找到小于`n+r-i`的最大值`i`。例如`n=5, r=3`我们有:

```
0 1 2
0 1 3
0 1 4
0 2 3
0 2 4
0 3 4
1 2 3
1 2 4
1 3 4
2 3 4 
```

Enter fullscreen mode Exit fullscreen mode

当我们从`0 3 4`移动到`1 2 3`时，`i = 2` (a[2] = 4)和`i = 1` (a[1] = 3)都处于最大值。我们需要搬到`i = 0`。类似地，第二个`if`必须是一个`while`循环，因为一旦我们为最小值`i`增加了 a[i],我们需要重置数组的外部元素来维持我们的约束。

### 我们最后的代号:

```
int[] a = new int[r];
// initialize first combination
for (int i = 0; i < r; i++) {
    a[i] = i;
}
int i = r - 1; // Index to keep track of maximum unsaturated element in array
// a[0] can only be n-r+1 exactly once - our termination condition!
while (a[0] < n - r + 1) {
    // If outer elements are saturated, keep decrementing i till you find unsaturated element
    while (i > 0 && a[i] == n - r + i) {
        i--;
    }
    printArray(a); // pseudo-code to print array as space separated numbers
    a[i]++;
    // Reset each outer element to prev element + 1
    while (i < r - 1) {
        a[i + 1] = a[i] + 1;
        i++;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 终止证明

现在我们有了我们的算法，我们如何证明它终止？在我们的外部 while 循环的每次迭代中，我们增加具有最大索引 I 的数组元素，该元素尚未达到值`n-r+i`，同时保持我们的约束。**由于字典排序的原因，我们之前的组合总是小于我们当前生成的组合。**因为只有有限数量的组合，直到我们到达我们的“最后”组合，我们可以说我们的算法将终止。

### Meta:

我今天带着这个问题开始了我的 100 天代码挑战。我将创建一个单独的帖子来解释我的动机和计划。

关于这个生成组合的问题陈述，我在最初从`r=2`案例转移到一般案例时遇到了一些麻烦。我花了一些时间找到正确的终止条件。我很高兴最终的算法比较紧凑。稍后我还想对这个算法做一个正确性证明。