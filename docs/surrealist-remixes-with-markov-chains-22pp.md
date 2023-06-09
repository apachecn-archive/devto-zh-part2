# 超现实主义与马尔可夫链的混搭

> 原文：<https://dev.to/dmfay/surrealist-remixes-with-markov-chains-22pp>

在这篇(和每篇)文章的底部有一个新的按钮。试试点击它！(如果你正在 [dev.to](https://dev.to) 或 RSS 阅读器上阅读这篇文章，你需要访问 di.nmfay.com 的[才能看到)](https://di.nmfay.com/markov-remix)

到目前为止，每个人都遇到过 Twitter 机器人和自动文本生成器，它们以几乎可以计算的方式组合单词。甚至有一个[子编辑](https://www.reddit.com/r/SubredditSimulator/)通过个人账户运行其他子编辑的用户生成内容，这些账户发布的帖子似乎模糊地代表了它们的来源，但要么难以理解，要么变得极其愚蠢。

自从我们用文字交流以来，人们就一直在玩文字游戏(和文字工作)。把语言拆开并以新颖的方式重新组合起来，这是诗人、哲学家和魔术师的领域，更不用说双关语、老爸笑话、语无伦次和单词沙拉了。

20 世纪初，与超现实主义运动有关的艺术家们玩了一种游戏，不同程度地是为了娱乐和灵感，叫做“精致的尸体”。每个玩家写一个单词(在这个版本中，每个人都提前分配了一个词类)或在一张暴露的纸上画画，然后将纸折叠起来，让下一个玩家看不到他们的作品。一旦每个人都有了机会，完整的句子或图片就会显现出来。这个游戏的名字来自于它的第一个记录结果:*le cadavre exquis boira le vin nouveau*，或者“精致的尸体应该喝新酒”。

超现实主义的种子落在肥沃的土地上，他们的思想传遍了整个艺术和文学世界，就像他们自己受到早期先锋派运动如象征主义和达达主义的影响一样。在本世纪中叶，作家和神秘学家如布里昂·吉辛和威廉·巴勒斯使用类似的技术在旧文本中发现新的含义。我们现代玩具唯一真正的区别是它们可以自己运行——这有点像恐怖电影中的占卜板，除了你可以自己看到它的工作原理。

有多种方法可以实现这种功能。在更原始的方面，你有“mad libs”算法，它选择随机值插入到已知的占位符中，就像许多 Twitter 机器人，如[@ god attributes](https://twitter.com/godtributes)或 [@bottest_takes](https://twitter.com/bottest_takes) 所做的那样。这种方法很快就遇到了明显的限制:替换的集合是有限的，它们被替换到的结构也是可预测的。

更先进的文本生成器是预测性的，以反映语料库本身组成的方式从文本主体或语料库中重新组织单词或短语:单词不是简单地随机混杂在一起，而是按照可识别的序列一个接一个。许多这样的生成器运行在马尔可夫链上，马尔可夫链是概率状态机，其中下一个状态只是当前状态的函数。

## 实现文本马尔可夫链

使用马尔可夫链生成文本的首要任务是分解原始语料库。匹配空白的正则表达式使这变得很容易，把它变成一个单词数组。下一步是建立国家之间的联系，这是事情开始变得有点复杂的地方。

文本马尔可夫链有一个重要的参数:前缀长度，它定义了有多少先前的状态(词)组成了当前状态，并且必须被评估以找到潜在的下一个状态。前缀必须包含至少一个单词，但是为了生成看起来自然的文本，根据语料库的长度，最佳位置往往是两到四个单词。如果前缀长度太短，输出很容易变成乱码；前缀太长或语料库太短，并且可能存在太少的潜在下一状态，使得链偏离原始文本。

将前缀映射到下一个状态需要数组上的滑动窗口。这更容易说明。这是一首 19 世纪的散文诗，被超现实主义者重新发现并赋予了新的名声(或恶名),超现实主义者认为这是对语言和仍在发展中的现代小说形式的解构，预示了他们自己的艺术意识形态。

> 他就像猛禽爪子的伸缩性一样公平；或者再一次，在下颈部区域的柔软部分的伤口中的肌肉运动的不确定性；或者更确切地说，永久捕鼠器总是由被困住的动物重置，它本身可以无限期地捕捉啮齿动物，即使藏在稻草下也能工作；最重要的是，在解剖台上一台缝纫机和一把雨伞的偶然相遇！

假设前缀长度为 2，映射可能开始采用这种形式:

```
"He is": ["as"],
"is as": ["fair"],
"as fair": ["as"],
"fair as": ["the"] 
```

Enter fullscreen mode Exit fullscreen mode

从第一个前缀(“他是”)开始，只有一个可能的下一个状态，因为单词“他是”在语料库中只出现一次。在到达下一状态时，活动前缀现在是“is as”，这同样只有一个可能的下一状态，依此类推。但当当前状态达到“作为”时，下一个要加的词可能是“伸缩性”、“不确定性”，或者是“偶然性”，之后会发生什么取决于走的路线。多个下一状态引入了发散的可能性；这也是为什么前缀太长，或者语料库太短，会导致无趣的输出！

因为前缀经常丢失最早的单词并追加下一个单词，所以它被存储为一个字符串化的数组，而不是一个串联的字符串。操作顺序是这样的:

1.  为当前字符串前缀数组选择一个潜在的下一个状态。
2.  `shift`前缀数组中最早的单词和`push`所选的下一个单词。
3.  新前缀数组。
4.  重复，直到厌倦，或直到没有可能的下一个状态。

## 混音！

如果你对实际代码感兴趣，它在 devtools 中的`remix.js`，或者你可以在[源代码控制](https://github.com/dmfay/blog/blob/master/assets/remix.js)中找到它。

马尔可夫链生成器通常不是交互式的；这就是“概率状态机”的“概率”部分发挥作用的地方。这使得这里的实现在设计上不完整。当只有一个可能的下一个状态存在时，状态机自己前进，但当有多个状态时，它允许用户选择如何继续。这与从头开始而不是选择一个随机的开头前缀相比，给了它更多的探索方向。陪审团仍然不知道是否有任何伟大的见解等待发掘，正如更具神秘感的运气编辑从业者所希望的那样，但与此同时，结果至少很有趣。