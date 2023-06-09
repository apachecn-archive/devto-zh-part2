# 编码字节:第 1 部分— (Intro &)数据类型

> 原文：<https://dev.to/waqardm/coding-bytes-part-1intro--data-types-2nbc>

# 介绍

*本帖最初发布在[媒体](https://medium.com/@lawyerscode/coding-bytes-part-1-intro-data-types-689b3c299d5a)T3 上*

我已经学习开/关编码大约 2 年了。多次从马车上掉下来。与几年前相比，现在可用的资源要好得多，也更容易获得，尽管我仍然认为自己是个新手，但我现在对周围的事情有了一些了解。我知道可能导致问题的原因，并且知道在哪里尝试解决问题。

我在算法方面仍然是垃圾，但这是我旅程的一部分，我知道我最终会解决的。这是一段艰难的旅程，尤其是在开始的时候。即使网上有足够的支持，陷入困境也可能是一场噩梦！解决一个摆在你面前的问题有时需要几个小时，但我已经接受这也是学习过程的一部分。我相信这是开始时最难的部分——自己解决问题，不要放弃。我也接受了这样一个事实:我可能比别人学得/干得慢，无论我做什么，我都学不会所有的东西。大多数人会告诉你冒名顶替综合症太普遍了，但唯一能做的就是坚持到底。

人们常说，最好的学习方法是教一些东西。虽然我还有很多东西要学，但我想开始一个系列来巩固我的学习，并通过编程的基础工作，同时帮助其他人学习编码(我希望！).我已经决定了一种旨在减少信息过载的格式，因此将其命名为“编码字节”。这个想法是，小块的信息可以更容易地被消化，当与其他信息放在一起时，神奇的事情就会发生！

在每篇文章中，我将致力于涵盖一个主题/概念，而不将它与任何其他原则或主题联系起来。我希望，通过这种方式，学习者可以迈出一小步，并在整个过程中减少冒名顶替综合症。

## 序言…

我将使用 Javascript 作为选择的编程语言(别担心，所有语言本质上都是一样的！)，仅仅是因为它的易用性，以及它可以直接从您的浏览器开箱即用。我制作了一个视频，展示你如何在这里做到这一点。

**基本数据类型**
我们处理的最基本的信息被称为‘数据类型’。所有这些都意味着我们将要处理的数据类型，例如，一个字母、一个单词或一个段落都可以作为数据类型的真实例子。在编程中，我们使用以下数据类型(带示例):

**整数**T3】整数是正数或负数。`4 or -12`

**浮点数**
一个带小数的数。`3.75`

虽然在 Javascript 中整数和浮点属于常见的数字类型，但我有意将它们分开，因为这些术语在许多其他语言中都很常见。

**串**
一个单词或句子或更多。因为字符串也可以包含数字。请注意封装字符串的引号。`“string”, “this is also a string”`

**布尔**
布尔是一个`true`或`false`输出。有时`true`也被称为`1`，`false`被称为`0`。

## 其他数据类型

**变量**

变量可以解释为一个可重复使用的单词或盒子，我们可以用它来存储信息并在以后更新它。有几种方法可以声明变量，但是我们将坚持使用最基本的 var。

`var age = 4;`

在上面的声明中，我让计算机知道我希望通过使用 var 关键字声明一个变量，然后调用变量' age '。现在我们有一个名为 age 的值为 4 的变量。随着我们的进展，赋值的原因将变得显而易见。

**数组**

描述数组最简单的方法是想象一个列表或集合。待办事项列表是一个数组。列表本身是一个数组，而每个待办事项是一个数组元素。注意，我们的数组存储在一个名为“list”的变量中。

```
var list = ["pick up shopping", "call the doctor", "book tickets"]; 
```

还有更多的数据类型，但这些现在应该做得很好，因为这篇文章由于介绍而变得比我想要的长了一点。

*感谢阅读。为了跟上我的编码之旅，来打个招呼吧👋在推特上。在[@ lawyers code](https://twitter.com/lawyerscode)T3 可以找到我*