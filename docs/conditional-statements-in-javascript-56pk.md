# JavaScript 中的条件语句

> 原文：<https://dev.to/christiangraves/conditional-statements-in-javascript-56pk>

从我上周关于比较和逻辑操作符的博客文章继续，我想在此基础上本周讨论条件语句。条件语句可以控制代码的执行流程。在这样做的时候，它们充当一些代码块的看门人，这些代码块是您希望在满足某些条件时执行的。条件语句使用比较和逻辑运算符来计算条件，并根据计算结果返回 true 或 false 值。我在下面列出了三个条件语句。

```
//if statement
if(//conditional){
    /**executed code if conditional is true**/
}
//continue with code

//if with else statement 
if(//conditional){
    /**executed code if conditional is true**/
}else{
    /**executed code if conditional is false**/
}

//if with else if statement
if(//conditional){
    /**executed code if conditional is true**/
}else if(//conditional){
    /**executed code if first conditional is false and else if conditional is true*//  } 
```

要想知道条件句 ***if*** 的逻辑是如何工作的，最简单的方法就是把它想象成你可能和你的朋友做的一个陈述。也许他们会问你是否想在 12 点去吃午饭。你告诉他们，如果你能在上午 10 点前完成一些工作，你就能按时完成。被评估的条件是你是否能在上午 10 点前完成工作。如果你能够完成你的工作，条件将是真的，你将能够和你的朋友去吃午饭。如果你不能完成你的工作，条件将是假的，你将不能和你的朋友去吃午饭，必须继续工作。我在下面包含了一个显示逻辑的 ***if*** 条件语句的例子。

```
let x = 10
let message = ""

if(x<15){
    message = "x is smaller"
} 
```

在上面的例子中，您可以看到条件为真，因此消息变量被设置为字符串“x 较小”。如果条件为假，浏览器将跳过条件语句的其余部分，并继续执行下一个代码块。条件为假现在把我们带到第二个条件语句 ***if else*** 。

在第二段的朋友例子中，你可以给他们第二个选择，而不是告诉他们如果你不完成工作，你就不能去吃午饭了。你可以说如果你完成了工作，你会和他们一起吃午饭，否则你会和他们一起吃晚饭。 ***if else*** 语句以同样的方式工作，我将扩展上面的 JS 示例来说明这一点。

```
let x = 10
let message = ""

if(x<5){
    message = "x is smaller"
}else{
    message = "x is larger"
} 
```

在本例中，第一个条件失败，因此 message 被设置为第二个字符串。之后，浏览器将继续执行下一个代码块。现在也许你想检查另一个条件，如果第一个条件失败或为假，就做些别的事情。

以朋友为例，也许你想先问问他们是否有空一起吃晚饭，然后告诉他们如果你不能去吃午饭，你会去吃晚饭。你说如果你完成了你的工作，你会和他们共进午餐，或者如果他们有空，你会和他们共进晚餐。使用我们的 JS 示例的一个例子是下面使用的 ***else if*** 语句。

```
let x = 10
let message = ""

if(x<9){
    message = "x is not 10"
}else if(x<11){
    message = "x is 10"
} 
```

***else if*** 语句的一个很酷的特性是，您可以将多个 ***else if*** 语句链接在一起，以检查不同执行的多个条件语句。唯一的问题是，这可能会损害代码的可读性。如果你想连锁多个 ***否则如果*** 语句在一起我会建议去看看 ***switch*** 语句。它有相同的功能，但保持你的代码更干净，易于阅读。