# 极端改造:代码版

> 原文：<https://dev.to/aspittel/extreme-makeover-code-edition-k5k>

我有点痴迷于编写干净的代码。代码应该为未来的开发人员(包括你自己)和可扩展性而编写——向应用程序添加功能和维护代码应该相对容易。如果我们为计算机写代码，我们只会写二进制代码！以下是我写更干净代码的一些建议:

## 1。使用清晰的变量名和函数名

如果写出完整的、描述性的变量和函数名，代码会变得更容易阅读。下面的代码不是很清楚:

```
function avg (a) {
  let s = a.reduce((x, y) => x + y)
  return s / a.length
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们写出完整的变量名，可读性会更好！

```
function averageArray (array) {
  let sum = array.reduce((number, currentSum) => number + currentSum)
  return sum / array.length
} 
```

Enter fullscreen mode Exit fullscreen mode

> 不要缩小你自己的代码；使用下一个开发人员可以理解的完整变量名。

## 2。编写只做一件事的简短函数

如果函数只做一件事，它们就更容易理解、阅读和维护。如果我们在编写短函数时有一个 bug，通常更容易找到这个 bug 的来源。此外，我们的代码将更加可重用。例如，我们可以将上面的函数重命名为“sumAndAverageArray ”,因为我们使用 reduce 计算总和，然后计算该总和的平均值。

```
function sumAndAverageArray(array) {
  let sum = array.reduce((number, currentSum) => number + currentSum)
  return sum / array.length
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以把它分成两个函数，这样代码的每个部分在做什么就变得更清楚了。此外，如果我们正在创建一个大型程序，拥有`sumArray`函数可能会派上用场！

```
function sumArray(array) {
  return array.reduce((number, currentSum) => number + currentSum)
}

function averageArray(array) {
  return sumArray(array) / array.length
} 
```

Enter fullscreen mode Exit fullscreen mode

> 如果你正在写一个可以用“和”来命名的函数——它实际上应该是两个函数。

### 总结我对函数的提示...

[![an infographic of the function rules](../Images/4a2e9b5010bc85bfb16474929167c3cc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PJAp4TtW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8n7vhis0wmdjqr4fsayn.jpg)

## 3。证明文件

为您的代码编写好的文档，以便将来的开发人员理解您的代码在做什么以及为什么。

下面的代码中有没有记录的“神奇数字”。

```
function areaOfCircle (radius) {
  return 3.14 * radius ** 2
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在上面的代码中添加注释，让那些不知道求圆面积的数学公式的人更容易理解。

```
const PI = 3.14 // PI rounded to two decimal places

function areaOfCircle (radius) {
  // Implements the mathematical equation for the area of a circle:
  // Pi times the radius of the circle squared.
  return PI * radius ** 2
} 
```

Enter fullscreen mode Exit fullscreen mode

*注意:上面的代码只是一个例子！你可能会想用数学。而不是创建自己的 PI 估计。*

> 你的注释应该描述你的代码的“为什么”。

额外收获:在你的代码中使用文档风格。对于 Python 来说，我喜欢[谷歌风格的文档字符串](http://sphinxcontrib-napoleon.readthedocs.io/en/latest/example_google.html)，对于 JavaScript 来说 [JSDoc](http://usejsdoc.org/) 非常棒。

## 4。桑迪·梅茨法则

Sandi Metz——一位出色的 Ruby 开发人员、演讲者和作者——有四条用面向对象语言编写干净代码的规则。

1.  类不能超过 100 行代码
2.  方法和函数不能超过 5 行代码
3.  向一个方法传递不超过 4 个参数
4.  控制器只能实例化一个对象

我强烈推荐观看[她关于这些规则的完整演讲](https://www.youtube.com/watch?v=npOGOmkxuio)！

在过去的两年左右的时间里，我一直在遵循这些原则，它们变得如此根深蒂固，以至于我几乎不去想它们！我真的很喜欢它们，我认为它们使代码更易于维护。

> 这些规则只是指导方针，但是它们可以显著地清理你的代码。

### 总结桑迪梅茨规则...

[![an infographic of the Sandi Metz rules](../Images/4bb096a81ae01231be0f083b64e674e0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YRRcUTJ1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ze3xrjqe0wpk6tgwzjlr.jpg)

## 5。保持一致

编写代码时，一致性是关键。人们不应该能够看着代码库，准确地说出每一行代码是谁写的，而没有一点责备！如果您在 JavaScript 中使用分号，请在每条语句的末尾使用它们。始终如一地使用“vs ”!

我建议使用风格指南和 linter 来执行这些标准——例如，我喜欢 JavaScript 的 [Standard JS](https://standardjs.com/) 和 Python 的 [PEP8](https://www.python.org/dev/peps/pep-0008/?) ！我甚至设置了文本编辑器，以便在每次保存时执行这些标准！

> **找到一种代码风格并坚持下去。**

## 6。保持你的代码干燥

教给新程序员的第一件事就是“不要重复自己”。如果您注意到代码中的模式，请使用代码来减少这些重复。我经常鼓励我的学生玩游戏 [SET](https://www.nytimes.com/crosswords/game/set) 来提高他们的模式识别技能。

也就是说，如果你的代码过于枯燥，或者选择了错误的模式进行抽象，你的代码可能会变得不可读，你可能需要在以后的工作中重复你的代码。Sandi Metz 有一篇关于“复制远比错误的抽象便宜”的伟大文章

> 不要重复你自己，但也不要把你的代码抽象到无法理解的程度。

## 7。封装+模块化

将变量和函数分组，使你的代码更容易重用和理解。

```
let name = 'Ali'
let age = 24
let job = 'Software Engineer'

let getBio = (name, age, job) => `${name} is a ${age} year-old ${job}` 
```

Enter fullscreen mode Exit fullscreen mode

如果你的程序中有多个人，下面的内容会更清楚:

```
class Person {
  constructor (name, age, job) {
    this.name = name
    this.age = age
    this.job = job
  }

  getBio () {
    return `${this.name} is a ${this.age} year-old ${this.job}` 
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

或者如果你的剧本里只有一个人:

```
const ali = {
  name: 'Ali',
  age: 24,
  job: 'Software Engineer',
  getBio: function () {
    return `${this.name} is a ${this.age} year-old ${this.job}` 
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，将长程序分解成不同的文件，这样你的代码更加模块化和易于理解。长文件通常很难筛选，您可能希望在项目之间使用小块代码。

> 将代码中相似的项目分组，这样更容易重用。

## 简而言之...

这些是清理代码的一些很好的指导方针，但是它们并不是一成不变的！我个人并不是一直都用这些(看我的个人项目！)，而且没有一个代码是完美的。这些只是编写更容易重用、被其他开发人员阅读和扩展的代码的一些技巧。

如果你喜欢这篇文章，请保持联系！我每周五发一份简讯,上面有我喜欢的文章和我那周写的东西。还有，[发推特](https://twitter.com/ASpittel)告诉我你最喜欢的写干净代码的技巧！