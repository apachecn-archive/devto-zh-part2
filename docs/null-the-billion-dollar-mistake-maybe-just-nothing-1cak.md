# 空，“十亿美元的错误”，也许什么都没有

> 原文：<https://dev.to/joelnet/null-the-billion-dollar-mistake-maybe-just-nothing-1cak>

[![Joker burning a huge pile of money](../Images/9cf4f0345fdcfc74710201fa60c1b43a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Hat4kzIV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c8q2xf9cx0wh2le4n3v0.jpg)

NULL 的创造者东尼·霍尔现在把 NULL 称为十亿美元的错误。即使空引用异常直到今天仍然困扰着我们的代码，我们仍然选择继续使用它。

出于某种原因，JavaScript 决定通过创建`undefined`来解决`null`的问题。

今天，我想用“也许”来演示这个问题的解决方案。

> 我称之为我的十亿美元错误。这是 1965 年零引用的发明。那时，我正在设计第一个面向对象语言(ALGOL W)的引用综合类型系统。我的目标是确保所有引用的使用都是绝对安全的，由编译器自动执行检查。但是我无法抗拒放入空引用的诱惑，仅仅是因为它太容易实现了。这导致了数不清的错误、漏洞和系统崩溃，在过去的四十年里，这可能造成了数十亿美元的痛苦和损失。——[东尼·霍尔](https://en.wikipedia.org/wiki/Tony_Hoare)

# 不要低估 NULL 的问题

在你读完这篇文章之前...我已经感觉到了，你想要点击页面，直接冲到评论区，大声说出“但是 NULL 对我来说从来都不是问题”。但是请停下来，慢下来，阅读和沉思。

来自[的 10 个错误中的 8 个来自 1000 多个项目的前 10 个 JavaScript 错误(以及如何避免它们)](https://rollbar.com/blog/top-10-javascript-errors/)是`null`和`undefined`问题。八个。出去。的。同ＴＥＮＵＴＯ

低估 NULL 就是被 NULL 打败。

# 零守卫

因为`null`带来的问题，我们不得不不断地保护我们的代码免受其害。未受保护的代码可能看起来像这样:

```
const toUpper = string => string.toUpperCase() 
```

这段代码容易出现空引用异常。

```
toUpper(null) //=> ​​Cannot read property 'toUpperCase' of null​​ 
```

所以我们被迫防范`null`。

```
const toUpper = string => {
  if (string != null) {
//    --------------
//                   \
//                    null guard
    return string.toUpperCase()
  }
} 
```

但是这很快变得冗长，因为可能遇到`null`的每个地方都必须被保护。

```
const toUpper = string => {
  if (string != null) {
//    --------------
//                   \
//                    duplication
    return string.toUpperCase()
  }
}

const toLower = string => {
  if (string != null) {
//    --------------
//                   \
//                    duplication
    return string.toLowerCase()
  }
}

const trim = string => {
  if (string != null) {
//    --------------
//                   \
//                    duplication
    return string.trim()
  }
} 
```

如果我们认为一个值与可能访问它的代码有一对多的关系，那么更有意义的是将防护放在*一个*而不是*多个*上。

# 可空类型

的。NET Framework 2.0 将可空类型引入了。网语。这个新的可空值可以在引用不为空的情况下被设置为空。这意味着如果`x`是一个可空类型，你仍然可以做类似于`x.HasValue`和`x.Value`的事情，而不需要得到一个`NullReferenceException`。

```
int? x = null
if (x.HasValue)
{
    Console.WriteLine($"x is {x.Value}")
}
else
{
    Console.WriteLine("x does not have a value")
} 
```

# 也许吧

`Maybe`类似于可空类型。该变量将始终有一个值，该值可能代表一个`null`，但它永远不会被设置为`null`。

对于这些例子，我将使用 MojiScript 中的`Maybe`。(同时查看[莫奈](https://github.com/monet/monet.js/blob/develop/docs/MAYBE.md)和[避难所](https://sanctuary.js.org/#maybe-type)，[民间故事](https://github.com/folktale/data.maybe)其他`Maybes`)。使用以下导入:

```
import { fromNullable } from "mojiscript/type/Maybe" 
```

`Maybe`是`Just`或`Nothing`的联合类型。`Just`包含一个值而`Nothing`是井...没什么。

但是现在值都被包装在`Maybe`里面了。要访问一个`Maybe`的值，你必须使用一个`map`函数。[谷歌的乐趣](https://www.google.com/search?q=javascript+functor) : `map`是`Maybe`成为 a 型`Functor`的原因。

如果你有这种感觉，那是因为这正是`Promise`的工作方式。不同的是`Promise`使用`then`，而`Maybe`使用`Map`。

```
const promise = Promise.resolve(888)
const maybe = Just(888)

promise.then(double)
maybe.map(double) 
```

相同但不同。

```
const toUpper = string => string.toUpperCase()

Just("abc").map(toUpper) //=> Just ('ABC')
Nothing.map(toUpper) //=> Nothing 
```

注意在上面的两种情况下，`toUpper`函数不再抛出`Error`。这是因为我们不再用一个`String`直接调用`toUpper`，而是用我们的`Maybe`映射它。

如果我们将应用程序中的所有类型转换为使用一个`Maybe`，那么所有的空保护都不再是必要的。

现在，`null`被保护在一个单独的地方，在`Maybe`类型中，而不是分散在整个应用程序中，任何可能访问值的地方。

这个`Maybe`是守卫在*上的一个*而不是*众多的*！

[![Neo vs many agent Smiths](../Images/14277f5568d0edc80c636bd894555132.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F4SwszJZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8i3rzn0tm7y9qepja9s4.jpg)

# 进出不定

但是当我们不能控制代码的时候，当我们必须发送或接收一个`null`值的时候呢？一些例子可能是将返回一个`null`的第三方库或需要传递`null`作为参数的库。

在这些情况下，我们可以使用`fromNullable`将空值转换为可能值，并且可以使用`fromMaybe`将其转换回可空值。

```
import { fromMaybe, fromNullable } from "mojiscript/type/Maybe"

// converting nullable values to a Maybe
fromNullable(undefined) //=> Nothing
fromNullable(null) //=> Nothing
fromNullable(123) //=> Just (123)
fromNullable("abc") //=> Just ("abc")

// converting Maybe to a nullable type
fromMaybe(Just("abc")) //=> 'abc'
fromMaybe(Nothing) //=> null 
```

你也可以像这样保护一个函数:

```
const toUpper = string =>
  fromNullable(string).map(s => s.toUpperCase()).value 
```

但是这有点冗长，最好将 Maybe 类型的安全性扩展到整个应用程序。在应用程序的入口和出口设置防护，而不是单独的功能。

一个例子是在你的 Redux 中使用一个 Maybe。

```
// username is a Maybe, initially set to Nothing.
const initalState = {
  username: Nothing
}

// your reducer is the gateway that ensures the value will always be a maybe.
const reducer = (state = initialState, { type, value }) =>
  type === 'SET_USERNAME'
    ? { ...state, username: fromNullable(value) }
    : state

// somewhere in your render
render() {
  const userBlock = this.props.username.map(username => <h1>{username}</h1>)
  const noUserBlock = <div>Anonymous</div> 
  return (
    <div>
    {fromMaybe (noUserBlock) (userBlock)}
    </div>
  )
} 
```

# JavaScript 类型强制

MojiScript 的`Maybe`可以利用 JavaScript 的隐式和显式强制来获得优势。

`Maybe`可以被隐含地胁迫成`String`。

```
// coercing to a String
console.log("a" + Just("b") + "c") //=> 'abc'
console.log("a" + Nothing + "c") //=> 'ac' 
```

`Maybe`可以显式地强制成一个`Number`。

```
Number(Just(888)) //=> 888
Number(Nothing) //=> 0 
```

甚至可以变成丝。

```
const data = {
  id: Nothing,
  name: Just("Joel")
}

JSON.stringify(data)
//=> {"id":null,"name":"Joel"} 
```

# 访问嵌套对象

让我们看看访问嵌套对象的常见任务。

我们将使用这些对象。一个是缺少地址，可以产生`nulls`。恶心。

```
const user1 = {
  id: 100,
  address: {
    address1: "123 Fake st",
    state: "CA"
  }
}

const user2 = {
  id: 101
} 
```

这些是访问嵌套对象的常用方法。

```
user1.address.state //=> 'CA'
user2.address.state //=> Error: Cannot read property 'state' of undefined

// short circuit
user2 && user2.address && user2.address.state //=> undefined

// Oliver Steel's Nested Object Pattern
((user2||{}).address||{}).state //=> undefined 
```

Prettier 似乎讨厌这两种技术，把它们变成了不可读的垃圾。

现在让我们尝试用`Maybe`访问嵌套对象。

```
import { fromNullable } from 'mojiscript/type/Maybe'

const prop = prop => obj =>
  fromNullable(obj).flatMap(o => fromNullable(o[prop]))

Just(user1)
  .flatMap(prop('address))
  .flatMap(prop('state)) //=> Just ("CA")

Just(user2)
  .flatMap(prop('address))
  .flatMap(prop('address)) //=> Nothing 
```

很多这种锅炉板可以减少一些辅助方法。

```
import pathOr from 'mojiscript/object/PathOr'
import { fromNullable } from 'mojiscript/type/Maybe'

const getStateFromUser = obj =>
  fromNullable(pathOr (null) ([ 'address', 'state' ]) (obj))

Just(user1).map(getStateFromUser) //=> Just ("CA")
Just(user2).map(getStateFromUser) //=> Nothing 
```

# 解耦地图功能

地图也可以和`Maybe`解耦。有很多库都有一个`map`函数，比如 Ramda，但是在这个例子中我将使用来自 [MojiScript](https://github.com/joelnet/MojiScript) 的函数。

```
import map from 'mojiscript/list/map'

const toUpper = string => string.toUpperCase()

Just("abc").map(toUpper) //=> Just ('ABC')
Nothing.map(toUpper) //=> Nothing 
```

```
import map from 'mojiscript/list/map'

const toUpper = string => string.toUpperCase()

map (toUpper) (Just ("abc")) //=> Just ('ABC')
map (toUpper) (Nothing) //=> Nothing 
```

这对于本节来说太大了，所以在这里分成了自己的文章:[MojiScript 增强地图介绍](https://dev.to/joelnet/an-introduction-to-mojiscripts-enhanced-map-3dpp)

# 重物起吊

提升是一种将`Applicatives`应用于函数的技术。在英语中，这意味着我们可以用我们的`Maybes`使用“普通”函数。谷歌的乐趣:`ap`是什么使`Maybe`型一个 [`Applicative`](https://www.google.com/search?q=javascript+applicative) 。

这段代码将使用`liftA2`、`A`表示`Applicative`，使用`2`表示函数中参数的数量。

```
import liftA2 from "mojiscript/function/liftA2"
import Just from "mojiscript/type/Just"
import Nothing from "mojiscript/type/Nothing"

const add = x => y => x + y
const ladd = liftA2 (add)

add (123) (765) //=> 888

ladd (Just (123)) (Just (765)) //=> Just (888)
ladd (Nothing) (Just (765)) //=> Nothing
ladd (Just (123)) (Nothing) //=> Nothing 
```

一些需要注意的事项:

*   功能`add`被执行。您可以使用任何一个`curry`函数来完成这项工作。
*   `add`由 2 个参数组成。如果是 3，我们就用`liftA3`。
*   所有参数必须是一个`Just`，否则返回`Nothing`。

所以现在我们不必修改我们的函数来理解`Maybe`类型，我们可以使用`map`和`lift`来将函数应用到我们的`Maybes`。

继续学习:[图片中的函子、应用程序和单子](http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html)做了一项令人难以置信的工作来解释这一点和更多！

# 也许是函数装饰器

有时，您希望保护单个函数不为空。这就是`maybe`函数装饰器派上用场的地方。

```
const maybe = func => (...args) =>
  !args.length || args.some(x => x == null)
    ? null
    : func(...args) 
```

用`maybe`函数装饰器:
保护你的函数不为空

```
const toUpper = string => string.toUpperCase()
const maybeToUpper = maybe(toUpper)
maybeToUpper("abc") //=> 'ABC'
maybeToUpper(null) //=> null 
```

也可以这样写:

```
const toUpper = maybe(string => string.toUpperCase()) 
```

了解关于函数装饰器的更多信息:

*   [函数装饰者:将回调转化为承诺，然后再转化回来](https://dev.toe274c7cf7293)
*   [函数 JavaScript:函数装饰者第二部分](https://dev.to/joelnet/function-decorators-part-2-javascript-4km9)

# TC39 可选 JavaScript 链接

这是提及目前处于第一阶段的 [TC39 可选链接提案](https://github.com/tc39/proposal-optional-chaining)的好时机。

可选的链接将允许您使用更短的语法来防止 null。

```
// without Optional Chaining
const toUpper = string => string && string.toUpperCase()

// with Optional Chaining
const toUpper = string => string?.toUpperCase() 
```

即使有了可选的链接，守卫仍然在*多*而不是*一*上，但至少语法是短的。

# 智慧

*   低估 NULL 就是被 NULL 打败。
*   前 10 个错误中有 8 个是空的和未定义的错误。
*   如果我们认为一个值与可能访问它的代码有一对多的关系，那么更有意义的是将防护放在*一个*而不是*多个*上。
*   通过消除`null`，有可能完全消除一整类错误(空引用异常)。
*   在代码中包含空引用异常是一种选择。

# 结束

有问题或意见吗？我很想听听！

[加入 MojiScript Discord 聊天室，打个招呼吧！](https://discord.gg/Gg7ptD5)

这比我最初想的要长一点。但是这是一个很难用一篇文章来概括的主题。

您也可以将`Maybe`与 MojiScript 的`map`一起使用。点击此处了解 MojiScript 地图有多棒...

我的文章是非常实用的 JavaScript，如果你需要更多的 FP，在这里关注我，或者在 Twitter [@joelnet](https://twitter.com/joelnet) ！

[![Cheers!](../Images/661c03d25961a6e3b1ecbe00d148b35a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PqqJmrxK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6jsy3a866frzp3u5oda0.jpg)