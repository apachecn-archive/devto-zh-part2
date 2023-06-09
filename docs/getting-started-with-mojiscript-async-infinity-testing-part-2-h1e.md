# MojiScript 入门:异步、无限、测试(第 2 部分)。

> 原文：<https://dev.to/joelnet/getting-started-with-mojiscript-async-infinity-testing-part-2-h1e>

[![Infinity Loop](img/4c3bcbb02d213d94fd3b1d3bdcabc74b.png)T2】](https://dribbble.com/shots/2245706-Infinity-Loop)

这是 MojiScript: FizzBuzz(第 1 部分)的第 2 部分。在第 1 部分中，我们使用 MojiScript 创建了一个基本的 FizzBuzz 应用程序。

# 跳过了 Part 1？

建议从第 1 部分开始，但如果你不想，这是如何赶上:

```
# download mojiscript-starter-app
git clone https://github.com/joelnet/mojiscript-starter-app.git
cd mojiscript-starter-app

# install, build and run
npm ci
npm run build
npm start --silent 
```

Enter fullscreen mode Exit fullscreen mode

将此复制到`src/index.mjs`

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

将此复制到`src/main.mjs`

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

运行`npm start --silent`以确保它仍然工作。

# 让乐趣开始吧！

这是所有有趣的事情发生的地方。

如果我想让 FizzBuzz 去`Infinity`呢？如果我用`Infinity`运行代码，我的控制台会因为日志而变得疯狂，CPU 会是 100%。我不知道我是否能阻止它，但我不想知道。

所以我想添加的第一件事是每个日志之间的延迟。这将拯救我的理智，所以如果我在等待`Infinity`到来时变得不耐烦，我可以按 CTRL-C 键。

正如我在第 1 部分中所说的，[异步任务不仅变得琐碎，而且成为使用](https://dev.to/joelnet/getting-started-with-mojiscript-fizzbuzz-part-1-2fji)的乐趣。

在顶部加一个`import`。

```
import sleep from 'mojiscript/threading/sleep' 
```

Enter fullscreen mode Exit fullscreen mode

然后只需将`sleep`命令滑入`logFizziness`即可。

```
const logFizziness = log => pipe ([
  sleep (1000),
  fizziness,
  log
]) 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。说真的。只需添加一个`sleep`命令。试着想象一下用 JavaScript 会有多复杂。

再次运行这个应用程序，每次 1 秒钟就能看到气泡流出来。

既然我已经不担心我的游戏机会爆炸，如果我想数到`Infinity`我所要做的就是改变...

```
// change this:
const state = {
  start: 1,
  end: 100
}

// to this:
const state = {
  start: 1,
  end: Infinity
} 
```

Enter fullscreen mode Exit fullscreen mode

你看我们可以这样做，因为`range`是一个`Iterator`而不是一个`Array`。所以它会在整个范围内一次枚举一个数字！

但是...`map`会把那个`Iterator`变成一个`Array`。所以最终`map`会爆炸我们的记忆。如果我的内存用完了，我该如何运行这个到`Infinity`？

好了，那么让我们扔掉正在慢慢创建的`Array`地图。

这就是`reduce`派上用场的地方。`reduce`会让我们控制输出值是多少。

```
// this is what map looks like
map (function) (iterable)

// this is what reduce looks like
reduce (function) (default) (iterable) 
```

Enter fullscreen mode Exit fullscreen mode

这不是唯一的区别，因为`reduce`的`function`还需要 1 个额外的参数。让我们比较一下`map`的函数和`reduce`的函数。

```
const mapper = x => Object
const reducer = x => y => Object 
```

Enter fullscreen mode Exit fullscreen mode

因为第一个参数是 reduce 的累加器，我不关心它，我可以忽略它。

```
// instead of this:
logFizziness (log)

// I would write this:
() => logFizziness (log) 
```

Enter fullscreen mode Exit fullscreen mode

我只需要把这个人放在最上面。

```
import reduce from 'mojiscript/list/reduce' 
```

Enter fullscreen mode Exit fullscreen mode

我还需要输入一个默认值`(0)`，然后我可以将`main`转换成这个:

```
const main = ({ log }) => pipe ([
  ({ start, end }) => range (start) (end + 1),
  reduce (() => logFizziness (log)) (0)
]) 
```

Enter fullscreen mode Exit fullscreen mode

我们不再有任何内存问题，因为没有创建`Array`！

最终的`src/main.mjs`应该是这样的:

```
import cond from 'mojiscript/logic/cond'
import pipe from 'mojiscript/core/pipe'
import range from 'mojiscript/list/range'
import reduce from 'mojiscript/list/reduce'
import allPass from 'mojiscript/logic/allPass'
import sleep from 'mojiscript/threading/sleep'

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
  sleep (1000),
  fizziness,
  log
])

