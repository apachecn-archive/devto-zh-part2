# JavaScript 函数完全指南

> 原文：<https://dev.to/codetheweb/the-complete-guide-to-javascript-functions-2i0l>

[![The complete guide to JavaScript functions](img/28e5ba72d6588dea78dd8522d1ebeb92.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w7lNMaYS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/javascript-functions/cover.jpg)

## 入门！

首先，我鼓励你跟随这篇文章。这将有助于你更好地学习，也有助于你记住你做了什么。让我们首先创建一个新的 HTML 文件，其中包含一个`<script>`标签:

```
<!DOCTYPE html>
<html>
    <head>
        If statements are awesome!
    </head>
    <body>
        <h1>JavaScript :)</h1>
        <script>
            // Our script will go here!
        </script>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

一旦完成，在你的网络浏览器中打开它，你就可以开始了！(不要忘记在每次更改时保存并重新加载页面)

## 什么是函数？

函数只是一小段 JavaScript 代码，可以反复运行。你甚至可以给它不同的输入和输出！

## 语法

现在，让我们来看看最简单的函数类型:

```
function myFirstFunction() {
    var x = 5;
    alert(x * 2);
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们声明函数。我们通过说出单词`function`然后说出你的函数名来做到这一点。这类似于我们如何声明变量(`var variableName`类似于`function functionName`)。名字后面有一组空括号/圆括号/随便你怎么称呼这些东西:`()`。

如果它们是空的，那还有什么意义呢？嗯，这是我们为函数放置*输入*的地方。目前，我们的函数没有任何输入，所以我们让它为空。在本文中，我将更深入地讨论输入(兴奋起来😉).

之后，我们有一组花括号(结束的在第四行)。在这些花括号里是函数中的所有代码。

## 运行(又名*【呼叫】*)一个功能

现在你知道它是如何工作的了，让我们运行它吧！将它放入您的`<script>`标签中，保存并重新加载页面...

什么！？？什么都没发生！嗯，事情是这样的——我们只声明了*函数*,而没有实际运行它。我们刚刚说“嘿，神奇的计算机，这是这段代码，它叫做`myFirstFunction`”。但是，我们还没有告诉计算机运行函数内部的代码！我们是这样做的:

```
myFirstFunction(); 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们通过引用它的名字来运行它。这样我们就有了一组空的括号。这是函数输入的地方，但是我们还没有任何输入，所以我们还是让它们为空。是时候运行您的函数了！看看你能不能找出把它放在代码的什么地方:

```
function myFirstFunction() {
    var x = 5;
    alert(x * 2);
}

myFirstFunction(); 
```

Enter fullscreen mode Exit fullscreen mode

在这段代码中，它在末尾。但是，也可以在函数之前！这与变量非常不同，在变量中，您必须在使用变量之前声明变量*。这不行:* 

```
alert(myVariable);

var myVariable = "Hello"; 
```

Enter fullscreen mode Exit fullscreen mode

然而，这将:

```
myFirstFunction();

function myFirstFunction() {
    var x = 5;
    alert(x * 2);
} 
```

Enter fullscreen mode Exit fullscreen mode

通常的做法是将所有的函数定义放在脚本的底部，但这并不重要。

哦，好了，这是你期待已久的时刻——是时候尝试一下了！确保您的脚本看起来像上面的代码(尽管请记住，在声明函数之前或之后运行它并不重要)。保存，重新加载和哒哒！函数中的代码现在已经运行了！

乍一看，这似乎有些矫枉过正——为什么不直接运行代码呢？想象一下，如果您想让这段代码在脚本中的许多不同点运行——在循环、if 语句等等。好吧，那么只需要说`myFirstFunction();`就会派上用场！尤其是如果您的函数有 30 行长😅

## 带输入的功能

在函数`myFirstFunction`中，我们将变量(`x`)设置为`5`，然后对其进行报警(`10`)。如果我们做一个名为`alertDouble`的函数来提醒 double *任何一个*数字会怎么样？

在这种情况下，我们有一个输入—让我们称之为`num`。下面是用`num`作为输入的函数声明:

```
function alertDouble(num) {

} 
```

Enter fullscreen mode Exit fullscreen mode

现在在花括号内，我们可以使用`num`作为变量！(**只在花括号**里面)

看看你能不能想出完成后的函数是什么样子...

```
function alertDouble(num) {
    alert(num * 2);
} 
```

Enter fullscreen mode Exit fullscreen mode

你做对了吗？如果是这样，太好了！如果没有，不要担心——希望在本文结束时，您会开始明白🙏

## 带输入运行功能

是时候运行我们的函数了！让我们尝试将几个不同的数字翻倍:

*   Two
*   Seventy
*   One thousand and twenty-four

首先，你可以把这部分你现有的代码留下来，如果你愿意:

```
function myFirstFunction() {
    var x = 5;
    alert(x * 2);
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，我们已经完成了，不想再运行它了。所以，删除或注释掉这一行:

```
myFirstFunction(); 
```

Enter fullscreen mode Exit fullscreen mode

相反，我们想调用我们的新函数`alertDouble`！首先，我们需要在代码末尾添加函数声明:

```
//myFirstFunction();

function myFirstFunction() {
    var x = 5;
    alert(x * 2);
}

function alertDouble(num) {
    alert(num * 2);
} 
```

Enter fullscreen mode Exit fullscreen mode

是时候运行我们的函数了！如果它没有任何输入，我们就简单地说`alertDouble()`。然而，我们需要将`num`的输入放在括号内！所以，让我们试试 double 2——看看你能不能猜出代码！

```
alertDouble(2); 
```

Enter fullscreen mode Exit fullscreen mode

括号内的`2`在函数运行时变成了函数内的`num`！尝试一下...

```
//myFirstFunction();

alertDouble(2);

function myFirstFunction() {
    var x = 5;
    alert(x * 2);
}

function alertDouble(num) {
    alert(num * 2);
} 
```

Enter fullscreen mode Exit fullscreen mode

呜！它惊动了`4`！🎉
让我们试试其他值...

```
//myFirstFunction();

alertDouble(2);
alertDouble(70);
alertDouble(1024);

function myFirstFunction() {
    var x = 5;
    alert(x * 2);
}

function alertDouble(num) {
    alert(num * 2);
} 
```

Enter fullscreen mode Exit fullscreen mode

耶！现在它警告`4`、`140`然后是`2048`。是时候继续前进了...

## 多输入功能

在函数`alertDouble`中，我们将`num`乘以`2`。如果我们想创建一个名为`alertMultiplied`的函数，在这个函数中，我们用一个输入乘以另一个输入来发出警报，会怎么样呢？这里我们需要两个输入:比如说`num1`和`num2`。

首先我们需要声明函数！当函数有多个输入时，它们用逗号分隔——看看你能不能算出来！

```
function alertMultiplied(num1, num2) {

} 
```

Enter fullscreen mode Exit fullscreen mode

然后，就是简单的报警`num1`乘以`num2`！

```
function alertMultiplied(num1, num2) {
    alert(num1 * num2);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 运行多输入功能

首先，像这样注释掉(或删除)所有的`doubleMe`函数调用:

```
//myFirstFunction();

//alertDouble(2);
//alertDouble(70);
//alertDouble(1024);

function myFirstFunction() {
    var x = 5;
    alert(x * 2);
}

function alertDouble(num) {
    alert(num * 2);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们将`alertMultiplied`函数声明添加到代码的末尾！

```
//myFirstFunction();

//alertDouble(2);
//alertDouble(70);
//alertDouble(1024);

function myFirstFunction() {
    var x = 5;
    alert(x * 2);
}

function alertDouble(num) {
    alert(num * 2);
}

function alertMultiplied(num1, num2) {
    alert(num1 * num2);
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，该运行函数了。让我们试试用`3`乘以`5`，用`21`乘以`8`。看看你是否能写出代码(提示:输入用逗号分隔，就像在声明中一样😉)

```
//myFirstFunction();

//alertDouble(2);
//alertDouble(70);
//alertDouble(1024);

alertMultiplied(5, 3);
alertMultiplied(8, 21);

function myFirstFunction() {
    var x = 5;
    alert(x * 2);
}

function alertDouble(num) {
    alert(num * 2);
}

function alertMultiplied(num1, num2) {
    alert(num1 * num2);
} 
```

Enter fullscreen mode Exit fullscreen mode

保存，重新加载，看看会发生什么！它应该先警告`15`，然后再警告`168`。

## 有输出的函数(返回某物的函数)

除了有输入，函数也可以有输出！然而，一个函数只能有一个输出(与输入不同)。

我们来做一个函数叫做`triple`。不过和`alertDouble`不同的是，它不会提示结果。它会输出它！

为了输出结果，我们只需在函数`return`中输入我们想要输出的内容。这里有一个例子:

```
function introduce() {
    return 'Hello, I'm Johnny.';
} 
```

Enter fullscreen mode Exit fullscreen mode

注意它*不需要括号！然而，你可以把它们放进去，因为它们是可选的。我更喜欢把它们放进去，因为这样更容易理解，但这取决于你。带括号的看起来像这样:* 

```
function introduce() {
    return('Hello, I'm Johnny.');
} 
```

Enter fullscreen mode Exit fullscreen mode

显然，返回值不一定是字符串。

让我们试着制作我们的`triple`函数！它需要一个输入`num`。它需要输出三倍于此的数字。看看你能不能算出这个函数是什么样子的！

```
function triple(num) {
    return(num * 3);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 运行有输出的功能(获取输出)

这一切都很好，但是我们实际上如何得到这个函数的输出呢？我们通过运行它来做到这一点！

实际的函数调用将变得等于输出，就像变量等于值一样。然后我们可以像使用变量一样使用这个函数。例如，我们可以这样说:

```
alert( triple(10) );

function triple(num) {
    return(num * 3);
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们警告`triple(10)`的输出。试试吧！不要忘记添加声明并注释掉`alertMultiplied` :
的调用

```
//myFirstFunction();

//alertDouble(2);
//alertDouble(70);
//alertDouble(1024);

//alertMultiplied(5, 3);
//alertMultiplied(8, 21);

alert( triple(10) );

function myFirstFunction() {
    var x = 5;
    alert(x * 2);
}

function alertDouble(num) {
    alert(num * 2);
}

function alertMultiplied(num1, num2) {
    alert(num1 * num2);
}

function triple(num) {
    return(num * 3);
} 
```

Enter fullscreen mode Exit fullscreen mode

呜！代码会提醒`30`！我们也可以像使用其他号码一样使用`triple`函数:

```
var x = (triple(6) + 3) * 2;
alert(x); 
```

Enter fullscreen mode Exit fullscreen mode

这简直就像说`(18 + 3) * 2`，等于`42`(当然是生命的意义😉).试试吧！

```
//myFirstFunction();

//alertDouble(2);
//alertDouble(70);
//alertDouble(1024);

//alertMultiplied(5, 3);
//alertMultiplied(8, 21);

alert( triple(10) );
var x = (triple(6) + 3) * 2;
alert(x);

function myFirstFunction() {
    var x = 5;
    alert(x * 2);
}

function alertDouble(num) {
    alert(num * 2);
}

function alertMultiplied(num1, num2) {
    alert(num1 * num2);
}

function triple(num) {
    return(num * 3);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在在提醒`30`之后，我们的代码也会提醒`42`！🎉

## 函数内的变量作用域

我们马上就要完成了！😅然而，关于函数，还有一件更重要的事情你需要知道——在函数内部声明的变量在函数外部不起作用。怎么了？这里有一个例子——试试看！

```
function myFunc() {
    var myVar = 3;
}

myFunc();
alert(myVar); 
```

Enter fullscreen mode Exit fullscreen mode

应该是 3 号警报吧？没有。因为`myVar`在函数内被声明为*，所以它不存在于函数外。代码将不起作用，并显示以下错误:*

> 未捕获的引用错误:未定义 myVar

但是，这个*确实*管用！

```
var myVar = 1;

function myFunc() {
    myVar = 3;
}

myFunc();
alert(myVar); 
```

Enter fullscreen mode Exit fullscreen mode

代码会提醒`3`！因为`myVar`已经在函数外声明了*，所以代码知道它的存在。它将在函数内部被更改，然后代码仍然可以提醒它，因为它是在函数外部定义的。*

这就是所谓的*全局*范围。这意味着对于代码的其余部分，`myVar`将被定义。在第一个例子中，`myVar`是在函数内部声明的，它是在*函数*范围内声明的。这意味着变量将只在函数的花括号内定义。

这可能需要一段时间来理解，但你最终会明白的。你需要记住的主要事情是**如果你想在函数外使用一个变量，首先在函数外声明它**。你甚至不必说`var myVar = 1;`——你可以直接说`var myVar;`就够了。我可能会在[未来](https://codetheweb.blog/newsletter)写一篇关于可变作用域的文章。

## 结论

咻！😅有太多要讲的了，所以一路走来做得很好！一如既往的坚持练习，你最终会掌握的。如果你需要任何帮助，请随时[给我发电子邮件](https://codetheweb.blog/contact)或[点击评论](https://codetheweb.blog/2018/04/05/javascript-functions/#disqus_thread)。

如果你从这篇文章中学到了什么，如果你给我买杯咖啡，我会非常感激。我已经写了 53，000 字，全部免费计算，来帮助像你这样的人学习 web 开发。如果你能给我一点小费作为回报，那就太好了😉。虽然看起来不多，但加起来就多了。如果每个在这个网站上阅读文章的人都给我 1 美元，我现在可能已经接近最低时薪了！

如果你知道有人会从这篇文章或这个博客中受益，那就去和他们分享吧，他们会感谢你的😜

最后，如果你想每周在你的收件箱里收到一些网络开发的好东西来帮助你学习 JavaScript，输入你的电子邮件，我会照做的😜

### [点击这里报名:D](https://codetheweb.blog/newsletter)

好了，今天到此为止！祝您编码愉快，下次再见，届时我将谈论所有关于[数组(又名。列表)在 JavaScript](https://codetheweb.blog/newsletter) 中。到时候见！👊

* * *

> ### 想要更多这样的帖子？[注册我的简讯](https://codetheweb.blog/newsletter/)。
> 
> 我一直在努力学习这些教程，但是到目前为止还没有赚到任何钱，所以如果你注册的话，我会非常感激；)

这篇文章最初发表在 [Code The Web](https://codetheweb.blog) 上，这是一个有趣且易于理解的博客，帮助你学习 Web 开发🚀
学:[HTML](https://codetheweb.blog/learn/html/)|[CSS](https://codetheweb.blog/learn/css/)|[JavaScript](https://codetheweb.blog/learn/javascript/)