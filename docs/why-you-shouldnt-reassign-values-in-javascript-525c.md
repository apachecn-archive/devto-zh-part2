# 为什么不应该在 JavaScript 中重新赋值

> 原文：<https://dev.to/zellwk/why-you-shouldnt-reassign-values-in-javascript-525c>

在 JavaScript 中，可以给用`let`或`var`声明的变量重新赋值。

我过去常常重新赋值。但是随着我对 JavaScript 的掌握越来越好，我意识到如果可以的话不应该重新赋值。这是因为:

1.  重新分配值时，您可能会意外更改外部状态
2.  当您重新赋值时，会创建更复杂的代码

## 重新赋值时，可能会意外改变外部状态

当你给一个变量赋值时，你写变量名，后面跟着`=`，后面跟着你的新值。

```
// Declaring a variable
let name = 'Zell'

// Reassigning a variable
name = 'Vincy' 
```

Enter fullscreen mode Exit fullscreen mode

乍一看，重新赋值似乎没问题，但事实并非如此。当您查看范围时，问题就出现了。

假设在全局环境中有一个名为`name`的变量。

假设您创建了一个名为`sayName`的函数。`sayName`记录您传递给它的参数。但是在你记录参数之前，你要改变`name`变量。

```
let name = 'Zell'

function sayName (arg) {
  name = arg
  console.log(name)
} 
```

Enter fullscreen mode Exit fullscreen mode

当你调用`sayName`时，你永远改变了`name`的值。它改变了函数之外的状态。

```
sayName('Vincy') // Vincy
console.log(name) // Vincy 
```

Enter fullscreen mode Exit fullscreen mode

现在，你可能认为这个例子听起来很愚蠢，你永远也不会写出这样愚蠢的代码。

问题是，你能保证你永远不会犯这个错误吗，即使是在最困难的时候？

我不能。

养成不重新分配变量的习惯。

我建议总是用关键字`const`创建变量。这是因为用`const`关键字创建的变量不能被重新分配。如果你试图给它们赋值，你会得到一个错误。

```
const name = 'Zell'

function sayName (arg) {
  name = arg
  console.log(name)
}

sayName('Vincy') // TypeError: invalid assignment to const `name' 
```

Enter fullscreen mode Exit fullscreen mode

## 重新赋值时会创建复杂的代码

想象一下你的一个同事每天都在换发型。她走进办公室，带着:

1.  周一的刘海
2.  周二编辫子
3.  周三短发
4.  周四留长发
5.  星期五亮粉色的头发

你很难认出她。

当你用`let`创建一个变量时，你的大脑知道这个变量将要改变。它会花费更多的精力来跟踪沿途的变化。

让我们看一个例子，你会看到这是如何工作的。

```
let hair

if (today === 'Monday') {
  hair = 'bangs'
} else {
  hair = 'something else'
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，你的大脑会想:

1.  有一个变量叫做`hair`。
2.  `hair`的值以后会变。现在不知道它的价值。(这个不舒服)。
3.  后来...
4.  今天是星期一。
5.  头发快要被`bangs`

现在，将这个思考过程与使用三元运算符的思考过程进行比较。

```
const hair = today === 'Monday'
  ? 'bangs'
  : 'something else' 
```

Enter fullscreen mode Exit fullscreen mode

在这里，你的大脑会想:

1.  有一个变量叫做头发。
2.  今天是星期一。
3.  头发快要`bangs`了。

有了三进制操作代码，就没有“等着瞧”的时刻了。你的大脑做较少的工作，因为不需要“等待和观察”。

这有点像决定你午餐吃什么。如果你事先已经决定了，你就不会浪费精力去想吃什么。

### 与多个分支合作

三元运算符只有在有简单的`if/else`语句时才有效。如果你需要更多的分支机构呢？

```
let hair

if (today === 'Monday') {
  hair = 'bangs'
} else if (today === 'Tuesday') {
  hair = 'braids'
} else if (today === 'Wednesday') {
  hair = 'short hair'
} else if (today === 'Thursday') {
  hair = 'long hair'
} else if (today === 'Friday') {
  hair = 'bright pink hair'
} 
```

Enter fullscreen mode Exit fullscreen mode

处理许多`if/else`语句(甚至一个`switch`语句)的最好方法是在一个函数中包含`if/else`逻辑。

这是一个开始(我们简单地在上面的代码周围包装一个函数):

```
function getHairType (today) {
  let hair

  if (today === 'Monday') {
    hair = 'bangs'
  } else if (today === 'Tuesday') {
    hair = 'braids'
  } else if (today === 'Wednesday') {
    hair = 'short hair'
  } else if (today === 'Thursday') {
    hair = 'long hair'
  } else if (today === 'Friday') {
    hair = 'bright pink hair'
  }

  return hair
} 
```

Enter fullscreen mode Exit fullscreen mode

当你调用`getHairType`时，你并不真正关心它是如何工作的。你所关心的是从`getHairType`中得到的值。这使您更容易理解代码是如何执行的。

```
// Using the function
const hair = getHairType('Wednesday') // short hair 
```

Enter fullscreen mode Exit fullscreen mode

下一步将改进`getHairType`。在这里，你可以用一个**提前返回**。

提前返回意味着在函数完成执行之前返回一个值。当你这样做的时候，你甚至不需要创建一个`hair`变量。可以直接返回`hair`的值。

```
function getHairType (today) {
  if (today === 'Monday') return 'bangs'
  if (today === 'Tuesday') return 'braids'
  if (today === 'Wednesday') return 'short hair'
  if (today === 'Thursday') return 'long hair'
  if (today === 'Friday') return 'bright pink hair'
} 
```

Enter fullscreen mode Exit fullscreen mode

现在读起来容易多了，不是吗？

## 包装完毕

您希望避免将值重新分配给变量，因为:

1.  重新分配值时，您可能会意外更改外部状态
2.  当您重新赋值时，会创建更复杂的代码

不要重新赋值，尝试使用三元运算符和早期返回。它们可以使你的代码更简洁，更易读。

如果这一课对你有帮助，你可能会喜欢[学习 JavaScript](https://learnjavascript.today) ，在这里你将学习如何从头开始构建任何你想要的东西。学习 JavaScript 的报名于 2018 年 7 月开放(三周后！).

* * *

感谢阅读。这篇文章最初发表在我的博客上。如果你想要更多的文章来帮助你成为一个更好的前端开发者，请注册[我的时事通讯](https://zellwk.com)。