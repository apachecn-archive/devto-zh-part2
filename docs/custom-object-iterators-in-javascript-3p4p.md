# JavaScript 中的自定义对象迭代器

> 原文：<https://dev.to/stevebrownlee/custom-object-iterators-in-javascript-3p4p>

*最初发表于 stevebrownlee.com*

假设在 JavaScript 中有一个维护项目列表的对象，您希望该对象本身是可迭代的，但是该对象还有其他重要的属性——比如关于项目列表的元数据。

这里有一个有趣且完全原创的例子。

```
const se7enDwarves = {
    employment: "Miners",
    output: "Diamonds",
    members: new Map()
} 
```

Enter fullscreen mode Exit fullscreen mode

```
se7enDwarves.members.set(1, "Sleepy")
se7enDwarves.members.set(2, "Grumpy")
se7enDwarves.members.set(3, "Happy")
se7enDwarves.members.set(4, "Bashful")
se7enDwarves.members.set(5, "Sneezy")
se7enDwarves.members.set(6, "Doc")
se7enDwarves.members.set(7, "Dopey") 
```

Enter fullscreen mode Exit fullscreen mode

我现在有了一个代表七个小矮人的物体。它在一个可迭代的集合中保存他们的名字，还保存了关于他们的其他相关数据。我希望能够迭代对象本身，而不是必须键入可怕、麻烦的属性名称空间`members`。

我想看到所有矮人的名字，所以我使用 spread 语法来迭代对象并将项目放在一个数组中。

```
[...se7enDwarves]

VM2417:1 Uncaught TypeError: se7enDwarves is not iterable
    at <anonymous>:1:5 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript，在各种圣义里告诉我，不能那样迭代一个对象。

嗯。

幸运的是，JavaScript 允许开发人员执行所谓的元编程。这是语言的一个特性，允许我们修改底层数据结构的一些默认行为。我可以让一个对象是可迭代的。

```
se7enDwarves[Symbol.iterator] = function* () {
  const states = se7enDwarves.members.values()
  let state = null
  do {
    state = states.next().value
    if (typeof state !== "undefined") yield state
  } while (typeof state !== "undefined")
} 
```

Enter fullscreen mode Exit fullscreen mode

高级概述...我把对象做成可迭代的。我想我应该指出这一点，以防它在“自文档化代码”中不明显。

```
[...se7enDwarves]

(7) ["Sleepy", "Grumpy", "Happy", "Bashful", "Sneezy", "Doc", "Dopey"] 
```

Enter fullscreen mode Exit fullscreen mode

## 在 jsfiddle 上试试吧

我为你创建了一个 [JSFiddle，让你自己摆弄这段代码。](http://jsfiddle.net/chortlehoort/fhey7qdm)