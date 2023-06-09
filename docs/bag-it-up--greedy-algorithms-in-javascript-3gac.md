# 包起来💰Javascript 中的贪婪算法

> 原文：<https://dev.to/albertywu/bag-it-up--greedy-algorithms-in-javascript-3gac>

* * *

#### 概述

javascript 工程师们不太理解的一个想法(除非你碰巧在为面试做准备)是贪婪算法的使用。一个[贪婪算法](https://en.wikipedia.org/wiki/Greedy_algorithm)做出当前看起来最好的选择，并解决随后出现的子问题。用一个形象的比喻来说，我们把每个子问题的结果放在一个“袋子”里，然后用更小的子问题来重复。当子问题为空时(没什么可做的了)，我们返回包中的内容。

事实证明，这种策略可以为实际问题带来一些非常优雅的解决方案。在本文的其余部分，我们将探索四个看似不同的问题，它们有几乎相同的解决方案(提示:它们都使用贪婪算法)。最后，我们将仔细看看所有四个问题的共同结构。让我们开始吧！

* * *

#### 举例:硬币找零问题

```
You are given coins of different denominations and a total amount of 
money. Write a function that returns the smallest set of coins that 
sums to that amount. 
```

Enter fullscreen mode Exit fullscreen mode

在继续之前，花点时间考虑一下你会怎么做…(答案就在下面)

* * *

```
function makeChange(amount, coins, bag = []) {
  if (amount === 0) return bag
  let largestCoin = getLargestCoin(amount, coins)
  return makeChange(amount - largestCoin, coins, bag.concat([largestCoin]))
}

function getLargestCoin(amount, coins) {
  let sortedCoins = coins.sort((a, b) =\> a - b)
  for (let i = sortedCoins.length - 1; i \>= 0; i--) {
    if (sortedCoins[i] \<= amount) return sortedCoins[i]
  }
  throw new Error('no coin that divides amount')
}

console.log(
  makeChange(42, [1, 5, 10, 25])
)
// [25, 10, 5, 1, 1] 
```

Enter fullscreen mode Exit fullscreen mode

我们保存一个硬币“包”，并递归地将符合我们选择标准的硬币添加到包中。如果最大的硬币有值`C`，我们将`C`添加到袋子中，并用`amount - C`调用`makeChange`。这一直持续到`amount`为 0，并且硬币袋被返回。

关于表达式`{ ...bag, ...{ [fn(array[0])]: matches } }`的一个快速注释，因为那里发生了很多事情。首先，`{ ...a, ...b }`是什么意思？这叫做[物体展开](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax#Spread_in_object_literals)。可以把它想象成将对象 a 和 b 放在一起创建一个新对象。所以`{ ...bag, ...somethingElse }`会把对象`bag`和对象`somethingElse`结合起来。在本例中，`somethingElse`是对象`{ [fn(array[0])]: matches }`，它是我们要插入包中的新组。

我也来解释一下`{ [key]: value }`和`{ key: value }`的区别。那些方括号表示[计算属性](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer#Computed_property_names)。你可以把任何表达式放在方括号之间，这个表达式的值将成为键值。因此，例如`{ [1 + 1]: 2}`将与`{ 2: 2 }`相同。

* * *

#### 例如:groupBy

```
Implement the "groupBy" function which takes an array A and a function F,
and returns an object composed of keys generated from the results of 
running each element of A through F. The corresponding value of each key 
is an array of elements responsible for generating the key. 
```

Enter fullscreen mode Exit fullscreen mode

在继续之前，花点时间考虑一下你会怎么做…(答案就在下面)

* * *

```
/*
  input: [6.1, 4.2, 6.3]
  function: Math.floor
  output: { '4': [4.2], '6': [6.1, 6.3] }
*/

function groupBy(array, fn, bag = {}) {
  if (array.length === 0) return bag
  let matches = array.filter(x =\> fn(x) === fn(array[0]))
  let rest = array.filter(x =\> fn(x) !== fn(array[0]))
  return (
    groupBy(
    rest,
    fn,
    { ...bag, ...{ [fn(array[0])]: matches } }
    )
  )
}

console.log(
  groupBy([6.1, 4.2, 6.3], Math.floor)
)
// { '4': [4.2], '6': [6.1, 6.3] } 
```

Enter fullscreen mode Exit fullscreen mode

保留一个组“包”，并递归地将符合我们选择标准的组添加到包中。然后用更新后的包调用剩余元素上的`groupBy`。这一直持续到原始数组为空，袋子被返回。

* * *

#### 举例:活动选择问题

另一个经典问题是[活动选择问题](https://en.wikipedia.org/wiki/Activity_selection_problem)。

```
Imagine you are trying to schedule a room for multiple competing events, 
each having its own time requirements (start and end time). How do you 
schedule the room such that you can host the maximum number of events 
with no scheduling conflicts? 
```

Enter fullscreen mode Exit fullscreen mode

在继续之前，花点时间考虑一下你会怎么做…(答案就在下面)

* * *

```
class Appointment {
  constructor(name, from, to) {
    this.name = name
    this.from = from
    this.to = to
  }
}

// push new appointments onto bag one-by-one until no more appointments are left
function getMaxAppointments(appointments, bag = []) {
  if (appointments.length === 0) return bag
  let selectedAppointment = appointments.sort((a, b) =\> a.to - b.to)[0] // sort from earliest end to latest end
  let futureCandidates = appointments.filter(a =\> a.from \> selectedAppointment.to)
  return getMaxAppointments(
    futureCandidates,
    bag.concat([selectedAppointment])
  )
}

let a1 = new Appointment('brush teeth', 0, 2)
let a2 = new Appointment('wash face', 1, 3)
let a3 = new Appointment('make coffee', 3, 5)
let a4 = new Appointment('blowdry hair', 3, 4)
let a5 = new Appointment('take shower', 4.5, 6)
let a6 = new Appointment('eat cereal', 7, 10)

console.log(
  getMaxAppointments([a1, a2, a3, a4, a5, a6]).map(a =\> a.name)
) 
// ['brush teeth', 'blowdry hair', 'take shower', 'eat cereal'] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

#### 举例:收集字谜

对于我们的最后一个例子，我们将考虑对字谜进行分组的问题。

```
Given an array of strings, group anagrams together.

For example:
Input: ["eat", "tea", "tan", "ate", "nat", "bat"],
Output:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
] 
```

Enter fullscreen mode Exit fullscreen mode

在继续之前，花点时间考虑一下你会怎么做…(答案就在下面)

* * *

```
function collectAnagrams(words, bag = []) {
  if (words.length === 0) return bag
  let matches = words.filter(w =\> isAnagram(w, words[0]))
  let rest = words.filter(w =\> !isAnagram(w, words[0]))
  return collectAnagrams(
    rest,
    bag.concat([matches])
  )
}

function stringSorter(a, b) { return a.localeCompare(b) }

function isAnagram(a, b) {
  let aSorted = a.toLowerCase().split('').sort(stringSorter).join('')
  let bSorted = b.toLowerCase().split('').sort(stringSorter).join('')
  return aSorted === bSorted
}

let x = ['bag', 'gab', 'foo', 'abg', 'oof', 'bum']
console.log(collectAnagrams(x))
// [['bag', 'gab', 'abg'], ['foo', 'oof'], ['bum']] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

#### 常见的结构

那么所有这些问题有什么共同点呢？对于循环中的每次迭代，我们从输入中选择一个项目子集，并将其添加到包中。剩余的项作为下一个输入传递给循环的下一次迭代。当输入为空时，我们返回袋子。

使用我们的 groupBy 示例，下图可能有助于澄清问题:

[![visualization](img/b32cadd9c508adeb1a20a89898ea87f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9PpHA1OT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/1920766/45563508-693f4680-b802-11e8-9891-1879d832b4ff.jpg)

如果你对伪代码更熟悉，这里是我们在前面所有例子中使用的模式:

```
function bagItUp(things, bag = []) {
  if (things is empty) return bag
  let thingsToPutInBag = ...
  let restOfThings = ...
  return bagItUp(
    restOfThings,
    bag + thingsToPutInBag
  ) 
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 连接

你怎么想呢?你在工作或个人项目中有没有用贪心算法解决过类似的问题？请在下面的评论中告诉我，或者在 [twitter](https://twitter.com/albertywu) 上告诉我！