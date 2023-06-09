# JavaScript，内在的野兽

> 原文：<https://dev.to/akshay5995/javascript-the-beast-within-5667>

自从我看到使用 JavaScript 可以做的事情和它的易用性，以及我们可以实现这些事情，我就被它逗乐了。我使用 React JS 和 Node JS 已经快一年半了。不用说，我是 React 的超级粉丝。

最近，我开始理解在作为开发人员呈现给我们的所有抽象层背后到底发生了什么。这一发现让我们回到了基础，并详细了解了 JavaScript 的工作原理😲

每当我回顾我在对 JS 的内部工作一无所知的情况下完成的所有工作时，我都觉得很有趣。请将此视为对 JavaScript 社区的赞美。你们太棒了🙌

> 注意:在本文中，我希望介绍 JS 和 4 个主题/特性，这些主题/特性引起了我对 JavaScript 的兴趣，并使我成为了 [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript) 和其他 JS 相关文章的热心读者。这对刚开始使用 JS 的人来说很有用，或者对有经验的人来说是一种复习。

我希望你也受到启发，去理解这个神秘的 JavaScript 世界🌎(或者，只是学点新东西😉).

下面是我个人[最喜欢的](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript)对 JS 的一点介绍。

JavaScript 是臭名昭著的世界上最容易被误解的编程语言。

它经常被嘲笑为一个玩具，但在其欺骗性的简单性之下，强大的语言功能正等着我们。(异步双关，呃？😜)

JavaScript 现在被数量惊人的高端应用程序所使用，这表明对这项技术的深入了解对于任何 web 或移动开发者来说都是一项重要的技能。

与大多数编程语言不同，JavaScript 语言没有输入或输出的概念。它被设计为在宿主环境中作为脚本语言运行，并且由宿主环境提供与外界通信的机制。

主机环境通常是一个*浏览器*。

JavaScript 解释器也可以在其他地方找到，包括 Adobe Photoshop、SVG images、Node.js 等服务器端环境、开源 Apache CouchDB 等 NoSQL 数据库、嵌入式计算机、GNOME(GNU/Linux 操作系统最流行的 GUI 之一)等完整的桌面环境。

JavaScript 是一种多范式的动态语言，具有类型和操作符、标准的内置对象和方法。它的语法基于 Java 和 C 语言——这些语言的许多结构也适用于 JavaScript。

> 如果你想了解更多，我还会给你一些文章。这些可能是 ES6 或其前身的特性。

* * *

## 名字怎么回事？

*或者说，ECMAScript 是什么？*

JavaScript 是由 Brendan Eich(网景工程师)于 1995 年创建的。它最初被命名为 Mocha，后来被改为 LiveScript，但在一项试图利用 Java 语言在当时的流行程度的营销决策后，它被重新命名。

Netscape 向 ECMA(一个欧洲标准组织)提交了 JavaScript，这导致了当年 ECMAScript 标准的第一版。

该标准在 1999 年获得了 ECMAScript 第 3 版的重大更新，从那以后一直保持稳定。

由于对语言复杂性的政治分歧，第四版被放弃了。第四版的许多部分构成了 2009 年 12 月发布的 ECMAScript 第 5 版和 2015 年 6 月发布的该标准第 6 主版本的基础。

总之，我认为 JavaScript 是实现 ECMAScript 标准的语言。

> JavaScript 是标准的实现，也就是 ECMAScript。

* * *

## 原型继承和类

*对我个人来说最吸引人的功能之一*

你问什么是原型继承？

JavaScript 只有一个构造:*对象*。每个对象都有一个私有属性，它包含一个到另一个对象的链接，这个对象叫做它的*原型*。

该原型对象有自己的原型，依此类推，直到到达一个以 null 作为其原型的对象。根据定义，null 没有原型，并且充当这个原型链中的最后一环。JavaScript 中几乎所有的对象都是位于原型链顶端的对象的实例。JS 中的每个对象都可以有另一个对象作为它的原型。那么前一个对象继承后一个对象的所有属性。

一个对象通过内部属性`[[Prototype]]`指定它的原型。由`[[Prototype]]`属性连接的对象链被称为原型链

`__proto__`属性公开了内部原型链接。

