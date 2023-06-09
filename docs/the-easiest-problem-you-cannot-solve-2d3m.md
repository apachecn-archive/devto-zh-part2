# 你无法解决的最简单的问题。

> 原文：<https://dev.to/joelnet/the-easiest-problem-you-cannot-solve-2d3m>

**给定**以下代码:

```
const K = a => b => a
const cat = 'cat'
const dog = 'dog' 
```

这样调用`K`会输出`cat`

```
K(cat)(dog)
//=> "cat" 
```

不交换`cat`和`dog`的顺序，怎么调用`K`输出`dog`？

`cat`和`dog`必须恰好出现一次。

无法修改给定的的**。** 

```
// INVALID: Cannot swap order!
K(dog)(cat)

// NO CHEATING: May only appear once.
K.bind(null, dog)(cat)(dog) 
```

我知道解决这个问题的两种方法。再给我一个惊喜！

我还应该指定没有绑定、调用或应用，但是这些解决方案太有趣了:)

**提示:** `K`问题中的是 **K 组合子**， **SKI 组合子演算**的一部分。

**剧透在评论里！当心！**

下面贴了很多很棒的解决方案！很多我从未想过的事。我还创造了一些非常有创意的规则漏洞；)

这里有一个未被发现的解决方案。这是 base64 编码隐藏“剧透”。但是如果你好奇的话，你可以用你的控制台的`atob`功能来解码。

```
Syh4PT54KShjYXQpKGRvZyk= 
```

[![Cheers!](img/661c03d25961a6e3b1ecbe00d148b35a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PqqJmrxK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6jsy3a866frzp3u5oda0.jpg)