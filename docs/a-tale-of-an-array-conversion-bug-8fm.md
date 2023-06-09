# 一个数组转换错误的故事

> 原文：<https://dev.to/martyhimmel/a-tale-of-an-array-conversion-bug-8fm>

鉴于本的一条推文，我想分享一个最近的 bug 故事。

> ![Ben Halpern profile image](img/3814cc9cbc453345725bf2b5e578e8a0.png)本哈尔彭@本哈尔彭![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)我以前写 bug 代码。
> 
> 我现在还是这样，但我以前也是。2018 年 8 月 19 日下午 17:29[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1031231746677899264)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1031231746677899264)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1031231746677899264)

将数组数据从 PHP 转移到 JavaScript 是我经常做的事情。有时是键/值对，有时是简单的值列表。

在这个特殊的例子中，我需要两个数组的交集。

这里有一个人为的例子:

```
$main_colors = ['yellow', 'blue', 'orange', 'brown'];
$other_colors = ['pink', 'purple', 'blue', 'brown'];
$intersection = array_intersect($main_colors, $other_colors); 
```

Enter fullscreen mode Exit fullscreen mode

然后作为`json_encode($intersection);`传递回 JavaScript。

在 JavaScript 端，应该是一个值数组，但是它收到了一个对象。

等待...什么？

没过多久，我就意识到了问题所在，但这比我愿意承认的时间要长。😆

`array_intersect`保持第一个数组的索引顺序。换句话说，`$intersection`是一个数组，结构如下:

```
[1] => 'blue',
[3] => 'brown' 
```

Enter fullscreen mode Exit fullscreen mode

用 JavaScript 翻译成一个对象:

```
{
  "1": "blue",
  "3": "brown"
} 
```

Enter fullscreen mode Exit fullscreen mode

因为索引号保存在交集中，所以它不是一个值列表，而是一个键/值对列表。

修复非常简单，使用`array_values`，它返回一个数组的所有值。

```
$intersection = array_values(array_intersect($main_colors, $other_colors)); 
```

Enter fullscreen mode Exit fullscreen mode

在 PHP 中，`$intersection`被重构为有序/索引数组:

```
[0] => 'blue',
[1] => 'brown' 
```

Enter fullscreen mode Exit fullscreen mode

当 JavaScript 使用编码(使用`json_encode`)时，它看起来像:

```
['blue', 'brown'] 
```

Enter fullscreen mode Exit fullscreen mode

这也是我一开始所期待的。😀

> ![Marty Himmel profile image](img/860cd933403bd2ab7f0f3468c2f4c0e0.png)马蒂·希梅尔[@马蒂希梅尔](https://dev.to/martyhimmel)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ bendhalpern](https://twitter.com/bendhalpern)我也习惯写 bug 代码。
> 
> 我现在也是。
> 
> 现在，我只是习惯了写 bug 代码。😂2018 年 8 月 19 日下午 18:02[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1031240031074242560)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1031240031074242560)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1031240031074242560)