[![prototypal inheritance example](img/5fc2901dfb7c9adbd11e8f5c1e04fa17.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sVTA1RsH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2Asacpzj7gNySk-x8O.png)

*原型* vs `__proto__`

*原型*是一个构造函数的属性，它设置将成为被构造对象的`__proto__`属性的内容。

```
var animal = {
  eats: true,
  walk() {
    console.log("walking!!")
  },
}

var rabbit = {
  jumps: true,
  __proto__ : animal,
}

var bugsBunny = {
  likesEating: "Cartoon Carrots",
  isCartoon: true,
  __proto__ : rabbit,
}

bugsBunny.walk() // walking!!!
// resolved by going up the prototype chain to 'animal'

console.log(bugsBunny.jumps) // true
// resolved by going up the prototype chain to 'rabbit' 
```

现在，给我讲讲课程？

好了，现在 JavaScript 中的类只是其原型继承之上的语法糖。(让原型继承对所有 OOPs 的人来说更经典😅)

ES6 引入了一组新的关键字来实现类。即类、构造函数、静态、扩展和超

我让你从这里开始了解更多关于课程的信息。

_ 来源:[类](https://medium.com/r/?url=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FJavaScript%2FReference%2FClasses)

* * *

## “这”是什么？

*剧透:只是法术绑定*

与其他语言相比，JavaScript 的表现稍有不同。它在 Javascript 中的严格模式和非严格模式之间也有一些区别。

`this`的值由函数的调用方式决定。在执行过程中不能通过赋值来设置，可能每次调用函数都不一样。

### 全局上下文

在非严格模式下默认指向浏览器中的全局窗口对象，在严格模式下默认*这个*是*未定义的*。

```
In browser
console.log(this === window); //true

In Node
console.log(this === global); //true

'use strict'
console.log(this); //undefined 
```

### 功能上下文

进入执行上下文时，`this`的值保持不变。

要将`this`的值从一个上下文传递到另一个上下文，请使用 call 或 apply:

```
var obj1 = { a: "Custom" }

var a = "Global"

function whatIsA() {
  return this.a
}

console.log(whatIsA()) //Global

console.log(whatIsA().call(obj1)) //Custom 
```

### 绑定()

ES5 为 Function.prototype 引入了一个名为 bind()的新函数。

调用 func.bind(someObject)会创建一个与 func 具有相同主体和作用域的新函数，但在原始函数中会出现这种情况，在新函数中它会永久绑定到 bind 的第一个参数

```
// function WhatIsA from the above example
var obj2 = { a: 'I'm Object 2's a!' };

var whatIsAInObj2 = whatIsA.bind(obj2);

console.log(whatIsAInObj2()); // I'm Object 2's a! 
```

### 箭头功能

随着箭头函数的引入，`this`保留了封闭词法上下文的值`this`。

```
var obj = {
  bar: function() {
    var x = () => this
    return x
  },
}

var fn = obj.bar()

console.log(fn() === obj) // true 
```

我可以继续下去。但是，要了解更多信息，请看一下 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)。😉

*注意:阅读关于这个绑定如何只受最近成员引用的影响。*

* * *

## 吊装⬆️

*涨涨涨？(酷玩粉丝会懂的🎶)*

只有 ES6 的吊装符合图片或规格。

提升被认为是一种思考执行上下文(特别是创建和执行阶段)在 JavaScript 中如何工作的一般方式。

提升的定义可能会建议将变量和函数声明移到代码的顶部。

这是不正确的，变量和函数声明在编译阶段被放入内存，但是仍然在你在代码中输入它们的地方。

```
console.log(sayHello("world!")) // Hello world!

console.log(a)

// undefined and not 'ReferenceError' due to hoisting
console.log(sayOla("world!"))

// TypeError: sayOla is not a function
function sayHello(param) {
  return "Hello " + param
}

var a = 1
var sayOla = function(param) {
  return "Hello " + param
} 
```

上面的例子显示了起重的作用。从概念上讲，您的运行时是如何看待它的。

```
function sayHello(param) {
  return "Hello " + param
}

var a
var sayOla

console.log(sayHello("world!")) // Hello world!
console.log(a)
// undefined and not 'ReferenceError' due to hoisting
console.log(sayOla("world!"))
// TypeError: sayOla is not a function
a = 1
sayOla = function(param) {
  return "Hello " + param
} 
```

*注:只悬挂声明，不悬挂赋值。此外，类声明不会被挂起。*

* * *

## 现在，让我们来个“收尾”🔚

*又一个 JS 双关(抱歉！)*

一个*闭包*是与对其周围状态的引用捆绑在一起(封闭)的函数(*词法环境*)。

闭包让您可以从内部函数访问外部函数的作用域。在 JavaScript 中，闭包是在每次创建函数时创建的。

```
function outerFunction() {
  var x = "Hello "
  return function(param) {
    return x + param
  }
}

var newFunc = outerFunction()

console.log(newFunc("World!")) // Hello World!
// Even after returning the function has access to variable x 
```

闭包通常用于给对象提供数据隐私，并固定函数部分应用的顺序。

```
// partial application

function makeAdder(x) {
  return function(y) {
    return x + y
  }
}

var add5 = makeAdder(5)

console.log(add5(10)) // 15 
```

* * *

我希望这篇文章能激发您的兴趣，让您了解更多关于 JavaScript 的知识。

PS:我会给你留下我个人最喜欢的 JS 文章/文档💌

1.  [JavaScript 的再介绍](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript)
2.  [事件循环](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop)
3.  [咖喱或部分应用](https://medium.com/javascript-scene/curry-or-partial-application-8150044c78b8)
4.  [关于 JavaScript 函数的一切](https://codeburst.io/all-about-javascript-functions-in-1-article-49bfd94b31ab)
5.  [JS 中的函数式编程](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-functional-programming-7f218c68b3a0)
6.  [类和原型继承](https://medium.com/javascript-scene/master-the-javascript-interview-what-s-the-difference-between-class-prototypal-inheritance-e4cd0a7562e9)

你也可以关注这个非常有趣的 [YouTube 频道](https://www.youtube.com/channel/UCO1cgjhGzsSYb1rsB4bFe4Q),了解与 JS 相关的很酷的东西等等。