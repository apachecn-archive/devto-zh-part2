# 你这周在黑什么？

> 原文：<https://dev.to/pancy/what-are-you-hacking-on-this-week-e20>

我几乎只在 Erlang 上做黑客。这是一种疯狂的语言，我不久前访问过，几乎马上就放弃了。在 FP 一年后重访，感觉我错过了很多。

**我从与 FP 合作中获得的优势**:

*   我很欣赏 ES6 和 JavaScript 的新语法。
*   我不能到处乱搞，让代码凭空工作。你可以用命令式语言来结束，你的代码会在你不知道的情况下运行。在 ML 和 Erlang 这样的语言中，你就是不能。
*   我开始抽象地思考...从树上看到木头。
*   我从未停止过对这样简单的排骨的思考:

```
list_length([]) -> 0;
list_length([Head | Rest]) -> 1 + list_length(Rest). 
```

Enter fullscreen mode Exit fullscreen mode

对比这个

```
def list_length(l):
    counter = 0
    for m in l:
        counter += 1

    return counter 
```

Enter fullscreen mode Exit fullscreen mode

关键是我意识到，设置一个计数器变量并像我们小时候第一次学习数数手指那样添加它并不是唯一的方法。(还记得你没有手指去数，或者完全忘记自己在哪里，而你的朋友一直在数的情况吗？这很好笑，但这些东西实际上导致了几十年后我们拔掉头发的大多数虫子)。