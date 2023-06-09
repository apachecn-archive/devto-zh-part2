# MojiScript 增强地图简介

> 原文：<https://dev.to/joelnet/an-introduction-to-mojiscripts-enhanced-map-3dpp>

[![World map](img/d7ac09895557ea4389c2fa788fbcbcea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--muenqqfd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h38jlrfkzqo771qvee7y.jpg)

MojiScript 在许多方面扩展和增强了`map`,比如支持可迭代和异步代码。这是对 MojiScript 的`map`提供的特性以及与传统的`map`的区别的介绍。

这篇文章的灵感来自 Twitter 上的一个很好的问题:

> ![𝕔𝕛 profile image](img/5840636d40c1afe2b030a77427c30387.png)𝕔𝕛@ dually 888![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ thepractical dev](https://twitter.com/ThePracticalDev)[@ Joel net](https://twitter.com/joelnet)但是香草 js 已经做了你想要做的为什么还要带一个 lib 来呢？😩2018 年 10 月 17 日下午 15:06[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1052576566750916608)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1052576566750916608)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1052576566750916608)

> ![𝕔𝕛 profile image](img/5840636d40c1afe2b030a77427c30387.png)𝕔𝕛@ dual ly 888![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ thepractical dev](https://twitter.com/ThePracticalDev)[@ Joel net](https://twitter.com/joelnet)字面上只是
> const evens = values . filter(x =>x % 2 = = = 0)；2018 年 10 月 17 日下午 15:08[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1052577088543297536)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1052577088543297536)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1052577088543297536)

# 传统地图

让我们从你应该已经熟悉的东西开始。这是最简单的形式。

```
const values = [1, 2, 3]
values.map(x => x * 2) //=> [2, 4, 6] 
```

Enter fullscreen mode Exit fullscreen mode

现在，在我们开始这个旅程之前，我建议将函数`x => x * 2`从`map()`调用中分离出来。

```
const double = x => x * 2
const values = [1, 2, 3]
values.map(double) //=> [2, 4, 6] 
```

Enter fullscreen mode Exit fullscreen mode

我知道我似乎在说一些琐碎而明显的事情，但这是我在阅读代码时很少看到的。

这是你可以用`map`、`filter`、`reduce`、`Promise.then`呼叫等等来做的事情。

# 语法

MojiScript 的`map`是一个独立的函数，而不是对象上的方法。你仍然有相同的`values`、`map`和`func`。但你称呼它的方式只是有一点点不同(但不会太多)。

```
// JavaScript
values.map(func)

// MojiScript
map (func) (values) 
```

Enter fullscreen mode Exit fullscreen mode

做出这种改变为轻松构建新功能提供了可能性。

```
const newFunc = map (func) // a new function is born!
newFunc (values) 
```

Enter fullscreen mode Exit fullscreen mode

# 映射迭代器

因为 JavaScript 的 map 是附加在`Array`上的方法，所以不容易和其他类型一起使用。

在这个例子中，我正在导入`range`，它是一个`Iterator`。

```
import range from 'mojiscript/list/range'
console.log(...range (1) (4)) //=> 1 2 3 
```

Enter fullscreen mode Exit fullscreen mode

可惜 JavaScript 的`map`不支持`Iterators`。

```
range (1) (4)
  .map (double) //=> Error: map is not a function​​ 
```

Enter fullscreen mode Exit fullscreen mode

即使我们施了魔法，我们也不能让它起作用...

```
Array.prototype.map.call(range (1) (4), double) //=> [] 
```

Enter fullscreen mode Exit fullscreen mode

在 JavaScript-land 中，`Iterator`在被映射之前必须先被转换为`Array`。

```
Array.prototype.map.call([...range (1) (4)], double) // [2, 4, 6]
//                        ----------------
//                      /
//     cast to an Array

[...range (1) (4)].map(double) //=> [2, 4, 6]
//---------------
//                 \
//                   cast to an Array 
```

Enter fullscreen mode Exit fullscreen mode

但是使用 MojiScript，`map`与`Iterators`没有问题，语法与映射一个`Array`相同。

```
map (double) (range (1) (4)) //=> [2, 4, 6] 
```

Enter fullscreen mode Exit fullscreen mode

# 映射字符串

相同的语法用于映射字符串中的字符。

```
const charCode = x => x.charCodeAt(0)

// JavaScript
Array.prototype.map.call('abc', charCode) //=> [97, 98, 99]

// MojiScript
map (charCode) ('abc') //=> [97, 98, 99] 
```

Enter fullscreen mode Exit fullscreen mode

# 节点列表

也支持节点列表！

```
// JavaScript
document.querySelectorAll('div[id]').map()
//=> Error: document.querySelectorAll(...).map is not a function

// MojiScript
const getId = element => element.getAttribute('id')
const divs = document.querySelectorAll('div[id]')

const ids = map (divs) (getIds)
//=> ['id1', 'id2', 'id3'] 
```

Enter fullscreen mode Exit fullscreen mode

# 也许吧

对于可空类型来说，`Maybe`类型是一个很好的选择。您可以使用一个`Maybe`类型来代替使用 null 并执行 null 检查。

**JavaScript:**

```
const upper = string =>
  string == null ? string : string.toUpperCase()

upper(null) //=> null
upper('abc') //=> 'ABC' 
```

Enter fullscreen mode Exit fullscreen mode

**MojiScript:**

`Maybe`可以消除大多数无效检查的需要。同样，语法与任何其他对`map`的调用相同。

```
import map from 'mojiscript/list/map'
import Just from 'mojiscript/type/Just'
import Nothing from 'mojiscript/type/Nothing'

const upper = map (string => string.toUpperCase())

upper (Nothing) //=> Nothing
upper (Just ('abc')) //=> Just ('ABC') 
```

Enter fullscreen mode Exit fullscreen mode

一些帮助方法可以让你轻松进出`Maybes` :

```
import Just from 'mojiscript/type/Just'
import { fromMaybe, fromNullable } from 'mojiscript/type/Maybe'
import Nothing from 'mojiscript/type/Nothing'

fromNullable (null) //=> Nothing
fromNullable ('abc') //=> Just ('abc')

fromMaybe ('') (Nothing) //=> ''
fromMaybe ('') (Just ('abc')) //=> 'abc' 
```

Enter fullscreen mode Exit fullscreen mode

这个主题太大了，无法在这里讨论。幸运的是，我在这里写了一整篇关于这个主题的文章: [NULL，“十亿美元的错误”，也许什么都没有](https://dev.to/joelnet/null-the-billion-dollar-mistake-maybe-just-nothing-1cak)

# 异步映射

MojiScript 的`map`也支持异步映射！

```
const double = x => x * 2

const asyncDouble = num => new Promise(resolve => {
  setTimeout(() => {
    console.log({ num })
    resolve(double(num))
  }, 1000)
})

map (asyncDouble) (range (1) (5))
//=> [2, 4, 6, 8, 10] 
```

Enter fullscreen mode Exit fullscreen mode

# 映射异步迭代表

MojiScript 的`map`也支持异步迭代器！

```
const timeout = seconds =>
  new Promise(resolve => setTimeout(resolve, seconds))

async function* asyncGen() {
  await timeout (1000)
  yield 1
  await timeout (1000)
  yield 2
  await timeout (1000)
  yield 3
}

const double = x => x * 2

const iter = asyncGen();

map (double) (iter)
//=> Promise([ 2, 4, 6 ]) 
```

Enter fullscreen mode Exit fullscreen mode

# 总结

魔咒可以`map` : `Array`、`Iterators`、`Async Iterators`、`Functors`、`Strings`。

MojiScript 的`map`也支持异步代码，这非常棒。

查看 [MojiScript](https://github.com/joelnet/MojiScript) 。挺牛逼的！[跳上 MojiScript Discord 聊天室，打个招呼吧！](https://discord.gg/Gg7ptD5)

我的文章是非常实用的 JavaScript，如果你需要更多的 FP，在这里关注我，或者在 Twitter [@joelnet](https://twitter.com/joelnet) ！

[![Cheers!](img/661c03d25961a6e3b1ecbe00d148b35a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PqqJmrxK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6jsy3a866frzp3u5oda0.jpg)