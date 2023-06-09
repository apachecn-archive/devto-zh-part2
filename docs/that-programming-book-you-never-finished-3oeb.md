# 那本你从未看完的编程书

> 原文：<https://dev.to/acoh3n/that-programming-book-you-never-finished-3oeb>

[![Books](img/5856909987e9860895b79804cec99878.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--maQiehMl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/svw6mcr9wgrmjqp3u8iu.jpg)

告诉我这听起来是否熟悉:

你走进一家书店，浏览一些书架，偶然发现一本关于你一直想学的一门学科的编程书。也许是关于机器学习或算法或其他一些很酷的技术。

你读了封底和介绍，你真的很激动。你对自己说:“我想我真的能得到这个”。还有，“当我写完这本书，我将拥有超能力”和“我将向同事们展示我是一个多么摇滚的明星”等等。

你回到家，打开那本将要改变你生活的书，翻到第 20 页，在看了太多的数学曲线之后，你开始睡着了。

然后你开始想一些其他的想法，比如“哦，不会又是数学吧！”和“我永远不会得到这个，我数学很烂！”以及“我想我就是傻”等诸如此类的废话。

如果我每次都有一块钱...总之。

所以我在这里告诉你，数学有一个肮脏的小秘密，可以帮助你读懂这些书:数学家喜欢用大得吓人的单词和符号来描述小而简单的概念。

让我们看一个例子。你可能会遇到看起来很吓人的适马符号:

[![Sigma](img/cfe844e8b07a080d14da3bc265e5fba8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3BLE2olE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7gkpblc3d74w4w4ih5yo.png)

我不知道是因为它很大，还是因为它看起来像埃及象形文字，但当我第一次看到它时，这个符号的某些东西让我吓坏了。但事实证明，这几乎是愚蠢的简单。

适马仅仅意味着“总结”或“相加”。你总结了什么？右边的东西。你可以从适马下面的数字(`i=1`这里)开始，直到适马上面指定的最后一个数字(`100`这里)。

所以这个特殊的适马等于:

```
1 * 2 + 2 * 2 + 3 * 2 ... + 100 * 2 = 10100 
```

Enter fullscreen mode Exit fullscreen mode

所以对我来说，考虑适马最简单的方法就是把它看作一个`for`循环，其中循环的下边界指定在适马之下，循环的上边界指定在适马之上。下面是它在 python 中的样子:

```
def sum(i):
  s = 0
  for n in range(1,i+1):
    s = s + (n*2)
  return s 
```

Enter fullscreen mode Exit fullscreen mode

我知道，它简单得令人失望。

这听起来像是过度简化了情况。一些数学概念难以理解，超出了凡人的理解范围。如果是这样的话，那我还没遇到过。

另一方面，这也不是一个“快速学习数学”的计划。为了理解这些数学概念，有时你必须递归地钻研原始概念所依赖的其他概念。这可能需要数小时、数天或更长时间才能完成，具体取决于概念。

但重点是一样的。只是些花哨的词语和符号。只要你继续使用字典，教程，视频，任何你能得到的东西，去定义这些单词和符号，你最终会明白的。有时尝试一下。

快乐学习。