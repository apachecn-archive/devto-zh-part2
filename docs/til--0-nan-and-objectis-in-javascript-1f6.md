# TIL: +-0，NaN 和 Object.is 在 JavaScript 中

> 原文：<https://dev.to/stefanjudis/til--0-nan-and-objectis-in-javascript-1f6>

过去几天我在克卢日纳波卡，在那里我在优秀的 T2 js heroes 会议上发言。在[Mathias Bynens](https://www.youtube.com/watch?v=HULaOA5oJMY)的演讲【JS 开发者的 V8 内幕】(录音来自另一个事件)中，我看到了一个非常有趣的代码片段。

```
Object.is(-0, +0); 
```

Enter fullscreen mode Exit fullscreen mode

这一行在两个方面很吸引人——让我们来看看。

## JavaScript 中存在的两个零

第一个事实是 JavaScript 中的数字遵循 IEEE 浮点运算标准。这个标准有几种变体，JavaScript 使用基于 64 位的“双精度”,也称为“binary64”。

IEEE 754 定义了一个符号、一个有效符号和一个指数来描述每个有限的数字。理解这是如何工作的可能需要一些时间，但重要的事实是 JavaScript 数字中有一位(符号位)定义了一个数字是正还是负，这意味着`0`也可以是负的。

```
const posNumber = 1;
const negNumber = -1;
const posZero = +0;
const negZero = -0; 
```

Enter fullscreen mode Exit fullscreen mode

我对发现负零的第一反应是，我的代码中肯定没有这些，但是很好...当我对`-0.23`进行舍入时，我也将以负零结束，这使得负零更有可能出现在我的 JavaScript 中。

```
Math.round(-0.23); // -0 
```

Enter fullscreen mode Exit fullscreen mode

当你想比较正负 0 时，这变得很有趣，因为它们被同等对待。

```
-0 === +0 // true 
```

Enter fullscreen mode Exit fullscreen mode

[AbdulFattah Popoola](https://twitter.com/abdulapopoola) 给[写了一篇很好的文章](https://abdulapopoola.com/2016/12/19/why-javascript-has-two-zeros-0-and-0/)，甚至在[的“你不知道 JavaScript”系列](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch2.md#zeros)中有一个关于正负零的章节，如果你感兴趣的话，会有更详细的介绍。

*旁注:你可以用除法和结果`Infinity`来区分`-0`和`0`。*

```
1 / -0 === -Infinity    // true 
1 / 0 === Infinity      // true
-Infinity !== Infinity  // true 
```

Enter fullscreen mode Exit fullscreen mode

## object . is——比较无怪癖？

所以，和`===`的严格比较没有抓住两个零不一样的事实。你可能知道`NaN`也不等于`NaN`。

```
NaN === NaN // false

// you can use Number.isNaN as an alternative
Number.isNaN(NaN) // true 
```

Enter fullscreen mode Exit fullscreen mode

这些场合正是`Object.is`发挥作用的时候。在大多数情况下，它的行为与`===`相同，但它包括一些小的“改进”,使事情变得更有逻辑。

```
Object.is(-0, 0);    // false
Object.is(NaN, NaN); // true 
```

Enter fullscreen mode Exit fullscreen mode

这样做的缺点是，不是每个人都知道`-0`的存在，这意味着对于`-0.23`的舍入，`0`和`-0` **之间的差异可能会导致难以发现的错误**。这大概就是它在 JavaScript 中通常被忽略的原因。

我第一次看到`Object.is`是在马蒂亚斯的幻灯片中，它似乎并不经常被使用。

我马上想到的一个问题是，它是否和 T1 一样快。我创建了一个快速 JSPerf 来看看`Object.is`和`===`相比表现如何。在 Safari 和 Firefox 中,`Object.is`似乎比`===`慢很多，而在 Chrome 中差不多。那太有意思了！

如果有人对性能测试有任何意见，请告诉我。浏览器内部极其复杂，有时测试中发生的优化会使整个事情无效。

我也很想知道你是否在源代码中使用了`Object.is`！:)

### 附加资源

*   [数字在 JavaScript 中是如何编码的](http://2ality.com/2012/04/number-encoding.html)
*   [“说 JavaScript”中的“数字”章节](http://speakingjs.com/es5/ch11.html#two_zeros)
*   [为什么 JavaScript 有两个零:-0 和+0](https://abdulapopoola.com/2016/12/19/why-javascript-has-two-zeros-0-and-0/)
*   [对象.在 MDN 上](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is)