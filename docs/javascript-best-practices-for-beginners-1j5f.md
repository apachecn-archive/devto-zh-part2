# JavaScript:初学者的最佳实践

> 原文：<https://dev.to/kymiddleton/javascript-best-practices-for-beginners-1j5f>

JavaScript 是一种编程语言，可以在任何地方使用，将网页从枯燥的文档转换成有趣的交互式内容。说到学习，确定建立健康习惯的最佳实践是很重要的。因此，让我们回顾一下 JavaScript 的一些最佳实践。

**变量命名**
给计算机写代码时，代码创造了一个故事。当故事以一种易于遵循的格式编写时，它就更容易理解，并且很好的第一步是通过使用其他编码者能够理解的描述来保持变量名的清晰和简洁。基本上*变量*是存储数据值的容器。假设鞋盒是变量，因为它是存放东西的地方。变量需要有唯一的名字，称为*标识符*。如果鞋盒是变量，鞋盒的标签就是变量名。这是指鞋盒时使用的名称。盒子里的鞋子等于变量的内容。如果变量名简洁明了，比如足球鞋，就很容易识别盒子里面是什么。

避免全局变量
你知道全局变量会被其他脚本覆盖吗？你能想象编码一项功能的所有努力被另一个开发人员编写的附加功能覆盖了吗？所有变量都是全局的，这意味着它们可以被访问，除非采取额外的措施，比如用闭包使变量私有。闭包和模块模式的使用包含了防止它们被覆盖的变量。

在这个例子中，局部变量是“a ”,它只能在函数内部使用，也就是定义它的地方，使它对其他函数和代码隐藏起来。

```
function myFunction() {
    var a=4;
    return a * a;
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，这里有一个“a”是全局变量的例子，因为它是在函数外部声明的。

```
varr a = 4;
    function myFunction () {
    return a * a;
} 
```

Enter fullscreen mode Exit fullscreen mode

对象字面量
对象字面量表示法是一个包含键值对的数组。冒号分隔键和值，除了最后一项之外，每个键值对后面跟一个逗号，就像一个常规数组一样。这是一种包含所有内容的方法，但是仍然可以通过对象名来访问它。

下面是一个使用点符号的代码示例。

```
var myObject = new Object();
    myObject.myProperty = value;
    myObject.yourProperty = value;
    myObject.myMethod = function(){
    //code here
}
    myObject.yourMethod = function(){
    //more code
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个使用对象文字的代码示例，它会产生更干净、更简洁的代码。

```
var myObject ={
    myProperty : value,
    yourProperty : value,
    myMethod : function(){
    //code here
},
    yourMethod : function(){
    //more code
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

**模块模式**
模块模式用于模仿其他编程语言中的类概念。在 JavaScript 中，它允许私有和公共的方法和变量存储在一个对象中。这为需要向外界公开的方法创建了一个面向公众的 API，同时也创建了一个代码封装来保持变量的私有性。这是 JavaScript 中最常见的设计模式，使用对象可以防止代码重复膨胀。就像章节书一样，好的模块是自成一体的。

**揭示模块模式**
这类似于模块模式，但是它确保所有的变量和方法都是私有的，直到它们被直接公开。它创建了一个易于定义的代码管理系统，并且对指向模块的方法有清晰的可见性。

**严格的编码风格**
你知道写得不好的 JavaScript 可能包含严重后果的安全漏洞吗？黑客喜欢使用 JavaScript 开发工具，使 JavaScript 成为容易攻击的目标，漏洞可能来自企业和客户端。花时间学习正确的代码从长远来看是值得的，记住有效的代码是安全的代码，安全的代码是有效的代码！

有人问过你这样一个问题吗:你如何吃掉一头大象？作为一个初学者学习 JavaScript 似乎也同样令人望而生畏。但是最好的方法，就像吃大象的答案一样，是一次吃一口。读一点，写一点，测试一点，然后再研究一些，重复这个过程，在你知道之前，你会成为 JavaScript 所有关键最佳实践的大师！