# JavaScript 令人恐惧的终结

> 原文：<https://dev.to/ogwurujohnson/the-fear-inspiring-closure-of-javascript--379>

[Getify](https://github.com/getify/You-Dont-Know-JS/blob/master/up%20%26%20going/ch2.md) 在他的系列文章**You-don-Know _ Js**中，解释了闭包是一种“记住”并继续访问函数范围(它的变量)的方式，即使函数已经结束运行。

通常，当你创建一个函数时；要么传递一些参数，要么声明一些内部变量。看下面的例子；

```
function multiplyBy(passed)
{
 var inner = 2;
 return passed * inner;
}
console.log(multiplyBy(3)); 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常简单的函数，它将传递给它的任何东西乘以`2`并返回解。在 Javascript 中，你可以在函数中不传递参数。现在你可能想知道我们的初始程序是如何工作的，你可以这样做；

```
var passed = 3;
function multiplyBy()
{
var inner = 2;
return passed * inner;
}
console.log(multiplyBy()); 
```

Enter fullscreen mode Exit fullscreen mode

在 Javascript 中，函数外部定义的变量在函数内部自动可用，因为 Javascript 使用了一种叫做**的词法作用域**；用我们的例子来说，这意味着函数`multiplyBy`无法访问`var inner`，但它是如何做到的，简单的答案是*闭包*，在这篇文章的结尾，我们将证明上面的是一个闭包。

现在让我们看一个更流行的例子；

```
function addTo(passed)
{
function add(inner)
    {
        return passed + inner;
}
return add;
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们有一个函数，它的作用域内有另一个函数。在 Javascript 中，这些被称为嵌套函数。内部函数返回第一个函数的参数和它自己的参数之和；而外部函数返回内部函数。

现在当我们调用外层函数:`addTo(3);`，会发生什么；`3`作为第一个函数的参数传递，在第二个函数中我们会返回；`3 + inner;`。我们的程序还没有解决，现在这就是闭包发挥作用的地方。还记得 [Getify 的](https://github.com/getify/You-Dont-Know-JS/blob/master/up%20%26%20going/ch2.md)对关闭的最初解释吗？在我们的函数下添加这段代码；

```
var addThree = addTo(3);
addThree(4);
```

Enter fullscreen mode Exit fullscreen mode

现在，当你运行我们的程序时，内部变量返回的是`3+4`，这就是 [Getify](https://github.com/getify/You-Dont-Know-JS/blob/master/up%20%26%20going/ch2.md) 的闭包定义发挥作用的地方，javascript 保留或记忆它需要完全执行程序的变量，即使外部函数必须已经完成运行。

**期末备注:**

1.  理解闭包的关键是理解函数内部的函数是如何工作的。
2.  当返回的内部函数不是*自包含的*时，闭包会介入；即当某个变量或参数依赖于*外部函数*来完成其执行时。

*N/B:* A *自含*内部函数不依赖于外部函数，这里是一个自含内部函数的例子；

```
function youSayBye()
{
alert("Good bye");
function iSayHello()
    {
       alert("hello");
}
return iSayHello;
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，内部函数和内部函数所依赖的变量之间的组合给了我们一个闭包。

`closure = function + outer context`

*   其中外部上下文是函数所依赖的变量。