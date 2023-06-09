# 约会 Javascript

> 原文：<https://dev.to/squgeim/date-ing-javascript-30k2>

*这篇博客最初发表于[蛙跳科技](https://blog.lftechnology.com/date-ing-javascript-6203650b752c)。*

在 JavaScript 中处理日期有一些问题，我们不得不艰难地学习。我希望现在能以一种可控的方式打爆你的脑袋，而不是以后被一只讨厌的虫子打爆。

# 用字符串初始化日期不明确。

先说个例子。在 Chrome 上打开控制台，运行这个:

```
new Date('2018-3-14')
// Wed Mar 14 2018 00:00:00 GMT+0545 (Nepal Time) 
```

Enter fullscreen mode Exit fullscreen mode

很好，成功了。现在，在 Safari 上做同样的事情(如果你现在没有 Safari 的权限，请相信我):

```
new Date('2018-3-14')
// Invalid Date 
```

Enter fullscreen mode Exit fullscreen mode

等等，什么！？

ECMAScript 规范指出，用单个字符串参数调用 Date 构造函数将创建一个新的日期，其实现与`Date.prototype.parse`的实现相同。

如果字符串不是由`Date.prototype.toString`或`Date.prototype.toUTCString`生成的，那么`Date.prototype.parse`将依赖于实现。

基本上，如果您试图解析的字符串不是由`Date.toString()`或`Date.toUTCString()`给出的格式，您就完蛋了。

Chrome 只是想成为多余的，并尽力支持更多的格式；但它真正做的是给开发人员一种错误的安全感，认为他们的代码是有效的。这给了我们足够的“b”..但是……它在我的机器上有效。

好吧，那么 new Date()支持哪些格式呢？

那……也取决于你用的是什么浏览器。以下是说明书中的一段引文:

> 字符串的内容依赖于实现，但是旨在以一种方便的、人类可读的形式表示日期…

幸运的是，在这种情况下，人们一致同意使用 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 日期格式。这很简单，你可能已经在使用了:

```
2018–06–17 // Notice it's 06 not 6
2018–06–17T07:11:54+00:00
2018–06–17T07:11:54Z
20180617T071154Z 
```

Enter fullscreen mode Exit fullscreen mode

> 第一课:无论何时何地，都要使用 ISO 8601 日期时间格式。说真的。

***更新***
*自从 ES5 将[【ISO 8601】](https://www.ecma-international.org/ecma-262/5.1/index.html#sec-15.9.1.15)定义为 [JavaScript 规范](https://www.ecma-international.org/ecma-262/5.1/index.html#sec-15.9.3)本身以来，规范一直在更新，这不再仅仅是一个共识。*

# 整个时区的问题。

JavaScript 中的 Date 对象在内部只是一个数字，存储自 UTC 1970 年 1 月 1 日以来的毫秒数。

JavaScript Date 对时区和日光节约有非常基本的了解。它知道它所运行的机器的时区偏移量是多少，以及现在是否应用了 DST(这两者都依赖于浏览器，而浏览器又依赖于操作系统)。

它不能计算出不同时区的时间，也不能计算出特定日期对象对应的时区。事实上，没有办法将一个日期对象锁定到一个特定的时区，对日期对象的所有操作都是基于运行它的系统的本地时区。

Date 对象所做的一切都是基于它在每个 Date 对象中的内部毫秒数。所以时区唯一真正的影响只有在我们初始化内部数字的时候。

例如，当你说`new Date('2018-04-14')`时，日期对象应该理解什么？如果日期是 UTC 时间，则可能是`1520985600000`;如果日期是+05:45(尼泊尔时间),则可能是`1520964900000`。

知道 JavaScript 何时理解什么对解决时区问题至关重要。

以下是可能性的快速运行:

## 用 ISO 8601 日期时间字符串初始化日期。

```
const d = new Date('2018-04-14');
d.toUTCString();
// "Sat, 14 Apr 2018 00:00:00 GMT"
d.toString();
// "Sat Apr 14 2018 05:45:00 GMT+0545" 
```

Enter fullscreen mode Exit fullscreen mode

这是大多数日期时间相关问题的最大罪魁祸首。考虑你拿这个日期对象并对它做一个`getDate()`。结果会怎样？14 岁，对吗？

```
d.getDate();
// 14 
```

Enter fullscreen mode Exit fullscreen mode

这里有个问题:看看上面`d.toString()`输出中的时间部分。因为 Date 对象只使用本地系统的时区，所以它对 Date 对象所做的一切都基于本地时区。

如果我们在纽约的电脑上运行同样的代码会怎么样？

```
const d = new Date('2018-04-14');
d.toUTCString();
// "Sat, 14 Apr 2018 00:00:00 GMT"
d.toString();
// "Fri Apr 13 2018 14:15:00 GMT-0400" 
```

Enter fullscreen mode Exit fullscreen mode

今天是几号？

```
d.getDate();
// 13 
```

Enter fullscreen mode Exit fullscreen mode

仔细想想，这是显而易见的。伦敦的`2018–04–14 00:00`是尼泊尔的`2018–04–14 05:14`和纽约的`2018–04–13 14:15`。

事实证明，`2018-04-14`只是`2018-04-14T00:00:00Z`的一个简称。看到最后的`Z`了吗？这意味着给定的日期时间采用 UTC。

如果我们去掉 z，结果就不一样了

```
const d = new Date('2018-04-14T00:00:00+05:45');
d.toUTCString();
// "Fri, 13 Apr 2018 18:15:00 GMT" 
```

Enter fullscreen mode Exit fullscreen mode

哪个是真的，尼泊尔 4 月 14 日的午夜是伦敦 4 月 13 日的 18:15。不过，`d.getDate()`会给尼泊尔 14 个，但给尼泊尔以西的任何地方 13 个。

> 第 2 课:从 ISO 8601 初始化的日期日期时间字符串根据字符串上的时区信息进行解析，但它被初始化为本地时间。

## 日期未从字符串中初始化。

```
new Date(2018, 3, 14, 0, 0, 0, 0); 
```

Enter fullscreen mode Exit fullscreen mode

猜猜今天是几号。2018 年 3 月 14 日？不对。那是 2018 年 4 月 14 日。你看，在 JavaScript 世界里，月份是从`0`开始的。但是日子还是从`1`开始。不要问我为什么。

但好消息是，这是 2018 年 4 月 14 日，在世界各地的每台电脑上。

当你用参数直接初始化日期对象时，它总是被认为是在本地时区。

这是你的解决方案，比如生日只是一个日期，不在乎它是在哪个时区初始化的。对于大多数其他事情来说，如果事情发生的时间和地点很重要，那么最好还是选择 ISO 8601。

但是，如果您有一个需要从 UTC 初始化的日期时间呢？把它变成 ISO 8601 字符串？可能…，或者直接用`Date.UTC`。

```
// These two are equivalent:
const a = new Date('2018-04-16');
const b = new Date(Date.UTC(2018, 3, 16));
a.toString() === b.toString();
// true 
```

Enter fullscreen mode Exit fullscreen mode

> 第三课:如果你是偏执狂或者只使用本地时间，总是直接用参数初始化日期。

## 不是 ISO 8601 的琴弦。

如前所述，不符合 ISO 8601 格式的字符串在不同的浏览器之间会被含糊地解析。但是最常见的实现值得讨论。

Chrome 支持多种格式(值得注意的是，Node 使用与 Chrome 相同的 V8 引擎，所以结果是一样的):

```
new Date('April 13') // April 13 2001 Local timezone
new Date('5/13/2012') // May 13 2012 Local timezone
new Date('15/12/2009') // Invalid Date (Finally!) 
```

Enter fullscreen mode Exit fullscreen mode

火狐上:

```
new Date('April 13') // Invalid Date
new Date('5/13/2012') // May 13 2012 Local timezone
new Date('15/12/2009') // Invalid Date 
```

Enter fullscreen mode Exit fullscreen mode

火狐似乎更严格一点，但 Safari 是迄今为止最严格的。

这里要注意的是，所有这些都是本地时区的，就好像它们是直接从参数中初始化的一样。

但也有例外。考虑一下这个:

```
new Date('2018-04-16T00:00:00') 
```

Enter fullscreen mode Exit fullscreen mode

那是 ISO 8601 吗？几乎是，但不是。字符串中没有时区部分。所以这也属于暧昧一族。

在 Chrome 上:

```
new Date('2018-04-16T00:00:00')
// Mon Apr 16 2018 00:00:00 GMT+0545 (Nepal Time) 
```

Enter fullscreen mode Exit fullscreen mode

解析为本地时间。

在 Safari:

```
new Date('2018-04-16T00:00:00')
// Mon Apr 16 2018 05:45:00 GMT+0545 (+0545) 
```

Enter fullscreen mode Exit fullscreen mode

解析为 utc。

如果你只是在 Chrome 上测试，这会导致很多困惑和头痛。

> 我重申，只使用 ISO 8601 格式的日期字符串。一直都是。

***更新***
*[ES5 的规范规定](https://www.ecma-international.org/ecma-262/5.1/index.html#sec-15.9.1.15)没有时区部分的 ISO 8601 字符串应该被视为 UTC，但是 [ES6 的规范规定](https://www.ecma-international.org/ecma-262/6.0/#sec-date-time-string-format)它们应该被视为本地时间。Safari 只是在实现规范方面比较慢。*

# NVM，我就用瞬间吧。

首先，moment 并不是解决所有 JavaScript 日期问题的灵丹妙药。其次，内部日期对象中的许多警告仍然适用于 moment。

例如，这两种方式在 Safari 中都将给出无效的日期，但在 Chrome 上却可以正常工作:

```
new Date('2018-3-14')
// Invalid Date
moment('2018-3-14')
// Invalid Date 
```

Enter fullscreen mode Exit fullscreen mode

此外，我曾经看到过有超过一半包大小的项目。这可能不是你一开始关心的事情，但它肯定会在未来咬你一口，到那时再回头可能就太晚了。

我并不反对 moment，我经常使用它——但是在后端没有大小限制；我仍然没有找到一个令人信服的在前端使用它的用例。也许 DateFNS 将满足您的用例？

# 得出结论。

与对日期对象的不正确假设相关的错误是常见的，每个人最终都会面对它。获得对底层工作方式的更好理解，并建立和执行最佳实践可能是解决这些问题的唯一方法。我们已经分享了通过梳理代码和追踪 bug 来找到它下面的错误日期对象的经历。