# 什么是 For 循环？

> 原文：<https://dev.to/yechielk/what-the-for-loop-4pam>

#### 从一个流行的面试问题中学习

这是我在求职过程中遇到的另一个技术面试问题，以及如何着手解决这些问题(更多帖子见[这里](https://blog.yechiel.me/get-your-fizz-buzz-on-543f2a327a9d)、[这里](https://dev.to/yechielk/parenthetically-speaking-1b)和[这里](https://dev.to/yechielk/parenthetically-speaking-2-502))。

今天的问题很简单，但是正确的答案是大多数语言中一个非常流行的特性的基础；如此受欢迎，我们几乎从来没有想过它。

问题是这样的:写一个函数，打印 20 以内的所有奇数。

听起来很简单，对吧？任何初学者都会告诉你这是一个`for`循环的工作。所以我们把它写出来:

```
for(let i=0; i<=20; i++){
  if(i%2===1){
    console.log(i)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

用(过度)简化的英语来说:我们运行一个循环 20 次迭代，在循环的每次迭代中，我们检查变量`i`的值是否是奇数，如果是，我们将它打印到控制台。

以上功能满足要求。如果你在你的控制台中运行它，你会看到它做的工作。问题是，有没有更好的做事方法？

#### 什么 For 循环？

*注意:关于 JavaScript for loops 的更多信息，请参见 W3SchoolsT3 上的[文档](https://www.w3schools.com/js/js_loop_for.asp)*

显然是有的(否则这篇博文就没有意义了)；为了理解这一点，让我们仔细看看`for`循环的语法，更具体地说，第一行:

```
for(let i=0; i<20; i++){ 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到,`for`循环将由`;`分隔的三个语句作为参数。

第一条语句是一个表达式，在执行`for`循环之前只运行一次。它通常用于初始化计数器，但是您可以放入任何有效的 JavaScript 表达式，或者根本不放入(例如，如果您已经在 for 循环之外初始化了计数器)。在上面的例子中，第一条语句定义了一个变量`i`，并将其值设置为`0`。

第二个语句是一个条件语句，在循环的每次迭代之前进行计算。只要条件求值结果为`true`，循环就一直运行。一旦条件发生变化，使得第二条语句的计算结果为`false`，我们就退出循环。在我们的例子中，条件是`i < 20`，所以只要`i`的值低于`20`，循环就会运行。

第三种说法是另一种表达方式。该表达式在循环的每次迭代中在之后运行**。它通常用于递增计数器，但是同样，您可以将任何合法的 JavaScript 放入其中，它将运行(当然，如果您不使用它来递增计数器，您需要确保有另一种方法将第二个语句中的条件语句更改为 true，否则您将陷入可怕的无限循环)。**

[![Person rolling down Escher's infinite staircase](img/a82e056c6a53dab3cb695a1baceb4082.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0WBtLvEW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://i.imgur.com/RthAcqh.gif)

在我们之前的例子中，我们使用第三条语句在每次迭代后增加`i`的值，这样在 20 次迭代后`i`等于`20`，`i < 20`计算为`true`，我们退出循环。

#### 我们可以做得更好

现在让我们来看看我们的函数，看看我们如何优化它。

作为复习，下面是函数:

```
for(let i=0; i<=20; i++){
  if(i%2===1){
    console.log(i)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我们将`i`的值设置为零，并开始循环。在循环的每次迭代中，我们检查`i`的当前值，如果它是奇数，我们将它记录到控制台，然后我们将`i`增加 1，并重新运行循环，直到`i`达到 20，在这一点上我们中断循环。

我们如何优化这一点？

关键在第三条语句。如前所述，没有任何东西迫使我们在第三条语句中将计数器加 1；我们可以为所欲为。将这一知识与 1 是一个奇数的事实相结合，并且将 2 加到一个奇数上也得到一个奇数，结果是一个循环只需要运行我们之前尝试使用的迭代的一半。

试着把下面的代码放到你的控制台中，看看它是如何运行的:

```
for(let i=1; i<=20; i +=2 ){
  console.log(i)
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数与前一个函数的不同之处在于，这里我们将`i`的初始值设置为`1`，而不是每次迭代都将`i`增加 1，而是增加 2(我们还去掉了`if`语句，因为我们知道现在`i`总是奇数，所以我们每次只记录`i`的值，而不进行检查)。

因此，我们看到，当我们想要对它们进行超出通常使用方式的调整时，有时了解事情是如何工作的会对我们有所帮助。

我希望这篇文章能启发你有时更深入地研究，甚至是“人人都知道”的概念。

编码快乐！

* * *

*这篇文章是从我的博客 [Rabbi On Rails](https://blog.yechiel.me/for-loops-in-javascript-cda0388b6dd0) 交叉发布的。
你可以在那里了解更多关于我的编程之旅，或者在 Twitter 上关注我 [@yechielk](https://twitter.com/yechielk)*