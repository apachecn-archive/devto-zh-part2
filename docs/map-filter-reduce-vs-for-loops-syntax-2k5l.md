# Map、Filter、Reduce vs For 循环(语法)

> 原文：<https://dev.to/joelnet/map-filter-reduce-vs-for-loops-syntax-2k5l>

这篇文章的目的不是说一种范式比另一种好。这只是为了展示您遇到的常见命令模式及其功能等同物。

有时候，如果你能把事情和你已经熟悉的事情联系起来，并给你一张如何来回转换的地图，事情会更容易学。

之前，我在 JavaScript 的中的[功能模式和命令模式做过类似的事情](https://dev.to/joelnet/functional-vs-imperative-patterns-in-javascript-505h)

# 地图

`map`获取一个列表并对列表中的每一项运行一个函数，返回相同长度的列表。

**祈使式**

```
const double = x => x * 2
const input = [ 1, 2, 3 ]
const output = []
for (let i = 0; i < input.length; i++) {
  output.push(double(input[i]))
}

output //=> [ 2, 4, 6 ] 
```

**功能性**

```
const double = x => x * 2
const input = [ 1, 2, 3 ]
const output = input.map(double)

output //=> [ 2, 4, 6 ] 
```

# 滤镜

`filter`获取一个列表并返回一个包含所有匹配谓词的项目的列表。在这个例子中,`isEven`是谓词。

**祈使式**

```
const isEven = x => x % 2 === 0
const input = [ 1, 2, 3, 4, 5 ]
const output = []
for (let i = 0; i < input.length; i++) {
  if (isEven(input[i])) {
    output.push(input[i])
  }
}

output //=> [ 2, 4, 6 ] 
```

**功能性**

```
const isEven = x => x % 2 === 0
const input = [ 1, 2, 3, 4, 5 ]
const output = input.filter(isEven)

output //=> [ 2, 4, 6 ] 
```

# 减少

`reduce`接受一个列表并返回任何数据结构。它可能是另一个列表或对象。

## 简单

**祈使式**

```
const add = (x, y) => x + y
const input = [ 1, 2, 3 ]
const initial = 0
let output = initial
for (let i = 0; i < input.length; i++) {
  output = add(output, input[i])
}

output //=> 6 
```

**功能性**

```
const add = (x, y) => x + y
const input = [ 1, 2, 3 ]
const initial = 0
const output = input.reduce(add, initial)

output //=> 6 
```

## 复杂

**祈使式**

```
const isEven = x => x % 2 === 0
const double = x => x * 2
const input = [ 1, 2, 3, 4, 5 ]
const initial = []

let output = initial
for (let i = 0; i < input.length; i++) {
  if (isEven(input[i])) {
    output.push(double(input[i]))
  }
}

output //=> [ 4, 8 ] 
```

**功能性**

它可以像下面这样写，但是要知道它将在数组上迭代两次。

```
const isEven = x => x % 2 === 0
const double = x => x * 2
const input = [ 1, 2, 3, 4, 5 ]
const initial = []

let output =
  input
    .filter(isEven)
    .map(double)

output //=> [ 4, 8 ] 
```

或者，您可以创建一个缩减器，它可以在一次迭代中同时执行`filter`和`map`。

```
const isEven = x => x % 2 === 0
const double = x => x * 2
const input = [ 1, 2, 3, 4, 5 ]
const initial = []

const reducer = (filter, map) => (acc, x) => {
  if (filter(x)) {
    acc.push(map(x))
  }
  return acc
}

const output = input.reduce(reducer(isEven, double), initial)

output //=> [ 4, 8 ] 
```

# 结束

我目前可以兼职合同工作(C#，JavaScript，反应)。在推特或 T2【LinkedIn】上给我打电话联系我。

我的文章是非常实用的 JavaScript，如果你需要更多的 FP，在这里关注我，或者在 Twitter [@joelnet](https://twitter.com/joelnet) ！

**更多文章**
[解构映射、过滤、归约](https://dev.to/joelnet/deconstructing-map-filter-and-reduce-1d1a)
[问我关于函数式编程的傻问题](https://dev.to/joelnet/ask-me-dumb-questions-about-functional-programming-bho)
[我们来做一个 DEV.to CLI...一起](https://dev.to/joelnet/lets-make-a-devto-cli-together-4eh1)
[让我们来谈谈 JavaScript 的自动生成文档工具](https://dev.to/joelnet/lets-talk-about-auto-generated-documentation-tools-for-javascript-49ol)

[![Cheers!](img/661c03d25961a6e3b1ecbe00d148b35a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PqqJmrxK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6jsy3a866frzp3u5oda0.jpg)