# JavaScript 中的函数模式与命令模式

> 原文：<https://dev.to/joelnet/functional-vs-imperative-patterns-in-javascript-505h>

这篇文章的目的不是说一种范式比另一种好。它只是显示您遇到的常见模式及其功能等同物。

如果有我错过的模式，你想看的话，在下面的评论里贴出来。警告，把你的模式归结为最小公约数。我一个 100 行的函数都转换不了！；)

# 如果/否则

**祈使式**

```
const hour = 14
let greeting

if (hour < 18) {
  greeting = 'Good day';
} else {
  greeting = 'Good evening';
} 
```

**功能性**

`if`现在可以作为函数`getGreeting`重用。

答:

```
const isDay = hour => hour < 18
const getGreeting = hour => isDay(hour) ? 'Good Day' : 'Good Evening'
const greeting = getGreeting (hour) 
```

乙:

```
import ifElse from 'mojiscript/logic/ifElse'

const isDay = hour => hour < 18
const getGreeting = ifElse (isDay) (() => 'Good Day') (() => 'Good evening')
const greeting = getGreeting (hour) 
```

# 如果(无其他)

**祈使式**

```
let name = 'joel'

if (name != null) {
  name = name.toUpperCase()
} 
```

**功能性**

`isNotnull`和`toUpperCase`是可重复使用的函数。没有覆盖`name`，而是创建了`upperName`。

```
import when from 'mojiscript/logic/when'

const isNotNull = obj => obj != null
const toUpperCase = when (isNotNull) (string => string.toUpperCase ())

const name = 'joel'
const upperName = toUpperCase (name) 
```

# 求和一个数组

**祈使式**

```
const values = [1, 2, 3]

let sum = 0
for (const x of values) {
  sum = sum + x
} 
```

**功能性**

不要变异`sum`。

答:

```
const values = [1, 2, 3]

const add = (x, y) => x + y
const sum = values.reduce(add) 
```

乙:

```
import reduce from 'mojiscript/list/reduce'

const add = x => y => x + y
const sum = reduce (add) (0)

const values = [1, 2, 3]
sum (values) 
```

# for / if (1)

**祈使式**

```
const values = [1, 2, 3, 4, 5]

let evens = []
for (const x of values) {
  if (x % 2 === 0) {
    evens.push(x)
  }
} 
```

**功能性**

不要变异`evens`。

```
import filter from 'mojiscript/list/filter'

const values = [1, 2, 3, 4, 5]

const isEven = num => num % 2 === 0
const evens = filter (isEven) (values) 
```

# for / if (2)

**祈使式**

```
const values = [1, 2, 3, 4, 5]

for (const x of values) {
  if (x % 2 === 0) {
    console.log(`${x} isEven`)
  }
} 
```

**功能性**

使用`when`进行条件执行。

```
import map from 'mojiscript/list/map'
import when from 'mojiscript/logic/when'

const isEven = num => num % 2 === 0
const logWhenEven = when (isEven) (x => console.log (`${x} isEven`))

const values = [1, 2, 3, 4, 5]
map (logWhenEven) (values) 
```

# 提前打破循环

**祈使式**

```
const values = [1, 2, 3]
let sum = 0
for (const x of values) {
  if (x > 3) break
  sum = sum + x 
} 
```

**功能性**

`reduceWhile`和`reduce`很像，只是接受了一个谓词来提前“断”。

```
import reduceWhile from 'mojiscript/list/reduceWhile'

const add = x => y => x + y
const lte3 = num => num <= 3

const sum = reduceWhile (() => lte3) (add) (0) (values) 
```

# 如果/否则如果/否则

**祈使式**

```
const fn = temp => {
   if (temp === 0) return 'water freezes at 0°C'
   else if (temp === 100) return 'water boils at 100°C'
   else return `nothing special happens at ${temp}°C`
}

fn(0) //=> 'water freezes at 0°C'
fn(50) //=> 'nothing special happens at 50°C'
fn(100) //=> 'water boils at 100°C' 
```

**功能性**

```
import cond from 'mojiscript/logic/cond'
import $ from 'mojiscript/string/template'

const fn = cond([
  [0, 'water freezes at 0°C'],
  [100, 'water boils at 100°C'],
  [() => true, $`nothing special happens at ${0}°C`]
])

fn(0) //=> 'water freezes at 0°C'
fn(50) //=> 'nothing special happens at 50°C'
fn(100) //=> 'water boils at 100°C' 
```

# 设置属性

**祈使式**

```
const obj = {
  one: 1
}

obj.two = 2 
```

**功能性**

不要改变原始对象，浅克隆它，然后添加新的道具。

注意:当对象可变时，必须进行深度克隆。如果对象是不可变的，那么可以进行浅层克隆，这有明显的性能优势。

