# 为什么异步代码如此令人困惑(以及如何让它变得简单)

> 原文：<https://dev.to/joelnet/why-async-code-is-so-damn-confusing-and-a-how-to-make-it-easy-3441>

[![Yarn](../Images/849c024bed4acbe9574f08016fe0c1cf.png)T2】](https://pixabay.com/en/yarn-kordonek-weave-sewing-model-3219436/)

为什么 Javascript 中的异步代码如此复杂和混乱？有很多文章和问题来自人们，他们试图用他们的 bean 来包装它。

来自 SO 的一些精选问题...

*   [sleep()的 JavaScript 版本是什么？](https://stackoverflow.com/questions/951021/what-is-the-javascript-version-of-sleep)

*   [JavaScript 睡眠/等待，然后继续](https://stackoverflow.com/questions/16873323/javascript-sleep-wait-before-continuing)

*   [JavaScript 中有睡眠/暂停/等待功能吗？](https://stackoverflow.com/questions/7854820/is-there-a-sleep-pause-wait-function-in-javascript)

*   [在 JavaScript 中睡眠-动作之间的延迟](https://stackoverflow.com/questions/758688/sleep-in-javascript-delay-between-actions)

关于异步有成百上千的问题和文章，其中很多听起来像这样:

```
// How do I do this in JavaScript?
action1();
sleep(1000);
action2(); 
```

Enter fullscreen mode Exit fullscreen mode

这是对 JavaScript 工作方式的常见误解。

# 脏黑客强行同步

甚至还有强制同步的肮脏手段

> ![unknown tweet media content](../Images/a16150547ccde195e4600b9f34ddec9b.png)![Kent C. Dodds profile image](../Images/94f207e09e64133620a88b3ae5533c42.png)肯特 c .多兹[@肯特多兹](https://dev.to/kentcdodds)![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)[@ dferber 90](https://twitter.com/dferber90)有一招是我前阵子从 [@threepointone](https://twitter.com/threepointone) 那里学来的，可以帮到你:下午 17:45-2018 年 9 月 21 日

**不推荐**

# 问题不是异步代码

我花了很多时间思考 Javascript，有一次我有了一个愚蠢的想法。如果问题不是异步代码呢？**如果问题其实是同步代码怎么办？**

同步代码是问题？瓦特？

我经常开始同步编写代码，然后试着把我的异步例程放进去。这是我的错误。

异步代码不能在同步环境中运行。但是，逆没有问题。

[![async/async feature chart](../Images/09b5a18f50863e8e25adef8db1fc3ba6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lRgdSJMI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/44chigz6jmnk4el9ctbg.png)

这个限制只适用于同步代码！

[![Mind blown GIF](../Images/34b8db6e6addfa98f0280ed9eb1ae688.png)T2】](https://i.giphy.com/media/xT0xeJpnrWC4XWblEk/giphy.gif)

# 从一开始就异步写入

认识到这一点，我现在知道我应该异步开始我的代码。

所以如果我再次解决异步问题，我会这样开始:

```
Promise.resolve()
    .then(() => action1())
    .then(() => sleep(1000))
    .then(() => action2()) 
```

Enter fullscreen mode Exit fullscreen mode

或者用`async`和`await`...

```
const main = async () => {
  action1()
  await sleep(1000)
  action2()
} 
```

Enter fullscreen mode Exit fullscreen mode

`Promise`解决方案是...沃尔蒂。`async/await`更好，但它只是承诺链的语法糖。我还得四处撒上`async`和`await`，希望我没弄错。

有时候`async/await`会让人困惑。举个例子:这两行做完全不同的事情。

```
// await `action()`
await thing().action()

// await `thing()`
(await thing()).action() 
```

Enter fullscreen mode Exit fullscreen mode

还有伯克·霍兰德最近的一篇文章:

> [【开发到】Async/Await 和 forEach 绝望坑](https://dev.to/burkeholland/asyncawait-and-the-foreach-pit-of-despair-2267)。

# 如果没有区别呢？

所以我开始重新思考...如果异步代码和同步代码没有区别会怎样？如果我可以编写代码而不用担心我编写的代码是否是异步的，那该有多好。如果异步和同步语法相同会怎样？这可能吗？

这意味着我不能使用标准函数，因为它们只是同步的。`async/await`也出去了。这些代码并不相同，而且有其自身的复杂性。承诺会要求我到处写`then`、`then`、`then`...

所以，我再次开始思考...

# 异步功能组成

我爱爱爱函数式编程。所以我开始思考异步函数组合，以及如何将它应用到这个问题上。

如果这是您第一次听说函数合成，这里有一些代码可能会有所帮助。就是你典型的(同步)“hello world”函数构成。如果你想了解更多关于函数合成的知识，请阅读这篇文章:[Functional JavaScript:Function Composition For daily Use](https://hackernoon.com/javascript-functional-composition-for-every-day-use-22421ef65a10)。

```
const greet = name => `Hello ${name}`
const exclaim = line => `${line}!`

// Without function composition
const sayHello = name =>
  exclaim(greet(name))

// With function composition (Ramda)
const sayHello = pipe(
  greet,
  exclaim
) 
```

Enter fullscreen mode Exit fullscreen mode

这里我用 [`pipe`](https://ramdajs.com/docs/#pipe) 把`greet`和`exclaim`合成一个新的函数`sayHello`。

因为`pipe`只是一个函数，所以我可以修改它来异步工作。那么代码是同步的还是异步的就无关紧要了。

我必须做的一件事是将任何回调样式的函数转换为承诺样式的函数。幸运的是，node 的内置`util.promisify`使得这很容易。

```
import fs from 'fs'
import { promisify } from 'util'
import pipe from 'mojiscript/core/pipe'

// Synchronous file reader
const readFileSync = fs.readFileSync

// Asynchronous file reader
const readFile = promisify(fs.readFile) 
```

Enter fullscreen mode Exit fullscreen mode

现在如果我比较一个同步例子和一个异步例子，**没有什么不同**。

```
const syncMain = pipe([
  file => readFileSync(file, 'utf8'),
  console.log
])

const asyncMain = pipe([
  file => readFile(file, 'utf8'),
  console.log
]) 
```

Enter fullscreen mode Exit fullscreen mode

这正是我想要的！！！

即使`readFileSync`是同步的，`readFile`是异步的，但是语法完全一样，输出完全一样！

我不再需要关心什么是同步，什么是异步。在这两种情况下，我写的代码是一样的。

[![Banana says Wooo-Wooooo!](../Images/d56120b6df34c2527be734e013a9f14f.png)T2】](http://worldartsme.com/hooray-clipart.html)

# ESNext 建议:管道运营商

值得一提的是 [ESNext 提案:管道运营商](https://github.com/tc39/proposal-pipeline-operator)。

提议的管道操作符将让您以与`pipe`相同的方式“管道化”功能。

```
// pipeline
const result = message =>
  message
    |> doubleSay
    |> capitalize
    |> exclaim

// pipe
const result = pipe([
  doubleSay,
  capitalize,
  exclaim
]) 
```

Enter fullscreen mode Exit fullscreen mode

`Pipeline Operator`和`pipe`之间的格式如此相似，以至于我也可以毫无问题地在两者之间切换。

管道提案非常令人兴奋，但有两个警告。

1.  现在还没到，也不知道*如果*或者*什么时候*会来或者*会是什么样子*。巴别塔是一种选择。
2.  它还不支持`await`，当它支持时，很可能需要不同的语法来传输同步和异步函数。呸。

与管道操作符语法相比，我还是更喜欢`pipe`函数语法。

同样，管道将同步启动代码，这是我已经发现的一个问题。

因此，虽然我对这个功能感到兴奋，但我可能永远不会使用它，因为我已经有了更好的东西。这让我百感交集:|

# MojiScript

这就是你问我这到底是什么的地方...

```
import pipe from 'mojiscript/core/pipe'
//                ----------
//               /
//          WAT? 
```

Enter fullscreen mode Exit fullscreen mode

(好吧，你没问...但是你还在读书，我还在写作...)

MojiScript 是一种**异步优先**，自以为是的函数式语言，旨在与 JavaScript 引擎 100%兼容。

因为 MojiScript 是异步优先的，所以异步代码不会出现与典型 JavaScript 相同的问题。事实上，用 MojiScript 编写异步代码是一种享受。

您还可以将函数从 MojiScript 导入到现有的 JavaScript 应用程序中。在这里阅读更多:[https://github.com/joelnet/MojiScript](https://github.com/joelnet/MojiScript)

# MojiScript 异步示例

这是 MojiScript 的`pipe`的另一个很好的异步例子。这个函数提示用户输入，然后使用 [Axios](https://github.com/axios/axios) 在[星球大战 API 中搜索](https://swapi.co)，然后将格式化的结果写入控制台。

```
const main = ({ axios, askQuestion, log }) => pipe ([
  askQuestion ('Search for Star Wars Character: '),
  ifEmpty (showNoSearch) (searchForPerson (axios)),
  log
]) 
```

Enter fullscreen mode Exit fullscreen mode

如果这让你感到好奇，请在这里查看完整的源代码:[https://github . com/Joel net/moji script/tree/master/examples/star-wars-console](https://github.com/joelnet/MojiScript/tree/master/examples/star-wars-console)

# 我需要你的帮助！

这是我请求你帮助的部分。MojiScript 就像是全新的、前 alpha 的和实验性的，我正在寻找贡献者。你如何能有所贡献？玩它，或提交拉请求，或给我你的反馈，或问我问题，任何事情！前往[https://github.com/joelnet/MojiScript](https://github.com/joelnet/MojiScript)看看吧。

# 总结

*   异步代码不能在同步环境中运行。
*   同步代码在异步环境中运行良好。
*   从一开始就异步编写代码。
*   `for`循环是同步的。摆脱他们。
*   尝试用 [`pipe`](https://github.com/joelnet/MojiScript) 之类的东西组成异步函数。
*   [`pipe`](https://github.com/joelnet/MojiScript) 与 ESNext Pipeline 提案具有类似的功能，但目前已经推出。
*   玩 [MojiScript](https://github.com/joelnet/MojiScript) :)
*   MojiScript 目前处于试验阶段，所以不要把它投入生产！

【MojiScript 入门:FizzBuzz(第 1 部分)

多看我在[发展到](https://dev.to/joelnet)或者[媒介](https://medium.com/@joelthoms)上的文章。

在推特上关注我

[![Cheers!](../Images/e06285b8de57257da4f316477c6f3559.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TBAbVzBy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/md1oj03uisp93k36ekup.jpg)