# 什么功能？

> 原文：<https://dev.to/kymiddleton/what-the-function-22o8>

JavaScript 可以在任何地方使用，它是一种非常好学的编程语言。它可以把一个网页从一个枯燥的文档变成一个互动的东西。JavaScript 有几个基础需要学习，比如语法、变量、数据类型、对象、条件、数组、循环和函数。还是把重点放在函数上吧！

函数到底是什么？非技术背景的人可能会理解这个术语的最佳含义是一组相关的动作。在计算世界中，*函数*是一段命名的代码，通常被称为块或包。代码束或代码块是一组计算值或执行任务的语句。绑定允许重用它，这样可以节省大量时间，避免多次重复代码行，并降低出错的风险。要使用一个函数，必须对它进行定义，并给它起一个容易理解的名字，这样以后就可以“调用”它来执行操作。函数的基本结构看起来有点像这样:

```
function  nameOfFunction(listOfVariableNames) {
    statements of the function should be written here
} 
```

Enter fullscreen mode Exit fullscreen mode

学习 JavaScript 的人可能会遇到术语*函数声明*。根据韦氏词典的观点，声明是一种陈述的行为。一个*函数声明*是一组共同执行任务的语句。基本上，它定义了一个已命名的函数，并允许该函数在被定义之前就被使用。

*函数表达式*是一个匿名函数，没有名字，在定义之前不能使用。当函数是匿名的，它可以被赋给一个变量。它可能看起来像这样:

```
nameOfFunction = function (listOfVariableNames)  {
    function should be written here
}; 
```

Enter fullscreen mode Exit fullscreen mode

另一类函数是*箭头函数*表达式。这使得函数可以表示为箭头函数，从而缩短了语法。这里有一个例子*在*之前应用了一个箭头表达式:

```
let func = function(param1, param2, …paramN) {
    return expression;
} 
```

Enter fullscreen mode Exit fullscreen mode

箭头功能更加简洁:

```
let func = (param1, param2, …paramN) => expression 
```

Enter fullscreen mode Exit fullscreen mode

*func* 有参数(param1，param2，…paramN)。该函数计算表达式并返回结果。

函数有实参，也有形参。参数是函数定义中列出的名称(变量)。参数允许将值传递给函数，并在调用时改变函数的行为。参数可以是字符串(用引号括起来的字符列表)或数字，但也可以是对象。函数是*函数对象*，在 JavaScript 中非常通用。有五种原始数据类型(字符串、数字、未定义、布尔和空)，任何不属于这五种类型的数据都被认为是对象。

一旦一个函数被完全定义，也就是说它已经被命名，并且已经添加了描述该函数应该做什么的细节，代码就可以运行了。要运行代码，或者执行一个函数，需要通过输入函数名，后跟一组括号来调用*或*，括号可以是空的，也可以包含函数期望的参数。函数体在函数被调用时执行，JavaScript 被要求打印的任何文本也会显示出来。**

```
nameOfFunction();
‘Returned Message Outlined Inside the Function’ 
```

Enter fullscreen mode Exit fullscreen mode

最后但同样重要的是，函数的一部分是*返回值*。使用上面的例子，如果在浏览器中调用该函数，它可能会返回第三行。

```
nameOfFunction();
‘Returned Message Outlined Inside the Function’
undefined 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript 中的每个函数，除非另外指定，否则将返回 undefined，这是返回值。如果函数被要求打印一条消息而不是返回一个值，它将作为未定义返回。

对于第一次尝试学习 JavaScript 函数的人来说，这可能会让人不知所措，在完全理解之前需要进行一些处理。请记住，函数允许代码块被重用，并根据传递的参数执行不同的操作。可以给函数起一个有意义的名字，清楚地表明一个函数有特定的功能。并且函数总是返回一个值，即使该值是未定义的。*