# 匿名函数 JavaScript 编码概念-匿名函数

> 原文：<https://dev.to/chris_bertrand/coding-concepts---anonymous-methods-a9o>

# 什么是匿名方法，什么时候我们应该使用它们？

## 定义

> **匿名函数——在[计算机编程](https://en.wikipedia.org/wiki/Computer_programming "Computer programming")中，匿名函数(函数文字、lambda 抽象或 lambda 表达式)是一个[函数](https://en.wikipedia.org/wiki/Function_(computer_science) "Function (computer science)")定义，它没有[绑定](https://en.wikipedia.org/wiki/Name_binding "Name binding")到[标识符](https://en.wikipedia.org/wiki/Name_(computer_science) "Name (computer science)")。匿名函数通常是<sup id="cite_ref-1">[【1】](https://en.wikipedia.org/wiki/Anonymous_function#cite_note-1)</sup>传递给[高阶函数](https://en.wikipedia.org/wiki/Higher-order_function "Higher-order function")的参数，或者用于构造需要返回函数的高阶函数的结果。如果函数只使用一次，或者使用次数有限，那么从语法上来说，匿名函数可能比使用命名函数更简单。匿名函数在[函数式编程语言](https://en.wikipedia.org/wiki/Functional_programming_language "Functional programming language")和其他具有[一级函数](https://en.wikipedia.org/wiki/First-class_function "First-class function")的语言中无处不在，它们为[函数类型](https://en.wikipedia.org/wiki/Function_type "Function type")履行与[文字](https://en.wikipedia.org/wiki/Literal_(computer_programming) "Literal (computer programming)")为其他[数据类型](https://en.wikipedia.org/wiki/Data_type "Data type")履行的相同角色。**

与编码概念系列中涉及的所有主题一样，这些编程思想已经存在了一段时间。自从 1958 年 Lisp 首次出现以来，匿名函数就出现在编程语言中了！我的例子将再次用 JavaScript 演示。JavaScript 的兴起，以及这种语言相对简单的本质，使得举例子时很容易编写代码。使用 C#多年后，lambda 函数(箭头函数符号= >)是我与委托和匿名方法的第一次互动，也是我对它们的了解开始的地方。

> 一个**匿名函数**是一个**函数**，它不被存储，但是与一个变量相关联。**匿名函数**可以接受输入并返回输出，就像标准的**函数**一样。

### 正规函数定义

```
// run the function 
sayHello(); // See that this can be before the function declaration

function sayHello() (
  alert("Hello");
} 
```

Enter fullscreen mode Exit fullscreen mode

### ![Anonymous emblem.svg](img/5b357304a17632409c14cf42d6552eeb.png)

### 匿名函数定义

```
 var sayHello = function {
alert('Hello');
}
sayHello(); // This needs to be after declaring the anon func 
```

Enter fullscreen mode Exit fullscreen mode

那么这两者有什么不同呢？首先要理解的是，普通函数是在任何其他代码之前运行的，这意味着在使用它们之前不必声明它们。匿名函数在运行时创建。

如果你回头看第一个例子， **sayHello()** 写在声明之前，但是在它之后的第二个里！如果第二个例子是以前写的，它就不会工作，而且看代码它是有意义的，**但是为什么允许这样做呢？**函数声明使用函数名在当前作用域内创建变量。这些函数不能用于创建匿名函数，因为它们要求函数有名称。

> 匿名函数是使用[函数操作符而不是函数声明](http://helephant.com/2012/07/14/javascript-function-declaration-vs-expression/)来声明的。

所以本质上匿名函数没有名字！

这看起来很奇怪，但是很有效，因为声明为你创建了变量。不必为匿名函数设置名称很方便，因为有时函数的名称并不重要。

你可能已经多次使用这条线，你有没有意识到这是一个匿名的方法？

```
$(document).ready(function () {
alert("Hello");
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 我什么时候使用它们？

一次性使用方法是何时使用这一原则的完美例子！当您打算直接使用该方法时，或者在一个 **if 语句**或一个 **for** **循环中使用该方法时，它也非常有用。**

```
for(var x=0; x<=3; x++) {
var helloFunction= function() {
alert("Hello Mr " + x + ");
}
helloFunction();
} 
```

Enter fullscreen mode Exit fullscreen mode

函数操作符(**匿名**)语法比函数声明(**标准**)更简洁。对于单行事件处理程序和快速处理样式化 DOM 元素来说，这是非常理想的。如果您将方法直接绑定到变量，将更容易找到实现，并将阻止函数名可能冲突的全局范围问题。我们不要谈论与 JQuery 的冲突和所有可能引起的混乱。 **jQuery.noConflict()** 在大多数组织中使用得太多了！

```
var wordHouse = {
 hello: function() { alert("Hello"); }
}
wordHouse.hello(); 
```

Enter fullscreen mode Exit fullscreen mode

函数运算符也是一个表达式，可以像上面一样做很酷的事情！你可以扩展单词库来表达其他单词。你甚至可以创建函数作为数组中的项，然后遍历它们！

```
// create an array on anonymous methods
var helloToday = [
 function() { alert("Hello Monday")},
 function() { alert("Hello Tuesday")},
];

// loop over the array
for(var x=0; x< helloToday.length; x++) {
 helloToday[x]();
} 
```

Enter fullscreen mode Exit fullscreen mode

有更多的匿名方法/函数，根据您选择的语言，它们的行为会稍有不同。所以如果你想了解更多关于这个话题的内容，可以看看附加阅读材料。

* * *

我错过了什么有用的东西吗？你还有什么要补充的吗？你用一种有趣的方式使用匿名方法吗？如果有分享下面！

感谢阅读。

克莉丝

# 附加阅读

[WikiBooks - JavaScript 匿名函数](https://en.wikibooks.org/wiki/JavaScript/Anonymous_functions)

[堆栈溢出-匿名函数 vs Clousures】](https://stackoverflow.com/questions/12930272/javascript-closures-vs-anonymous-functions)

[函数声明 vs 函数运算符](http://helephant.com/2012/07/14/javascript-function-declaration-vs-expression/)

[自执行匿名函数](http://esbueno.noahstokes.com/post/77292606977/self-executing-anonymous-functions-or-how-to-write)