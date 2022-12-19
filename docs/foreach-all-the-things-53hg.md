# “forEach”所有的东西！

> 原文：<https://dev.to/jperasmus/foreach-all-the-things-53hg>

*免责声明:这是一篇观点文章，你可以不同意。*

在过去的几年里，我做了大量的代码审查，看到了什么会反过来伤害我们，什么通常会按照我们预期的方式工作，其中一个问题就是知道什么时候使用`forEach`。归结起来就是问自己正确的问题。

在另一个独立但相关的问题上，我认为，熟悉并知道何时使用`forEach`、`map`、`filter`或`reduce`，您就可以解决任何与数组相关的数据操作问题，所以作为一名开发人员，努力熟悉它是值得的。当然还有其他一些非常有用的方法，比如`flatMap`，但是知道了这四个，你可以走很长的路。

本文将以 JavaScript 为例，但这个概念对于任何支持高阶数组函数的语言都是一样的:`forEach`、`map`、`filter`和`reduce`。

#### 这个帖子是给谁的？

已经知道偏爱`forEach`而不是`for`循环的开发人员可以写出更漂亮的(声明性的！)代码，但是可能不习惯这些高阶数组函数。有一个`for`循环的地方，但更多的时候，你应该使用更多的声明性选项。

#### 【forEach】所有的事情！

当开发人员从命令式 for 循环转移到更具声明性的 forEach 函数时，他们开始做的第一件事就是**“forEach all things”**！

[![](../Images/91112db14be89d763067adcd2185c7fe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--429AEgKF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/500/1%2A_HepUVzNXC4oo5jGsco_og.jpeg)

这没问题，肯定比到处使用 for 循环要好，但是我们可以做得更好。

#### 思维过程

当然，你可以用鞋子把画挂在墙上，但是锤子是更好的工具。

> 使用合适的工具完成工作。

以下是你在编码时可以问自己的问题，并试图决定是否应该使用`forEach`、`map`、`filter`或`reduce`。

#### 1。我有一系列的东西，我想以某种方式操作每一项

**错误的方式:**使用 forEach

```
const list = ['a', 'b', 'c']
const newList = []

list.forEach(item => {
 newList.push(item.toUpperCase())
})

console.log(newList) // ['A', 'B', 'C'] 
```

**正确的方法:**使用地图

```
const list = ['a', 'b', 'c']

const newList = list.map(item => item.toUpperCase())

console.log(newList) // ['A', 'B', 'C'] 
```

#### 2。我有一大堆事情，我想把它们“简化”成一件事

一旦你熟悉了这个方法，你就很容易记住它，而且它非常适用于求和或者将一列对象简化为一个对象的情况。您可以将`reduce`看作是将一系列输入压缩成一个输出。

**错误的方式:**使用 forEach

```
let total = 0
const list = [1, 2, 3, 4]

list.forEach(item => {
 total = total + item
})

console.log(total) // 10 
```

**正确的方法:**使用 reduce

```
const list = [1, 2, 3, 4]

const total = list.reduce((sum, item) => sum + item, 0)

console.log(total) // 10 
```

这个例子总结了几个数字，并没有使痛苦变得明显，但是处理更复杂的数据会使问题浮出水面。

#### 3。我有很多东西，但我只想要其中的一部分

**错误的方式:**使用 forEach *(注意到这里的一个模式？)*

```
const list = [1, 2, 3, 4]
const evenNumbers = []

list.forEach(item => {
 if (item % 2 === 0) {
 evenNumbers.push(item)
 }
})

console.log(evenNumbers) 
```

**正确的方法:**使用滤镜

```
const list = [1, 2, 3, 4]

const evenNumbers = list.filter(item => item % 2 === 0)

console.log(evenNumbers) 
```

#### 4。我有一组东西，我想对每一项执行一个动作，但是我不在乎返回值

**错误方式:**使用贴图、滤镜或缩小

正如我们在上面看到的，在技术上可以将`forEach`用于其他任何事情一样，在这里也可以使用任何其他高阶函数，但是它并不适合这项工作。

> “这只是语义”不是一个东西。

```
const list = ['user1', 'user2', 'user3']

const newList = list.map(user => fireAndForgetNetworkRequest(user))

console.log(newList) // [undefined, undefined, undefined] 
```

**正确的方式:**使用 forEach

```
const list = ['user1', 'user2', 'user3']

list.forEach(user => fireAndForgetNetworkRequest(user))

// nothing to log 
```

> **提示**:如果你不能用*常量*声明你的变量，而你需要使用 *let* ，你可能用错了方法。

#### TL；DR；

[![](../Images/5cbb0c5ba19dec9558708bec2fec85d7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WemmvgaZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/757/1%2A8AOqGSDe1pzNAb1nMrYOTA.png)

如果你做到了这一步，希望这篇文章不会太无聊。如果你有任何不同意的地方，或者你有一些我没有想到的更好的例子，请在评论中告诉我。

*发布日期:2018 年 5 月 10 日*