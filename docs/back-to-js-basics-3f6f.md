# 回到 JS 基础

> 原文：<https://dev.to/dandevri/back-to-js-basics-3f6f>

> JS 会留在这里，没有你我会有麻烦。这里的理解是 JavaScript 基础知识的快速复习。

* * *

我们都读过几年前的这篇文章。(不！？你一直住在石头下面还是什么地方？)很容易陷入 JavaScript 库，永远出不来。我见过有人比纯粹的香草 JS 更了解 React 和 jQuery。

当我在阿姆斯特丹应用科技大学开始[小型网站开发](https://cmda.github.io/minor-everything-web/)时，我有一个(佛教徒称之为)初学者的心态。

> 它指的是在学习一门学科时，即使是在高级水平学习时，也要像这门学科的初学者一样，有一种开放、渴望和没有先入之见的态度。

我周围的人会坐在键盘后面，开始写一行又一行复杂的 JS 代码。我会进来，尽管我很好奇，也会问一些“简单”的问题，比如:

```
Where does this function gets invoked?
What are the initial arguments of this function?
Where do you declare this variable? 
```

Enter fullscreen mode Exit fullscreen mode

让我困扰的是，有些人不能给我直接的答案，这样我就可以向他们学习。不要误解我，大多数人都非常擅长处理复杂的 JS 东西(和库),但是对我来说，他们似乎被迷住了，忘记了一般的 JavaScript(编程)概念。

因此，考虑到这一点，我想写一篇关于基本 JS 概念的快速复习(是的，你可以称之为备忘单)。

## 编程 vs 脚本。

人们在争论*编程*或*编程语言*是否是描述 JavaScript 的正确术语。JavaScript 本身不袖手旁观，它需要使用一个**解释器**。在大多数情况下，这是浏览器或独立的 JS 引擎。它不是本机代码(二进制 01010)，但它需要被**编译**，这就是浏览器发挥作用的地方。

为了安全起见，我称 JavaScript 为脚本语言，它属于更大的类别，即 T2 编程语言。

## 变量。

变量存储`data`并操纵它。他们通过使用一个**标签**指向数据来做到这一点。在 JavaScript 中，数据可以是以下 7 种数据类型之一:

*   `undefined`
*   `null`
*   `boolean`
*   `string`
*   `symbol`
*   `number`
*   `object`

它们可以在不同的时间存储不同的值。

*   要**声明**一个变量:`var`关键字带`myVar`变量名。
*   **用**赋值运算符** : `=`将**值存储在一个变量中
*   **用数据类型`19`(在本例中是一个数字)初始化**变量

```
var myVar = 19; 
```

Enter fullscreen mode Exit fullscreen mode

一个只有`declared`而没有`initial`值的变量有`undefined`的值。

```
var a; // value is undefined 
```

Enter fullscreen mode Exit fullscreen mode

## 功能。

你可以通过使用**函数**将你的代码分成可重用的部分。

```
function myFunction() {
// do stuff
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过使用函数名并将**括号**放在它们后面来`call`或`invoke`函数。

```
myFunction() 
```

Enter fullscreen mode Exit fullscreen mode

`parameters`基本上是变量，当函数被调用时，它们充当输入到函数中的值的占位符。你**向**传递一个值作为函数的输入。许多人交替使用术语**参数**和**参数**，但是有一点细微的区别。

```
function myFunction(param1, param2) { // These are parameters
   console.log(param1 + param2);
}

myFunction(1, 2); // These are arguments 
```

Enter fullscreen mode Exit fullscreen mode

当函数被调用时，**传递给函数**的值是**参数**。占位符是**参数**。

## 阵列。

使用数组，你可以在一个变量中存储多个`data types`。你用和变量一样的方式来声明它，但是你用`[]`将值括起来，并用逗号分隔每个值。数组中的每个值被称为一个`entry`。

您可以使用索引来访问数组内部的数据。数组使用`zero-based indexing`。所以数组中的第一项以一个`0`开始。

## 物体。

对象类似于数组，除了不使用索引，而是通过所谓的**属性**来访问对象中的数据。每个属性都由一个*键*和一个*值*组成。

```
var dog = {
  "name": "Peter",
  "legs": "4"
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

上面的内容是一些核心 JavaScript 术语的基本轮廓。一些我需要每天提醒自己的事情。人们，依我看，需要经常更新的东西。希望这有所帮助，下次你想听起来很酷；尽量使用正确的术语。👌

> 感谢阅读！你可以在 [@dandevri](https://dev.to/dandevri) 上关注我，如果你有什么想说的，随时联系我！