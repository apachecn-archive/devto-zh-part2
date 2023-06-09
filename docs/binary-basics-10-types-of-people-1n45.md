# 二元基础:10 种类型的人

> 原文：<https://dev.to/lefebvre/binary-basics-10-types-of-people-1n45>

正如一个老笑话所说，“有 10 种类型的人，一种理解二进制数如何工作，另一种不理解。”让我们把你放进“理解”组。

[![](img/41e1f410e86323ec93713443511e5446.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wvcLRnDo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgs.xkcd.com/comics/1_to_10.png)

二进制是计算机固有的一种编号系统。它的基数是 2。正常人使用的数字被称为十进制或十进制。

对于普通的十进制数，你用数字 0 到 9 来描述一个数。以下是你如何数出 10 件事:

```
1
2
3
4
5
6
7
8
9 
```

Enter fullscreen mode Exit fullscreen mode

现在你已经用完了数字。所以你在十位放一个“1 ”,在一位放一个“0 ”,得到:

```
10 
```

Enter fullscreen mode Exit fullscreen mode

尝试用二进制数相同的数量时，你首先应该意识到你只有两位数(基数为 2):0 和 1。因此计数看起来像这样:

```
1 
```

Enter fullscreen mode Exit fullscreen mode

现在你马上就没有数字了。所以你在 2 的位置放一个“1 ”,在 1 的位置放一个“0 ”,然后重新开始计数:

```
10
11 
```

Enter fullscreen mode Exit fullscreen mode

你又用完了数字。这种情况在二进制中经常发生，这也是为什么它对人们来说不是一个很好的计数系统。只要继续这个模式继续计数:

```
100
101
110
111

1000
1001
1010 
```

Enter fullscreen mode Exit fullscreen mode

我们将在这里停下来，因为我们现在已经数到十进制的“10”了。

解析这个数字的另一种方法是考虑每个位置如何表示数字的一个组成部分。

二进制 1010 的组件如下:

```
1 * 2^3 = 8
0 * 2^2 = 0
1 * 2^1 = 2
0 * 2^0 = 0 
```

Enter fullscreen mode Exit fullscreen mode

将 8 + 2 相加得到十进制数 10。

你可能听说过“8 位”这个术语，那么什么是 8 位数呢？因为位不是 0 就是 1，所以用二进制表示。因此，一个所有值都设置为 1 的 8 位二进制数是:

```
11111111 
```

Enter fullscreen mode Exit fullscreen mode

这是你能装进 8 位的最大数字。转换成十进制的话就是:

```
1 * 2^7 + 1 * 2^6 + 1 * 2^5 + 1 * 2^4 + 1 * 2^3 + 1 * 2^2 + 1 * 2^1 + 1 * 2^0 
```

Enter fullscreen mode Exit fullscreen mode

这可以分解为:

```
128 + 64 + 32 + 16 + 8 + 4 + 2 + 1 
```

Enter fullscreen mode Exit fullscreen mode

总共是 255，我相信你以前也见过这个数字。这是 Xojo 中使用 [UInt8 数据类型](http://developer.xojo.com/integer-size-specific)得到的最大值。

相反，你可以把十进制数转换成二进制数，方法是反复把它除以 2 并记下余数。当余数是偶数时，你写 0，当余数是奇数时，你写 1。然后你从下往上读号码。

所以要把十进制 10 转换成二进制:

10 / 2 = 5，余数为 0。0 甚至是这么写的一个“0”:

```
0 
```

Enter fullscreen mode Exit fullscreen mode

现在除以 5 / 2 得到余数为 1 的 2，这很奇怪:

```
0
1 
```

Enter fullscreen mode Exit fullscreen mode

现在除以 2 /2 得到 1，余数为 0:

```
0
1
0 
```

Enter fullscreen mode Exit fullscreen mode

最后，除以 1 / 2 得到 0，余数为 1，这是奇数。所以你得到了这个:

```
0
1
0
1 
```

Enter fullscreen mode Exit fullscreen mode

从下往上读，你会得到:1010

我希望你现在认为自己是理解二进制的一员！

**这篇文章最初出现在 [Xojo 博客](https://blog.xojo.com/2018/02/21/binary-basics-10-types-of-people/)上。**