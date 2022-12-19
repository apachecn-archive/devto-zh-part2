# 我们不需要讨厌的地图()或过滤器()

> 原文：<https://dev.to/jdsteinhauser/we-dont-need-no-stinking-map-or-filter-1hp4>

我知道这个标题很吸引人，但是听我说完！我向我们的一些初级开发人员提出了一个[淋浴思考](https://www.businessinsider.com/why-people-get-their-best-ideas-in-the-shower-2016-1)问题。如果你必须在`map`、`filter`和`reduce`中选择一个来度过余生，你会选择哪一个？虽然我在程序中经常使用`map`和`filter`——基本上与语言无关——而且我更喜欢使用它们...它们基本上都是`reduce`的衍生或者优化。

# 如何？！

所以，我们来看看 JavaScript 中的一些`Array.prototype.reduce()`的例子。它需要一个`callback`函数和一个可选的`initial value`。`callback`函数接受我们正在减少的数组中的`accumulator`和`currentValue`。Reduce 常用于集合项的求和:

```
const sum = (accumulator, currentValue) => currentValue + accumulator

let val = [1,2,3,4,5].reduce(sum,0)

console.log(val)  // => prints 15 to the console 
```

Enter fullscreen mode Exit fullscreen mode

让我们走一遍所发生事情的各个步骤。在第一步中，我们有一个累积值 0(我们将它作为初始值传递)，当前值是 1。我们对数组中的每个元素重复这个过程，并返回最终的累积值。

```
sum(0,1) = 0 + 1 = 1     // remaining values to accumulate: [2,3,4,5]
sum(1,2) = 1 + 2 = 3     // remaining values to accumulate: [3,4,5]
sum(3,3) = 3 + 3 = 6     // remaining values to accumulate: [4,5]
sum(6,4) = 6 + 4 = 10    // remaining value to accumulate: [5]
sum(10, 5) = 10 + 5 = 15 // remaining values to accumulate: []

// return accumulator of 15 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常简单的用例。Reduce 似乎最常用于求和与乘积。

# 你确定我们不需要地图？

[![Lost in the desert](img/73d408f0fd769063c4c2eac8a94fea21.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JuarEI_S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/29q3nrdrzw2xlmz5aj9v.jpg) 照片来自 [Julian Peters 摄影](https://julianpetersphotography.de/blog/getting-lost-in-the-desert-my-trip-to-the-sahara/)

`Array.prototype.map()`取一个函数应用于数组的每个元素，并返回结果数组。`map` ping 的经典例子是加倍整数:

```
const doubler = x => x * 2

let val = [1,2,3,4,5].map(doubler)

console.log(val)    // => prints [2,4,6,8,10] to the console 
```

Enter fullscreen mode Exit fullscreen mode

很简单，对吧？那么，我们究竟如何使用`reduce`来构建`map`？我们的累加器现在不是一个单一的值，而是一个数组。我们将把这个函数应用到每个`currentValue`上，并把它附加到累加器数组的末尾:

```
const pushAndReturn = (arr, item) => { arr.push(item); return arr }

const map = (f, arr) => arr.reduce( (acc, x) => pushAndReturn(acc, f(x)), [])

// Let's try it!
console.log( map(doubler, [1,2,3,4,5]) )    // => prints [2,4,6,8,10] 
```

Enter fullscreen mode Exit fullscreen mode

太棒了。和我们预期的完全一样。但是得到这个答案的所有步骤是什么呢？

```
[].pushAndReturn(doubler(1)) = [2]
// remaining values to accumulate: [2,3,4,5]
[2].pushAndReturn(doubler(2)) = [2,4]
// remaining values to accumulate: [3,4,5]
[2,4].pushAndReturn(doubler(3)) = [2,4,6]
// remaining values to accumulate: [4,5]
[2,4,6].pushAndReturn(doubler(4)) = [2,4,6,8]
// remaining value to accumulate: [5]
[2,4,6,8].pushAndReturn(doubler(5)) = [2,4,6,8,10]
// remaining values to accumulate: []

// return accumulator of [2,4,6,8,10] 
```

Enter fullscreen mode Exit fullscreen mode

重要的是要记住累加器可以是我们想要的任何类型。对一系列数字求和不一定非得是数字。它可以是任何东西。

# 来筛选一下我们的$#！+

![Filtering stream water](img/cda993fe37eeab6620241bab39fcf529.png)
Image from [Outside Magazine](https://www.outsideonline.com/2316951/know-when-its-time-new-water-filter)

`Array.prototype.filter()`接受一个谓词函数，并返回一个值数组，当谓词被应用时，该数组返回一个真值。例如，如果我们只想从数组中获取奇数，我们可以使用:

```
const isOdd = x => x % 2 === 1

let val = [1,2,3,4,5].filter(isOdd)

console.log(val)        // => prints [1,3,5] 
```

让我们用 just reduce 实现过滤器吧！这将看起来非常类似于我们上面的地图实现。

```
const filter = (pred, arr) => arr.reduce( (acc, x) => pred(x) ? pushAndReturn(acc, x) : acc, [])

// Let's try this one now!
console.log( filter(isOdd, [1,2,3,4,5]) )    // => prints [1,3,5] 
```

同样，让我们通过这些步骤来看看会发生什么:

```
isOdd(1) ? [].pushAndReturn(1) : [] = [1]
// remaining values to accumulate: [2,3,4,5]
isOdd(2) ? [1].pushAndReturn(2) : [1] = [1]
// remaining values to accumulate: [3,4,5]
isOdd(3) ? [1].pushAndReturn(3) : [1] = [1,3]
// remaining values to accumulate: [4,5]
isOdd(4) ? [1,3].pushAndReturn(4) : [1,3] = [1,3]
// remaining value to accumulate: [5]
isOdd(5) ? [1,3].pushAndReturn(5) : [1,3] = [1,3,5]
// remaining values to accumulate: []

// return accumulator of [1,3,5] 
```

# 如此...为什么？

[![Ryan Reynolds' existential question](img/938fa768b344f7fcaf2aefad6c8f9f8d.png)T2】](https://i.giphy.com/media/1M9fmo1WAFVK0/giphy.gif)

虽然这主要是一个思考练习(我不一定在产品代码中使用`reduce()`来代替`map()`)，但重要的是要记住`reduce()`可以返回任何类型的值。当然，你可以用 reduce 做加法、乘法和串联运算...但是您也可以进行映射、过滤、在元素匹配谓词时获取元素、构建树结构、从列表/数组创建字典/映射...全部通过使用 reduce。

感谢阅读！