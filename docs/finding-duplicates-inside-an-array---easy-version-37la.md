# 一个未排序的数组作为输入。

> 原文：<https://dev.to/s_awdesh/finding-duplicates-inside-an-array---easy-version-37la>

每当我准备面试时，我都从“数组和字符串”开始，然后转向链表、树，最终发现自己迷失在过多的我不太了解的主题中，如动态编程、图形等。

所以我们又来了，希望我这次能通过大部分，更重要的是更好地理解我已经知道的，因此写在这里。

问题:在一个数组中寻找重复项？

解决方案:我们来画一个简单的例子，看看这个问题中的预期是什么。

[![Example](img/6a72fc122a2191f48cb52d7e32f35077.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pKSZ8Hqd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9ut1e3qm8g2hwbiadhd2.jpeg)

我一听到/看到这个问题就有什么顾虑和疑问。

[![Follow-ups](img/754b4a940fe9b187e7f05e5a4ebede38.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Mej8yIsu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/blrt04e429ajs55xq50i.jpeg)

集合或哈希表类型的数据结构，这是我想到的大多数与数组/字符串相关的问题。

Set-:使用 Set 我可以只存储项目，而不存储它们的出现，这样可以吗？让我们看看:对于数组中的每一项，将它们添加到集合中，如果项已经存在于数组中，这意味着它是一个重复项，所以我们将把它添加到列表中。对数组中的所有项重复这个操作，并在最后返回列表。

为什么 Set/HashTable 不列出或另一个数组-:Set 和 HashTable 都在其中存储不同的项，它们给我们 O(1)的搜索时间，这是不同数据结构中最快的。当输入数组很大时，常量搜索运行时会非常重要。

[![Set & Map](img/b6fa6d60549a1dc5c5101cd997c60c4d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FdaQhSI_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rri7bnc7r0u0xr6wyy1h.jpeg)

让我们试着写下一个伪代码。

[![pseudo code](img/e01f194914834a69b62d8409d30f2d13.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MG3W1s2h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/92607peg08e3ofwohf1l.jpeg)

时间复杂度-: O(n)
空间复杂度-: O(n)

n:数组中的项数。

当我们只需要检查数组中是否存在重复时，如何更新我们的算法？

我们可以改变我们现有的算法，当我们在集合上迭代时，我们将只返回真实的布尔值，如果项目存在于集合内或其他地方，而不是填充列表内的项目。

类似的问题:

[![s_awdesh image](img/7969cd8e1635aacfa4a52476257fbc8c.png)](/s_awdesh) [## 寻找重复算法系列-一个字符串作为输入。

### 阿德什

#string #array #duplicates](/s_awdesh/finding-duplicate-character-inside-string-3100)[![s_awdesh image](img/7969cd8e1635aacfa4a52476257fbc8c.png)](/s_awdesh) [## 查找重复算法系列-一个排序后的数组作为输入。

### 阿德什

#array #twopointers](/s_awdesh/check-if-duplicate-exists-inside-a-sorted-array-3aa5)

这是查找重复项的最简单但不是最有效的实现方式。在下一篇文章中，我计划解决这个问题的不同版本。

敬请关注。