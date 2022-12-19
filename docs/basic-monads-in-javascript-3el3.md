# Javascript 中的基本单子

> 原文：<https://dev.to/rametta/basic-monads-in-javascript-3el3>

我将解释一些常见的单子，你可以从今天开始在你的 javascript 中使用。单子将有助于使你的代码更容易阅读，更易维护，最重要的是更安全。

## 也许吧

Maybe 单子用于处理可空数据。通常我们喜欢用 javascript 处理数据，比如格式化、计算、过滤和排序。但是通常我们需要在做任何事情之前确保数据已经存在。这也许能帮上忙。

我将使用一个名为 [Pratica](https://github.com/rametta/pratica) 的小型友好助手库来实现本文中的单子。

让我们来看一个可以受益于 Maybe 单子的片段。

```
const data = 'Hello my name is Jason'

if (data) {
  console.log(data.toUpperCase()) // HELLO MY NAME IS JASON
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们看看如何用一个也许来重构它。

```
import { Maybe } from 'pratica'

Maybe('Hello my name is Jason')
  .map(data => data.toUpperCase())
  .cata({
    Just: data => console.log(data), // HELLO MY NAME IS JASON
    Nothing: () => console.log('No data available')
  }) 
```

Enter fullscreen mode Exit fullscreen mode

我们不需要检查数据是否存在，因为如果数据为空，可能会自动停止运行任何函数。避免错误就像`Uncaught TypeError: Cannot read property 'toUpperCase' of undefined`

现在你可能不会马上看到优势，但这不是可能发光的地方。让我们看另一个有更多步骤的例子。

```
// Step 1: Filter cool people
// Step 2: Find the first cool person
// Step 3: Log their uppercased name if there is one

const data = [
  { name: 'Jason', level: 7, cool: true },
  { name: 'Blanche', level: 8, cool: false }
]

if (data) {
  const coolPeople = data.filter(person => person.cool)
  if (coolPeople) {
    const firstCoolPerson = coolPeople[0]

    if (firstCoolPerson && firstCoolPerson.name) {
      console.log(firstCoolPerson.name.toUpperCase()) // JASON
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们看看可能的选择。

```
import { Maybe } from 'pratica'

Maybe(data)
  .map(people => people.filter(person => person.cool))
  .map(people => people[0])
  .map(person => person.name)
  .map(name => name.toUpperCase())
  .cata({
    Just: data => console.log(data), // JASON
    Nothing: () => console.log('No data available')
  }) 
```

Enter fullscreen mode Exit fullscreen mode

如果数据实际上为空或未定义，那么。在 cata 中将运行 map 函数，并执行 Nothing 函数。

但是假设我们也想在数据为空时返回一个默认值。然后我们可以用`.default()`的方法。

```
import { Maybe } from 'pratica'

Maybe(null)
  .map(people => people.filter(person => person.cool))
  .map(people => people[0])
  .map(person => person.name)
  .map(name => name.toUpperCase())
  .default(() => 'No cool people yo')
  .cata({
    Just: data => console.log(data), // No cool people yo
    Nothing: () => console.log('No data available')
  }) 
```

Enter fullscreen mode Exit fullscreen mode

哇这么干净，平多了。

## 结果

因此，我们了解到 Maybe monad 适合处理可空数据，但是如果我们想要检查数据的值并根据值做不同的事情呢？

输入结果单子(有时也称为任一单子)。

结果用于“分支”您的逻辑。我们先来看一个没有结果的例子。

```
const person = { name: 'Jason', level: 7, cool: true }

if (person.level === 7) {
  console.log('This person is level 7, ew')
} else {
  console.error('This person is some other level, but not 7')
} 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在有结果了。

```
import { Ok, Err } from 'pratica'

const person = { name: 'Jason', level: 7, cool: true }

const lvl = person.level === 7
  ? Ok('This person is level 7, ew')
  : Err('This person is some other level, but not 7')

lvl.cata({
  Ok: msg => console.log(msg), // This person is level 7, ew
  Err: err => console.log(err) // This person is some other level, but not 7
}) 
```

Enter fullscreen mode Exit fullscreen mode

嗯，我不明白这有什么意义。什么是好的和错误的？这怎么更好？

先多做一个例子再解释。

在本例中，我们需要在继续之前验证一些数据。

```
const data = {
  first: 'Jason',
  level: 85,
  cool: true,
  shirt: {
    size: 'm',
    color: 'blue',
    length: 90,
    logo: {
      color1: '#abc123',
      color2: '#somehexcolor'
    }
  }
}

if (data) {
  if (data.shirt) {
    if (data.shirt.logo) {
      if (data.shirt.logo.color1 !== 'black') {

        // Color1 is valid, now lets continue
        console.log(data.shirt.logo.color1)

      } else {
        console.error ('Color1 is black')
      }
    } else {
      console.error ('No logo')
    }
  } else {
    console.error ('No shirt')
  }
} else {
  console.error ('No data')
} 
```

Enter fullscreen mode Exit fullscreen mode

这看起来有点乱。让我们看看如何才能改善结果。

```
import { Ok, Err } from 'pratica'

const hasData = data => data
  ? Ok (data.shirt)
  : Err ('No data')

const hasShirt = shirt => shirt
  ? Ok (shirt.logo)
  : Err ('No shirt')

const hasLogo = logo => logo
  ? Ok (logo.color1)
  : Err ('No logo')

const isNotBlack = color => color !== 'black'
  ? Ok (color)
  : Err ('Color is black')

hasData (data2)
  .chain (hasShirt)
  .chain (hasLogo)
  .chain (isNotBlack)
  .cata ({
    Ok: color => console.log(color), // #abc123
    Err: msg => console.log(msg)
  }) 
```

Enter fullscreen mode Exit fullscreen mode

有意思，奉承了很多，但我还是不明白怎么回事。

好吧，事情是这样的。

我们从 hasData 函数开始。它获取需要验证的初始数据，并返回下一个需要验证的数据，但返回的数据包装在结果单子中，更具体地说，是 Ok 或 Err 类型。这两者都是形成结果单子的原因，也是我们的应用程序如何分支逻辑的原因。

为什么每行都有`.chain()`？

每个函数都返回 Ok 或 Err 数据类型。但是每个函数都希望它的输入只是数据，而不是包装在单子里的数据。所以在每个函数上调用 chain 将解开 monad 中的数据，这样函数就可以读取里面的内容。

为什么这样更好？

嗯，更好是主观的，但在函数式编程中，这被认为是更好的，因为它将 IO (IO 是控制台日志记录语句)推到了程序的边缘。这意味着有更多的纯函数可以进行单元测试，并且不会在其中混入 IO。将 IO 放在纯函数中不会使它们变得更纯，这意味着它们更难进行单元测试，并且是错误的来源。控制台日志在 javascript 中并不重要，但是如果 IO 正在发出网络请求，那么这种类型的编程就大不一样了，因为所有的逻辑/验证都将独立于 IO，并且更容易测试和维护。

这是两种流行的单子，你可以从今天开始使用。

这是我的第一篇 dev.to 文章，所以请在评论中告诉我你的想法！

如果你想了解更多关于单子的知识，可以看看这些很酷的文章和库。

*   练习
*   [图片中的单子](http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html#monads)
*   [幻境](https://github.com/fantasyland/fantasy-land)
*   [避难所 JS](https://github.com/sanctuary-js/sanctuary)
*   一把单子