# JavaScript 中的 For 循环和 while 循环

> 原文：<https://dev.to/codetheweb/for-loops-and-while-loops-in-javascript-3a1d>

[![For loops and while loops in JavaScript](../Images/6f4769c42ffeef2a73dd0fb112dcedee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ThFcgDrf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/for-and-while-loops-javascript/cover.jpg) 
循环让你想运行多少次代码就运行多少次！他们真的很强大，这是一件值得了解的事情。从《编写 Web 代码》这篇文章中一劳永逸地学习它们吧！

## 入门！

欢迎来到另一篇关于编写 Web 代码的帖子！首先，我想鼓励你跟随这篇文章。这将有助于你更好地学习，也有助于你记住你做了什么。让我们首先创建一个新的 HTML 文件，其中包含一个`<script>`标签:

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

## JavaScript 中什么是循环？

循环只是多次运行一段代码。例如，看看这段代码:

```
alert('Hi!'); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想重复五次，我们可以这样做:

```
alert('Hi!');
alert('Hi!');
alert('Hi!');
alert('Hi!');
alert('Hi!'); 
```

Enter fullscreen mode Exit fullscreen mode

然而，这是非常糟糕的编程，如果您需要数百次重复大块代码，会变得非常笨拙。而且，也不是很有用。例如，如果想让它重复`x`次呢？这就是循环派上用场的地方。次数的多少往往由变量决定，但也可以由实际数字决定。循环对于处理一个[数组](https://codetheweb.blog/newsletter)(列表中的每一项)中的每一个元素特别有用，但是在很多情况下还是很方便的。

## While loops

> 等等，如果我们先做 while 循环，那为什么标题以“for”开头？我真的不知道，只是这样听起来更好。

当某个语句为`true`(即直到它为`false`)时，while 循环不断重复代码。下面是语法:

```
while ( /* something that's true or false */ ) {
    // Do some stuff
} 
```

Enter fullscreen mode Exit fullscreen mode

就这么简单！这里有一个例子，它从`0` - `1`一直寻找一个随机数，直到它结束`0.2`。

```
while(Math.random() > 0.2) {
    alert('hi');
} 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我们想让它提醒这个号码呢？我们不能写`alert(Math.random())`,因为这样它会发现一个新的*随机数来报警。所以，我们必须创建一个变量，然后在每次循环结束时，将它设置为随机数。然后它会比较*这个*数字，并把它存储在一个变量中，这样我们可以在以后提醒它:* 

```
var randomNum = Math.random();

while(randomNum > 0.2) {
    alert(randomNum);
    randomNum = Math.random();
} 
```

Enter fullscreen mode Exit fullscreen mode

花点时间看看这段代码。首先，我们将变量`randomNum`设置为一个随机数。然后我们看看它是否大于 0.2。如果是，我们运行花括号里的内容。这将提醒该号码，然后将`randomNum`更改为新号码。然后我们检查这个数字是否大于`0.2`。如果是，我们再次运行花括号内的内容，如此循环下去...

如果数字是大于`0.2`的*而不是*，括号中的东西不运行，程序继续运行。它**不**然后继续运行，直到数字再次大于`0.2`。

> ## ☢☢的一句警告...避免无限循环！☢☢
> 
> 无限循环是将永远运行的循环。for 循环*和* while 循环都可能发生这种情况。这里有一个非常基本的例子——**不要尝试它，除非你很乐意丢掉你所有的标签**

```
while(true) {
    // some code
} 
```

Enter fullscreen mode Exit fullscreen mode

> 它将冻结网页，甚至整个浏览器取决于你的电脑。您通常无法关闭标签，可能需要退出并重新打开整个浏览器！**所以，要时刻警惕无限循环。**它们可能不总是像`while(true)`那样明显。它可能是*任何一个*语句，将总是计算为`true`。

## 为循环

假设你想提醒“嗨”五次。我前面说过，这是一种非常糟糕的做事方式:

```
alert('Hi!');
alert('Hi!');
alert('Hi!');
alert('Hi!');
alert('Hi!'); 
```

Enter fullscreen mode Exit fullscreen mode

然而，我们如何用 while 循环来实现这一点呢？我们必须设置一个计数器变量，然后每次给它加一:

```
var counter = 1;

while(counter <= 5) {
    alert('Hi!');
    counter = counter + 1;
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，让我们让这段代码遵循更好的编程约定。不使用`counter`变量，我们应该将其命名为`i`。这只是一个约定，代表“迭代”。您不必使用`i`，但这样做是个好主意。这将使你的代码更容易与其他开发者的代码集成，并使一切更加一致。

此外，我们将在`1`启动计数器。在编程中，在`0`上启动计数器变量是一个惯例。为什么不是`1`？嗯，在大多数编程语言中，[数组](https://codetheweb.blog/newsletter) (list)的第一个元素是项目`0`，而不是项目`1`。由于循环通常与数组有关，所以在`0`开始所有 for 循环是一个好的编程习惯。虽然一开始看起来有些混乱，但是随着您学习 JavaScript 的进展，它会使您的代码变得不那么混乱。因为`i`现在少了一个，我们会说`counter < 5`而不是`counter <= 5`。

最后，在这一行，我们将`i`加 1。不要说`i = i + 1`，有更好的方法可以做到这一点！你可以简单地说`i++;`。

因此，下面是我们的 while 循环在遵循良好的编程惯例(“最佳实践”)后的样子:

```
var i = 0;

while(i < 5) {
    alert('Hi!');
    i++;
} 
```

Enter fullscreen mode Exit fullscreen mode

信不信由你，使用 JavaScript 有一种更快、更好、更简单的方法来做到这一点！这是通过使用 for 循环:

```
for(var i = 0; i < 5; i++) {
    alert('Hi!');
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们来看看这段代码。这很像 while 循环，除了所有不同的部分都被移动了。同样，我们使用`i`作为计数器变量。

首先，我们将`i`设置为`0`。我们不是在循环之前做这些，而是在 for 循环的括号内做，就在开始的时候。

在分号之后，我们有一个[布尔](https://codetheweb.blog/2018/02/28/if-statements-boolean-operators/)表达式(计算结果为`true`或`false`)。这类似于 while 循环中的部分，并且以相同的方式操作！花括号内的内容将会运行，而该语句相当于`true`(在本例中为`i < 5`)。

在第二个分号之后，我们将最后一段代码放在括号内:`i++`。这是 while 循环中花括号内的最后一行！简而言之，第二个分号后的代码将在每次循环结束时运行。

下面是 for 循环语法的总结，以便于理解:

```
for( /* Set your counter variable */ ; /* Repeat while this is true */ ; /* Run this at the end of each repetition */ ) {
    // Your code!
} 
```

Enter fullscreen mode Exit fullscreen mode

(将鼠标悬停在代码上，向侧面滚动以查看全部内容，或者在触摸屏上向侧面拖动)

试着做一个 for 循环，提醒“Yay”三次。**不要用重复五次的代码来帮助！**看看你是否能用语法代码来帮助你。

```
for( /* Set your counter variable */ ; /* Repeat while this is true */ ; /* Run this at the end of each repetition */ ) {
    // Your code!
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

看看你是不是答对了！

```
for(var i = 0; i < 3; i++) {
    alert('Yay');
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

同样，你可以在花括号中的代码中使用`i` *！虽然这在遍历[数组](https://codetheweb.blog/newsletter)时非常方便，但是它也可以按照你想要的方式使用。例如，从 0 - 4 提示数字:* 

```
for(var i = 0; i < 5; i++) {
    alert(i);
} 
```

Enter fullscreen mode Exit fullscreen mode

注意是从 0 - 4 不是 1 - 5，因为所有循环都应该从 0 开始！如果你想提醒数字 1 - 5，我会这样做:

```
for(var i = 0; i < 5; i++) {
    alert(i + 1);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

恭喜你学会了 for 循环和 while 循环！我知道我在学习的时候，发现 for 循环的概念特别混乱。记住括号内的三个部分是很难的，我最终得到它的唯一方法是通过练习。所以试着用 for(和 while)循环做一些不同的事情吧！这是学习它的最好方法。

如果你从这篇文章中有所收获，如果你[分享它](https://codetheweb.blog/2018/03/25/for-and-while-loops-javascript/#share)或者[请我喝咖啡](https://buymeacoff.ee/CodeTheWeb)，我会非常感激。这 4 美元够用很长时间了，让我今天余下的时间过得棒极了。

如果我把时间花在编写网页代码上，而不是拿最低工资，我现在已经赚了 1770 美元了！但是我却把我的时间奉献给了你们，奉献给了帮助人们学习，并且只挣了 16 美元。所以如果你能[回报一点点感谢就太好了](https://buymeacoff.ee/CodeTheWeb)，我会非常感激的！😘

如果你想收到更多像这样的关于 web 开发的文章，以及来自网络上的精彩的 web 开发文章，一定要注册每周时事通讯！我每周只发一封电子邮件，里面有一些精彩文章的综述。我将把这个方便的小表格留在下面...

### [点击这里报名:D](https://codetheweb.blog/newsletter)

下次再见，我将会谈到 JavaScript 中的[函数！](https://codetheweb.blog/2018/04/05/javascript-functions/)它们是 JavaScript 最重要的组成部分之一(我知道我已经说了差不多 4 篇文章了，但这是真的😉).[到时候见！](https://codetheweb.blog/2018/04/05/javascript-functions/)

> **P.S .在这个帖子里，我正式过了 5 万字！🎉😁写这篇博客的时间太棒了(快 6 个月了！)，而我也一定会一直延续到未来！我要感谢所有的粉丝、[简讯订阅者](https://codetheweb.blog/newsletter)和读者，感谢他们真的来看我要说的话。我真的没有想到代码网会有这么多的访问者，真的很感谢你们每一个人。💖**

* * *

> ### 想要更多这样的帖子？[注册我的简讯](https://codetheweb.blog/newsletter/)。
> 
> 我一直在努力学习这些教程，但是到目前为止还没有赚到任何钱，所以如果你注册的话，我会非常感激；)

这篇文章最初发表在 [Code The Web](https://codetheweb.blog) 上，这是一个有趣且易于理解的博客，帮助你学习 Web 开发🚀
学:[HTML](https://codetheweb.blog/learn/html/)|[CSS](https://codetheweb.blog/learn/css/)|[JavaScript](https://codetheweb.blog/learn/javascript/)*