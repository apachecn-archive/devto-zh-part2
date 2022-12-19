# 删除注释

> 原文：<https://dev.to/gonedark/removing-comments--204k>

我在 [Boyscouting](https://dev.to/gonedark/are-you-a-boy-scout-b9) 上发表文章后，我发现大多数讨论都集中在代码注释上。具体来说，童子军的这一部分就是*删除*评论。删除评论的技巧也是我最喜欢的[代码清理推文](https://twitter.com/i/moments/948577473742233600)之一。

最近在一次代码审查中又出现了删除注释:

```
// filter out contacts that have unsubscribed
$contacts = $unsubscribedFilter->filter($contacts);

// filter out duplicate contacts
$contacts = $duplicateFilter->filter($contacts); 
```

Enter fullscreen mode Exit fullscreen mode

当我将代码压缩成以下代码时，审查者问我为什么“*删除了文档*:

```
$contacts = $unsubscribedFilter->filter($contacts);
$contacts = $duplicateFilter->filter($contacts); 
```

Enter fullscreen mode Exit fullscreen mode

我将回到*评论*和*文档*的区别。现在，有一个区别，我删除的确实是评论。

首先，让我感谢所有团队领导和工程经理大声疾呼:

> 删除评论？！！

是的，我建议从你的代码中删除注释。

> 你肯定不是指删除有用的评论吧？

那么，什么是*有用的*评论呢？

让我们支持并解决*评论*和*文档*之间的差异。文档是格式化的注释块(如 DocBlock、JavaDoc 等)。评论就是一切。

评论应该传达**为什么**，而不是*什么*或者*如何*。换句话说，如果有些东西不能通过阅读代码来传达，那么可能需要一个注释。

回到代码评审，没有任何注释转述了代码没有的内容。我可以从赋值和方法名推断出我们是“*过滤重复的联系*”。所以上面的代码注释是**不是**有用。事实上，我浪费时间读它。

对我来说，删除评论是为了获得清晰交流的代码。人们甚至可以重构代码以提高可读性。考虑:

```
$contacts->filterUnsubscribed(); 
```

Enter fullscreen mode Exit fullscreen mode

评论不仅有用，还可能误导人。我不断遇到过时的注释，它们没有随着代码的变化而发展。我最近需要修复以下提前结束的遗留代码。

```
foreach ($items as $item) {
    if ($item->published) {
        // we've hit the most recent item before this push, so stop looping
        exit;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注释说停止循环，但是代码存在。我浪费了几分钟辩论该相信哪个。考虑到这个 bug，我更新了代码以遵循注释并且*停止循环*。不管怎样，如果没有注释，这个 bug 就会被解决。加上错误的代码，它弊大于利。

这才是真正的意义所在——做好事。留下比你发现的更好的东西。这就是为什么它被称为[童子军](http://jason.pureconcepts.net/2015/01/are-you-a-boy-scout/)。如果你遇到可以删除的评论，就删除它。如果不能移除，看看能否重构代码，这样就可以移除注释了。未来的开发者会感谢你的。即使未来的开发者是你。

我最终看到了罗布·派克关于评论的一段话，这段话有效地总结了整篇文章。

> 【评论是】一件微妙的事情，需要品味和判断。出于几个原因，我倾向于删除评论。首先，如果代码是清晰的，并且使用了好的类型名和变量名，它应该解释清楚。第二，注释不会被编译器检查，所以不能保证它们是正确的，尤其是在代码被修改之后。一个误导性的评论可能会非常混乱。第三，排版问题:注释杂乱代码。

* * *

***想要更多清理技巧？**我正在写一本包含真实实践的“现场指南”,帮助你写出真正持久的代码。[报名](https://basecodefieldguide.com)提前进入。*