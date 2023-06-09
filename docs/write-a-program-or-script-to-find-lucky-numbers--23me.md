# 写一个程序或脚本来寻找幸运数字

> 原文：<https://dev.to/heikodudzus/write-a-program-or-script-to-find-lucky-numbers--23me>

# 幸运数字

在[“快乐数字”挑战](https://dev.to/peter/write-a-script-to-find-happy-numbers-133b)之后，这里是“幸运数字”:一些正整数数字是幸运的，因为它们通过了约瑟夫斯的筛子。

根据定义，1 是幸运的。

1 的后继者是 2。因此，每隔一个数字就会被删除:
1，3，5，7，9，11，13，15，17，19，21，...

下一个数字是 3。现在，每三个数字中就有一个被剔除:1，3，7，9，13，15，19，21，...

3 号是幸运的！它的后继者是 7。现在，每七个数字中就有一个被淘汰。诸如此类。

在这篇维基百科文章中阅读更多相关信息。

# 挑战

1)用你选择的语言编写一个程序或脚本，打印 1 到 n 之间的幸运数字。2)尽可能快地筛选 1 到 100 万之间的幸运数字。(也许测量时间而不打印结果是明智的。)

编辑:考虑从奇数开始。

我发现让它变快特别有趣。这似乎主要取决于所使用的数据结构。它必须满足不同的需求，这些需求有时似乎是矛盾的。我真的很好奇，你们在实现中使用了什么数据结构。

# 验证

有

*   23 个低于 100 的幸运数字，
*   153 个在 1000 以下，
*   1118 低于 10000，
*   8772 个低于 100000，
*   在前一百万个正整数中，71918 是幸运的。

1 到 300 之间的幸运数字:

```
[1, 3, 7, 9, 13, 15, 21, 25, 31, 33, 37, 43, 49, 51, 63, 67, 69, 73, 75, 79, 87, 93, 99, 105, 111, 115, 127, 129, 133, 135, 141, 151, 159, 163, 169, 171, 189, 193, 195, 201, 205, 211, 219, 223, 231, 235, 237, 241, 259, 261, 267, 273, 283, 285, 289, 297] 
```

Enter fullscreen mode Exit fullscreen mode