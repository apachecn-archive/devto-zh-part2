# MojiScript 入门:FizzBuzz(第 1 部分)

> 原文：<https://dev.to/joelnet/getting-started-with-mojiscript-fizzbuzz-part-1-2fji>

[![4 people looking at laptop](img/79cf7713f05c3d30adf768cc9c5d13fe.png)T2】](https://unsplash.com/photos/vdXMSiX-n6M)

# 什么是 MojiScript

MojiScript 是一种异步优先、固执己见的函数式语言，旨在与 JavaScript 引擎 100%兼容。

因为 MojiScript 是异步优先编写的，异步任务不仅变得琐碎，而且变得令人愉快。阅读这篇文章，了解 MojiScript 中关于异步的更多内容:[为什么异步代码如此令人困惑(以及如何让它变得简单)](https://dev.to/joelnet/why-async-code-is-so-damn-confusing-and-a-how-to-make-it-easy-3441)。

MojiScript 也是 JavaScript 引擎兼容的，这意味着**它可以在 node.js 和浏览器中运行，而不需要传输文件！**

即使它可以在任何 JavaScript 引擎中运行，您也会注意到 MojiScript 和 JavaScript 之间的显著差异。

# 你说显著差异？

你知道的 JavaScript 不会运行。但是除此之外...

[![screenshot of JavaScript errors](img/587f10299c8be528428ef7fa96914354.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MSUTQ_hC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h7waopmb48icskst1kwd.png)

学习 MojiScript 的时候最好忘记你所知道的关于 JavaScript 的一切。

但是不要担心，**有一些简单的方法可以与 JavaScript** 互操作。不过，我们不会在本文中讨论 JavaScript iterop。

# FizzBuzz

你应该已经熟悉 FizzBuzz 了。如果没有，游戏很简单:

> 写一个程序打印从 1 到 100 的数字。但是对于三的倍数打印“嘶嘶”而不是数字，对于五的倍数打印“嗡嗡”。对于同时是 3 和 5 的倍数的数字，打印“FizzBuzz”。- [嘶嘶声测试](http://wiki.c2.com/?FizzBuzzTest)

# 开始

我们将编写一个节点应用程序，所以我假设您已经安装了 node 和 git。

确保您有节点`v10.9.0` +

安装`mojiscript-starter-app`

```
git clone https://github.com/joelnet/mojiscript-starter-app.git
cd mojiscript-starter-app 
```

Enter fullscreen mode Exit fullscreen mode

确保它能够构建和运行。

```
npm ci
npm run build
npm start --silent 
```

Enter fullscreen mode Exit fullscreen mode

如果一切顺利，您应该会看到:

```
Hello World 
```

Enter fullscreen mode Exit fullscreen mode

# 格式保存

强烈建议将 Visual Studio 代码作为您的编辑器。它增加了一些不错的功能，比如保存时的格式。

如果你喜欢的另一个 IDE 在保存时没有格式化，那么你可以运行这个命令:

```
npm run watch 
```

Enter fullscreen mode Exit fullscreen mode

这一点很重要，因为如果你的格式关闭了，你的应用就不会`build`了。

您也可以运行此命令来修复您的格式。

```
npm run build -- --fix 
```

Enter fullscreen mode Exit fullscreen mode

正是像这样的小事情让 MojiScript 成为编写代码的乐趣！

# 文件

有两个重要的文件:

`src/index.mjs` -加载依赖项并启动应用程序。
`src/main.mjs`——你的 app 没有依赖，让测试变得容易。

注意:我们使用的是`.mjs`文件扩展名，这样我们就可以使用`node --experimental-modules`,这使我们能够在不传输文件的情况下使用`import`和`export`。

那是我们将要开始的地方。

应该是这样的:

```
import pipe from 'mojiscript/core/pipe'

const main = ({ log }) => pipe ([
  'Hello World',
  log
])

export default main 
```

Enter fullscreen mode Exit fullscreen mode

# 我们写点代码吧！

首先让我们创建一个从 1 到 100 的循环。

导入这两个函数:

*   `range` -创建一个从`start`到`end`的可迭代对象。
*   `map` -可迭代的映射。

```
import range from 'mojiscript/list/range'
import map from 'mojiscript/list/map' 
```

Enter fullscreen mode Exit fullscreen mode

将你的 main 修改成这样:

```
const main = ({ log }) => pipe ([
  () => range (1) (101),
  map (log)
]) 
```

Enter fullscreen mode Exit fullscreen mode

运行您的应用程序，您应该会看到控制台输出数字 1 到 100。

接下来，我想去掉那些“幻数”`1`和`100`。你不应该将价值直接硬编码到你的源代码中，至少在`src/main.mjs`中不应该。然而，您可以将这些值放在`src/index.mjs`中，因为它的职责是加载和注入依赖项并添加配置。

所以打开`src/index.mjs`把那些数字加到一个新的值`state`。

```
const state = {
  start: 1,
  end: 100
} 
```

Enter fullscreen mode Exit fullscreen mode

将状态添加到`run`命令

```
run ({ dependencies, state, main }) 
```

Enter fullscreen mode Exit fullscreen mode

现在`src/index.mjs`应该是这样的:

```
import log from 'mojiscript/console/log'
import run from 'mojiscript/core/run'
import main from './main'

const dependencies = {
  log
}

const state = {
  start: 1,
  end: 100
}

run ({ dependencies, state, main }) 
```

Enter fullscreen mode Exit fullscreen mode

回到`src/main.mjs`并修改`main`以使用`start`和`end`。

```
const main = ({ log }) => pipe ([
  ({ start, end }) => range (start) (end + 1),
  map (log)
]) 
```

Enter fullscreen mode Exit fullscreen mode

再次运行`npm start`以确保其工作正常。

# 让我们休息一下来谈谈管道

我想简单介绍一下`pipe`以及它是如何工作的。想象数据在`pipe`中移动，并在途中的每一步被转换(或变形)。

有了这个管道，如果我们让一个`4`通过它，它会变成一个`9`，然后是一个`18`。`log`执行一个副作用，根本不改变值，所以`18`将从管道返回。

```
const main = pipe ([
  //         |
  //         | 4
  //         ▼ 
  /*-------------------*/
  /**/  x => x + 5,  /**/
  /*-------------------*/
  //         |
  //         | 9
  //         ▼
  /*-------------------*/
  /**/  x => x * 2,  /**/
  /*-------------------*/
  //         |
  //         | 18
  //         ▼
  /*-------------------*/
  /**/      log,     /**/
  /*-------------------*/
  //         |
  //         | 18
  //         ▼
]) 
```

Enter fullscreen mode Exit fullscreen mode

所以在我们的 FizzBuzz 例子中，我们从`{ start: 1, end: 100 }`开始，把它变成`1`到`100`的`Iterable`，然后是`log`的每个值。管道将返回一个数组`1`到`100`。

# 回到 FizzBuzz

到目前为止，我们所做的就是创建一个数组。我们仍然需要计算每个数字的泡沫。

```
import allPass from 'mojiscript/logic/allPass'
import cond from 'mojiscript/logic/cond'

const isFizz = num => num % 3 === 0
const isBuzz = num => num % 5 === 0
const isFizzBuzz = allPass ([ isFizz, isBuzz ])

const fizziness = cond ([
  [ isFizzBuzz, 'FizzBuzz' ],
  [ isFizz, 'Fizz' ],
  [ isBuzz, 'Buzz' ],
  [ () => true, x => x ]
]) 
```

Enter fullscreen mode Exit fullscreen mode

如果`isFizz`和`isBuzz`都为真，则`isFizzBuzz`为`true`。

`cond`类似于 JavaScript 的`switch`。它比较函数或值，然后执行函数或值。最后一个条件`[ () => true, x => x ]`将总是返回`true`，然后将返回传递给`fizziness`的值。这是默认情况。

最后，添加`fizziness`态射到你的`main`

```
const main = ({ log }) => pipe ([
  ({ start, end }) => range (start) (end + 1),
  map (fizziness),
  map (log)
]) 
```

Enter fullscreen mode Exit fullscreen mode

# 功能组合

你可能已经注意到`map`被叫了两次。我们将从`1`到`100`循环两次。这没什么大不了的，因为 100 次迭代是微观的。但在其他应用中，这可能很重要。

我们可以使用一个`pipe`将`fizziness`和`log`组合在一起，并修改我们的`main`以使用我们新的`logFizziness`函数。

```
// logFizziness :: Function -> Number -> Number
const logFizziness = log => pipe ([
  fizziness,
  log
])

const main = ({ log }) => pipe ([
  ({ start, end }) => range (start) (end + 1),
  map (logFizziness (log))
]) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们只遍历迭代器一次。

我们最终的`src/main.mjs`应该是这样的:

```
import cond from 'mojiscript/logic/cond'
import pipe from 'mojiscript/core/pipe'
import map from 'mojiscript/list/map'
import range from 'mojiscript/list/range'
import allPass from 'mojiscript/logic/allPass'

const isFizz = num => num % 3 === 0
const isBuzz = num => num % 5 === 0
const isFizzBuzz = allPass ([ isFizz, isBuzz ])

const fizziness = cond ([
  [ isFizzBuzz, 'FizzBuzz' ],
  [ isFizz, 'Fizz' ],
  [ isBuzz, 'Buzz' ],
  [ () => true, x => x ]
])

const logFizziness = log => pipe ([
  fizziness,
  log
])

const main = ({ log }) => pipe ([
  ({ start, end }) => range (start) (end + 1),
  map (logFizziness (log))
])

export default main 
```

Enter fullscreen mode Exit fullscreen mode

# 第二部分

在第 2 部分中，我将回顾异步映射、`Infinity`、`reduce`、单元测试等等！这是 MojiScript 真正开始变得有趣的地方！

在这里或者推特上关注我，这样你就不会错过《T2》第二部了！

# 结束

你注意到你刚刚学了 currying，部分应用，函数合成，函子，和范畴理论吗？喘息！当然不是。那是因为我们玩得太开心了！

如果你认为 MojiScript 很有趣，给它一颗星[https://github.com/joelnet/MojiScript](https://github.com/joelnet/MojiScript)！如果你有问题，请在评论中提出来！

不要错过第二部分，在那里我揭示了生命的奥秘！

**看我的其他文章:**

[为什么异步代码如此令人困惑(以及如何让它变得简单)](https://dev.to/joelnet/why-async-code-is-so-damn-confusing-and-a-how-to-make-it-easy-3441)

[在把 90%的 JavaScript 扔进垃圾桶后，我是如何重新发现自己对它的热爱的](https://hackernoon.com/how-i-rediscovered-my-love-for-javascript-after-throwing-90-of-it-in-the-trash-f1baed075d1b)

[![Cheers!](img/661c03d25961a6e3b1ecbe00d148b35a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PqqJmrxK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6jsy3a866frzp3u5oda0.jpg)