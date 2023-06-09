# JS 性能:也许我们不应该总是使用数组

> 原文：<https://dev.to/nimmo/js-performance-perhaps-we-shouldnt-always-use-arrays-38ce>

感觉我花了相当多的时间去寻找数组中的东西。几乎和我花在寻找我的猫上的时间一样多！

考虑以下情况:

```
const catsArray = 
  [ {name: 'Aeris', isFavourite: true}
  , {name: 'Juri'}
  , {name: 'Dante'}
  , {name: 'Frankenstein'}
  ];

const findMyCat =
  requestedCat =>
    catsArray.find(({name}) => name === requestedCat);

findMyCat('Dante'); // {name: 'Dante'}
findMyCat('Marmalade'); // undefined 
```

Enter fullscreen mode Exit fullscreen mode

我想找到但丁(这是一个常见的现实问题)，为此我首先需要检查以确保艾瑞斯不是但丁，然后我必须检查朱里也不是但丁。有点奇怪，不过还好。如果我想检查我是否有一只叫橘子酱的猫，我必须先检查我所有的猫，以确保我没有。嗯。

也许我的数据可以在这里更好地表达？

```
const catsMap = new Map(
  [ ['Aeris', { name: 'Aeris', isFavourite: true }]
  , ['Juri', { name: 'Juri' }]
  , ['Dante', { name: 'Dante' }]
  , ['Frankenstein', { name: 'Frankenstein' }]
  , ['Aeris', { name: 'Aeris' }]
  ]
)

const findMyCat =
    requestedCat => catsMap.get(requestedCat)

findMyCat('Dante'); // {name: 'Dante'}
findMyCat('Marmalade'); // undefined 
```

Enter fullscreen mode Exit fullscreen mode

下次我想找但丁的时候，我可以直接找他。如果他像他应该的那样在 catsMap[《但丁》]那里，我会找到他，如果他不在，我不会-但我不需要浪费时间在路上看任何其他的猫。想象一下，当我有 10，000 只猫，没有一只叫橘子酱时，情况会有什么不同；我刚刚给自己省了一万(假设)手术！

## 更新:

我知道如果我在这里发布这条消息，有人会很快提供一个更好的替代方案，所以感谢 [Weston Wedding](https://dev.to/weswedding) 指出[地图实际上存在于 JS now](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) ！(我已经更新了这篇文章中的例子来反映这一点，所以如果你是第一次阅读这篇文章，你不需要担心“旧的”)

也感谢 [Oscar](https://dev.to/ibibgor) 接下来的性能测试，展示了在原始帖子中描述的两种方法之间的差异。< 3

[![](img/0fd7e2479f149ce1e22a905f04ba3548.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TkXrIgvv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6o6kztplpgpz26tvlh2c.png)