```
const obj = {
  one: 1
}

const newObj = {
  ...obj,
  two: 2
} 
```

# 修改数组

**祈使式**

```
const values = [1, 2, 3]
values.push(4) 
```

**功能性**

不要变异`values`。

答:

```
const values = [1, 2, 3]
const newValues = [...values, 4] 
```

乙:

对于大型数组，使用像 [list](https://github.com/funkia/list) 这样的不可变库来获得高性能的不可变数组。

```
import L from 'list'

const values = L.from([1, 2, 3])
const newValues = L.append(4, values) 
```

# 类

**祈使句**

容易出错。

```
class Cat {
  constructor() {
    this.sound = 'Meow'
  }

  talk() {
    return this.sound
  }
}

const cat = new Cat()
const talk = cat.talk

cat.talk() //=> 'Meow'
talk() //=> Error: Cannot read property 'sound' of undefined 
```

**功能性**

将功能与数据分离，以实现最大的可重用性。

```
const cat = {
  sound: 'Meow'
}

const dog = {
  sound: 'Woof'
}

const talk = animal => animal.sound

talk (cat) //=> 'Meow'
talk (dog) //=> 'Woof' 
```

# 嵌套为循环

**祈使式**

```
let box = ''
for (let y = 0; y < 5; y++) {
  for (let x = 0; x < 5; x++) {
    box = box + '* '
  }
  box = box + '\n'
} 
```

**功能性**

不再筑巢。不可改变。

```
import reduce from 'mojiscript/list/reduce'
import range from 'mojiscript/list/range'

const makeCols = cols =>
  reduce (acc => () => acc + '* ') ('') (range (0) (cols))

const makeBox = ({ cols, rows }) =>
  reduce (acc => () => `${acc}${makeCols (cols)}\n`) ('') (range (0) (rows))

const box = makeBox ({ cols: 5, rows: 5 })
//=> ​​​​​* * * * * ​​​​​
//=> ​​​​​* * * * * ​​​​​
//=> ​​​​​* * * * * ​​​​​
//=> ​​​​​* * * * * ​​​​​
//=> ​​​​​* * * * * ​​​​​ 
```

而且可重复使用！

```
const makeTriangle = length =>
  reduce
    (acc => i => `${acc}${'  '.repeat(length - i)}${makeCols (i + 1)}\n`)
    ('')
    (range (0) (length))

const triangle = makeTriangle (5)
//=>​​​​​     * ​​​​​
//=>​​​​​    * * ​​​​​
//=>​​​​​   * * * ​​​​​
//=>​​​​​  * * * * ​​​​​
//=>​​​​​ * * * * * ​​​​​ 
```

# 零位保护

**祈使式**

```
const toUpper = string => {
  if (string != null) {
    return string.toUpperCase()
  }
} 
```

**功能性**

答:

这个例子将参数包装在一个`Maybe`类型中，然后在最后将其打开。在一个典型的 FP 应用中，你会在整个应用中使用`Maybe`，所以你不需要包装和打开`string`。因此，这比您通常看到的要详细一些。

```
import S from 'sanctuary'

const toUpper = S.pipe ([
  S.toMaybe,
  S.map (string => string.toUpperCase ()),
  S.maybeToNullable
])

// If you use `Maybe` throughout your app, this would be your `toUpper` function.
const toUpper = S.map (string => string.toUpperCase ()) 
```

乙:

`maybe`是一个函数装饰器，它只在提供了参数的情况下执行函数。现在我们的零保护是可重用的。这里有更多关于函数装饰者的内容:[函数 JavaScript:函数装饰者第二部分# JavaScript](https://dev.to/joelnet/function-decorators-part-2-javascript-4km9)T3】

```
const maybe = func => (...args) =>
  args.length === 0 || args[0] == null
    ? args[0]
    : func(...args)

const toUpper = maybe(string => string.toUpperCase ()) 
```

# 结束

我的文章是非常实用的 JavaScript，如果你需要更多的 FP，在这里关注我，或者在 Twitter [@joelnet](https://twitter.com/joelnet) ！

**更多文章**
[问我关于函数式编程的傻问题](https://dev.to/joelnet/ask-me-dumb-questions-about-functional-programming-bho)
[我们来做一个开发人员对 CLI...一起](https://dev.to/joelnet/lets-make-a-devto-cli-together-4eh1)
[让我们来谈谈 JavaScript 的自动生成文档工具](https://dev.to/joelnet/lets-talk-about-auto-generated-documentation-tools-for-javascript-49ol)

[![Cheers!](img/661c03d25961a6e3b1ecbe00d148b35a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PqqJmrxK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6jsy3a866frzp3u5oda0.jpg)