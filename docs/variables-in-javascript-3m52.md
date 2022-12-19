# JavaScript 中的变量

> 原文：<https://dev.to/codetheweb/variables-in-javascript-3m52>

[![Variables in JavaScript](img/eb314591f12752636f6fe568b3e58a7e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LyeGMs-k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/javascript-variables/cover.jpg)

变量是 JavaScript 的核心部分——事实上，它们是几乎所有编程语言的核心部分。它们让你存储信息，也是 JavaScript 如此强大的基础。在这篇文章中了解它们...

## 什么是变量？

变量只是一个存储东西的地方。事实上，你可以把一个变量想象成一个橱柜。首先，我们需要*创建*橱柜来存放东西。接下来，我们要在橱柜里放些东西。之后，我们会想要检索并使用该项目。我们可能还想用另一个项目替换这个项目。

是时候看看它的使用了！假设我们想要提醒数字`25`。通常，我们会这样做:

```
alert(25); 
```

Enter fullscreen mode Exit fullscreen mode

但是用变量来做这件事怎么样呢？下面是方法:

```
var x = 25;
alert(x); 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看这里发生了什么——首先，我们通过说`var x`来*创建*(又名*声明*)我们的变量`x`。在同一行，我们也通过说`= 25`给变量一个初始值来存储。

现在，当我们运行这段代码时，它会提醒`25`！这是因为`x`现在只是数字 25 的占位符。这就像你正在编辑一个文档，想要替换单词“banana”的所有实例一样——在这种情况下，浏览器遍历我们的代码，用`25`替换变量`x`的所有实例。

> PS:跟着走！创建一个名为`index.html`的新文件，并添加以下代码:
> 
> ```
> <!DOCTYPE html>
> <html>
>     <head>
>         Hello world in JavaScript!
>     </head>
>     <body>
>         <h1>JavaScript :)</h1>
>         <script>
>             var x = 25;
>             alert(x);
>         </script>
>     </body>
> </html> 
> ```
> 
> 现在您可以将所有的 JavaScript 放在`<script>`标签中了！🎉

但是这些有什么关系呢？嗯，变量的真正力量在于你可以*改变*它们。比如:

```
var x = 25;
x = 85;
alert(x); 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，设置一个已经声明的变量就像说`x = 85`一样简单。你不需要`var`位，因为它已经被声明了。

现在，我们可以提醒`85`，甚至不用改变显示`alert`的行！这现在看起来可能很奇怪，但是变量是 JavaScript 的核心部分之一，在编写更复杂的代码时会更方便。

## 变量类型

在 JavaScript 中，有不同的变量类型。这是因为它需要知道我们给变量的是什么类型的数据，因为计算机比你更笨。注意**你可以在一个变量被声明后把它改成不同的类型，它不需要保持相同的类型。**以下是 JavaScript 中的一些基本数据类型:

### 字符串

一个*字符串*是一些文本的另一种说法。我们必须用引号将它括起来，否则它会被认为是一个变量。这里有一个例子:

```
var x = "I am a string!"; 
```

Enter fullscreen mode Exit fullscreen mode

单引号或双引号都没关系:

```
var x = 'I am a string!'; 
```

Enter fullscreen mode Exit fullscreen mode

### 数字

显然，我们也可以将一个变量设置为一个*数字*——就像我们之前做的那样！可以是整数:

```
var x = 16; 
```

Enter fullscreen mode Exit fullscreen mode

或者小数:

```
var x = 3.14; 
```

Enter fullscreen mode Exit fullscreen mode

### 布尔

*布尔值*就是简单的`true`或`false`值:

```
var x = true;
var y = false; 
```

Enter fullscreen mode Exit fullscreen mode

这听起来可能很简单，但是当使用 [`if`语句](https://codetheweb.blog/2018/02/28/if-statements-boolean-operators/)时，它们会派上用场——它让你根据语句是否等于`true`或`false`来执行你的代码。

## 结论

耶！你现在对变量了如指掌！让我们复习一下今天所学的内容。

首先，我们学习了如何声明、设置和更改变量:

```
var x = 12; /* Declared and set a variable */

x = "Helloooo!"; /* Change a variable (you can change it to a different type) */ 
```

Enter fullscreen mode Exit fullscreen mode

我们还学习了一些基本的数据类型:

```
var x = 2.71; /* Number */

var y = 'I <3 JavaScript!'; /* String */

var z = false; /* Boolean */ 
```

Enter fullscreen mode Exit fullscreen mode

我很荣幸成为你的编码之旅的一部分，我希望这对你有用！如果是这样的话，你为什么不[加入时事通讯](https://codetheweb.blog/newsletter/)并在你的收件箱里收到更多这样的文章呢？另外，我会爱你作为回报< 3

另外，如果你真的很好，并且有 4 美元闲钱，如果你给我买一杯☕咖啡，我会非常感激。谢谢！

如果你认为这篇文章真的能帮助到谁，别忘了与他们分享！最后，如果你想和我(或其他人)聊天，请点击下方的[评论。](https://codetheweb.blog/2018/02/25/javascript-variables/#disqus_thread)

下次见，届时我将讨论 JavaScript 中的 [if 语句，以及我们如何比较变量来创建`true`和`false`值。到时候见！](https://codetheweb.blog/2018/02/28/if-statements-boolean-operators/)

> ### 想要更多这样的帖子？[注册我的简讯](https://codetheweb.blog/newsletter/)。
> 
> 我一直在努力学习这些教程，但是到目前为止还没有赚到任何钱，所以如果你注册的话，我会非常感激；)

这篇文章最初发表在 [Code The Web](https://codetheweb.blog) 上，这是一个有趣且易于理解的博客，帮助你学习 Web 开发🚀
学:[HTML](https://codetheweb.blog/learn/html/)|[CSS](https://codetheweb.blog/learn/css/)|[JavaScript](https://codetheweb.blog/learn/javascript/)