const main = ({ log }) => pipe ([
  ({ start, end }) => range (start) (end + 1),
  reduce (() => logFizziness (log)) (0)
])

export default main 
```

Enter fullscreen mode Exit fullscreen mode

# 单元测试

将`isFizz`、`isBuzz`、`isFizzBuzz`和`fizziness`移至`src/fizziness.mjs`可能是个不错的做法。但是为了文章简洁起见，我在这里不这样做。

要对这些坏男孩进行单元测试，只需给他们添加 export 关键字。

```
export const isFizz = num => num % 3 === 0
export const isBuzz = num => num % 5 === 0
export const isFizzBuzz = allPass ([ isFizz, isBuzz ])

export const fizziness = cond ([
  [ isFizzBuzz, 'FizzBuzz' ],
  [ isFizz, 'Fizz' ],
  [ isBuzz, 'Buzz' ],
  [ () => true, x => x ]
])

export const logFizziness = log => pipe ([
  sleep (1000),
  fizziness,
  log
])

export const main = ({ log }) => pipe ([
  ({ start, end }) => range (start) (end + 1),
  reduce (() => logFizziness (log)) (0)
])

export default main 
```

Enter fullscreen mode Exit fullscreen mode

创建`src/__tests__/fizziness.test.mjs`并编写一些测试:

```
import { isFizz } from '../main'

describe('fizziness', () => {
  describe('isFizz', () => {
    test('true when divisible by 5', () => {
      const expected = true
      const actual = isFizz(5)
      expect(actual).toBe(expected)
    })

    test('false when not divisible by 5', () => {
      const expected = false
      const actual = isFizz(6)
      expect(actual).toBe(expected)
    })
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在我正在使用 [Jest](https://jestjs.io) 测试框架。你可以用任何东西。注意，我是用 JavaScript 编写测试的。我发现最好遵循测试框架希望你使用的格式。我认为不值得包装 Jest，这样我们就可以用 MojiScript 编写测试。

# 测试主

测试`main`复合体。我们在那里有一个`sleep`命令。所以如果我们测试数字 1-15，那么需要 15 秒。

[![Ain't nobody got time for that](img/0f4a5f517bfc33c79b81446fe94cd54d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1cCscE9G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mqsmfcbhkf7wlrottdl4.jpg)

幸运的是，嘲笑`setTimeout`很容易。

```
// setup mocks
jest.spyOn(global, 'setTimeout').mockImplementation(func => func())

// take down mocks
global.setTimeout.mockReset() 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的测试应该需要 7 毫秒，而不是 15 秒！

```
import I from 'mojiscript/combinators/I'
import main from '../main'

describe('main', () => {
  const log = jest.fn(I)

  beforeEach(() => jest.spyOn(global, 'setTimeout').mockImplementation(func => func()))
  afterEach(() => global.setTimeout.mockReset())

  test('main', async () => {
    const expected = [[1], [2], ["Buzz"], [4], ["Fizz"], ["Buzz"], [7], [8], ["Buzz"], ["Fizz"], [11], ["Buzz"], [13], [14], ["FizzBuzz"]]
    expect.assertions(1)
    await main ({ log }) ({ start: 1, end: 15 })
    const actual = log.mock.calls
    expect(actual).toMatchObject(expected)
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

# 总结

*   我们了解到添加异步代码是多么的简单。
*   我们了解了如何将依赖项从`main`分离到`index`中，从而使测试变得更加容易。
*   我们学习了如何异步`map`。等待...我刚才说的是 **`async`** `map`吗？您可能已经错过了这一点，因为它太容易了，但是`map`、`filter`和`reduce`可以是异步的。这是一件大事，我将在不久的将来就此写一整篇文章。

哦，是的，在第一部分我说过我会“揭示生命的奥秘！”。嗯，我不想让人失望，所以生命的奥秘是...生活。这是递归，所以循环。

在这里关注我，或者在 Twitter [@joelnet](https://twitter.com/joelnet) ！

如果你认为 MojiScript 很有趣，给它一颗星[https://github.com/joelnet/MojiScript](https://github.com/joelnet/MojiScript)！在评论里和我分享你的看法吧！

**看我的其他文章:**

[为什么异步代码如此令人困惑(以及如何让它变得简单)](https://dev.to/joelnet/why-async-code-is-so-damn-confusing-and-a-how-to-make-it-easy-3441)

[在把 90%的 JavaScript 扔进垃圾桶后，我是如何重新发现自己对它的热爱的](https://hackernoon.com/how-i-rediscovered-my-love-for-javascript-after-throwing-90-of-it-in-the-trash-f1baed075d1b)

[![Cheers!](img/661c03d25961a6e3b1ecbe00d148b35a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PqqJmrxK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6jsy3a866frzp3u5oda0.jpg)