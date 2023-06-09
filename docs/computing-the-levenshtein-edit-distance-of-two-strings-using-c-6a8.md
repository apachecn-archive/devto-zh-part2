# 使用 C#计算两个字符串的编辑距离

> 原文：<https://dev.to/elemarjr/computing-the-levenshtein-edit-distance-of-two-strings-using-c-6a8>

有时仅仅知道两个字符串是否相等是不够的。相反，我们需要了解它们有多大的不同。

比如*蚂蚁*和*阿姨*是两个不同的词。然而，不像*蚂蚁*和*解药*那么不同。*蚂蚁*和*阿姨*之间的“编辑距离”小于 _ 蚂蚁和解药之间的“编辑距离”。_ 对于编辑距离，我指的是将一个字符串转换成另一个字符串所需的编辑次数(删除、插入和替换)。

在这篇文章中，我分享了一个解决编辑距离问题的 Levenshtein 算法的实现。

## [T1】Levenshtein 算法](#the-levenshtein-algorithm)

1965 年， [Vladimir Levenshtein](https://en.wikipedia.org/wiki/Vladimir_Levenshtein) 创造了一个[美丽距离算法](https://en.wikipedia.org/wiki/Levenshtein_distance)。这是一个 C#实现。

```
using System;

public static class Levenshtein
{
    public static int Compute(string first, string second)
    {
        if (first.Length == 0)
        {
            return second.Length;
        }

        if (second.Length == 0)
        {
            return first.Length;
        }

        var d = new int[first.Length + 1, second.Length + 1];
        for (var i = 0;
            i <= first.Length;
            i++)
        {
            d[i, 0] = i;
        }

        for (var j = 0;
            j <= second.Length;
            j++)
        {
            d[0, j] = j;
        }

        for (var i = 1;
            i <= first.Length;
            i++)
        {
            for (var j = 1;
                j <= second.Length;
                j++)
            {
                var cost = (second[j - 1] == first[i - 1]) ? 0 : 1;
                d[i, j] = Min(d[i - 1, j] + 1, d[i, j - 1] + 1, d[i - 1, j - 1] + cost);
            }
        }

        return d[first.Length, second.Length];
    }

    private static int Min(int e1, int e2, int e3) => 
        Math.Min(Math.Min(e1, e2), e3);
} 
```

Enter fullscreen mode Exit fullscreen mode

这里是一些测试来确保这个实现的工作:

```
public class LevenshteinShould
{
    [Theory]
    [InlineData("ant", "aunt", 1)]
    [InlineData("fast", "cats", 3)]
    [InlineData("Elemar", "Vilmar", 3)]
    [InlineData("kitten", "sitting", 3)]
    public void ComputeTheDistanceBetween(string s1, string s2, int expectedDistance)
    {
        Assert.Equal(expectedDistance, Levenshtein.ComputeDistance(s1, s2));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 工作原理

Levenshtein 是一个[动态规划](https://en.wikipedia.org/wiki/Dynamic_programming)算法。

主要思想是填充矩阵 *m，*的条目，矩阵的两个维度等于正在计算编辑距离的两个字符串的长度。

在执行结束时，每个条目( *i* ， *j* )保存由第一个字符串的第一个 *i _ 字符和第二个字符串的第一个 _j* 字符组成的字符串之间的编辑距离。

让我们看一个例子:

[![](img/599472818f3eae3b6e47e7caba62fd37.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qmtnKXxY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.elemarjr.com/wp-content/uploads/2018/03/sunday_saturday.png)

正如您所看到的，右下角的单元格包含我们正在比较的两个字符串的编辑距离。

让我们一步一步来看看算法是如何工作的。这次让我们比较字符串“蚂蚁”和“阿姨”(编辑距离= 1)

首先，我们可以快速填充第一行和第一列。

[![](img/2691a45871f5ed8e778bd6325d8306a0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--01rff2Ax--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.elemarjr.com/wp-content/uploads/2018/03/ant_aunt-1.png)

然后，对于每个单元格，我们将比较第一个字符串和第二个字符串中的相应字符，然后选择这三个值中最小的一个:

*   左边单元格的值加 1
*   上面单元格的值加 1
*   左上方单元格的值，如果比较的字符不同，则加 1

[![](img/a56bdc49326e863f6ef40a932fe27858.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OVeYG0St--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.elemarjr.com/wp-content/uploads/2018/03/ant_aunt-2.png)

[![](img/1071cda148558cf65b73ea3ebcaed997.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--m_kYGgVa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.elemarjr.com/wp-content/uploads/2018/03/ant_aunt-3.png)

[![](img/3839eef2c952e6ea5524b30c40304451.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gyGVI_78--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.elemarjr.com/wp-content/uploads/2018/03/ant_aunt-4.png)

并且该过程将继续填充矩阵。

[![](img/d12ae44e2307b0d24e79abfac33088b9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ugQBSV4f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.elemarjr.com/wp-content/uploads/2018/03/ant_aunt.png)

## 优化时机

正如你可能在一步一步的过程中注意到的。我们只使用矩阵的两行来计算每个单元格的值。所以，我们不需要把所有的成本，所有的时间，都保存在内存里。

```
using System;

public static class Levenshtein
{
    public static int ComputeDistance(string first, string second)
    {
        if (first.Length == 0)
        {
            return second.Length;
        }

        if (second.Length == 0)
        {
            return first.Length;
        }

        var current = 1;
        var previous = 0;
        var r = new int[2, second.Length + 1];
        for (var i = 0;
            i <= second.Length;
            i++)
        {
            r[previous, i] = i;
        }

        for (var i = 0;
            i < first.Length;
            i++)
        {
            r[current, 0] = i + 1;
            for (var j = 1;
                j <= second.Length;
                j++)
            {
                var cost = (second[j - 1] == first[i]) ? 0 : 1;
                r[current, j] = Min(r[previous, j] + 1, r[current, j - 1] + 1, r[previous, j - 1] + cost);
            }

            previous = (previous + 1) % 2;
            current = (current + 1) % 2;
        }

        return r[previous, second.Length];
    }

    private static int Min(int e1, int e2, int e3) =>
        Math.Min(Math.Min(e1, e2), e3);
} 
```

Enter fullscreen mode Exit fullscreen mode

好多好多！

## 最后一句话

Levenshtein 是一个非常有趣和健壮的算法。将来，我将使用这个实现来改进我一直在工作的简单搜索库中的查询体验。

还是那句话，如果你有什么建议，欢迎在评论中分享。

用 C# 计算两个字符串的 Levenshtein (Edit)距离的帖子[最早出现在](http://www.elemarjr.com/en/2018/03/computing-the-levenshtein-distance-of-two-strings-using-c/) [Elemar JR](http://www.elemarjr.com/en) 上。