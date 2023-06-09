# JavaScript 中变量、函数和“this”的范围和提升

> 原文：<https://dev.to/ddhogan/scope-and-hoisting-of-variables-functions-and-this-in-javascript-5176>

> 关于这个主题的博客帖子和文章比比皆是(参考列表如下),所以我将试图强调学习 JavaScript 的初级-中级 web 开发学生的常见陷阱。

尽管 JavaScript 是一种解释型语言，但在 web 开发环境中的实现涉及到执行前的一个步骤，称为词法作用域(tokenizing)，在这个步骤中，解释器浏览您的代码，识别您声明的所有变量，记录它们何时被重新分配，并将代码块划分到三个级别的**作用域**中:块、函数和全局。

```
// Example 1 (credit MDN)

function exampleFunction() { 
 var x = "declared inside function"; // x can only be used in exampleFunction 
 console.log("Inside function"); 
 console.log(x);
}

console.log(x); // Causes Reference Error 
```

该函数的作用域包括变量`x`，因此该变量仅在该函数中是已知的。试图在全局范围内访问它会抛出一个错误，因为`x`不是一个声明的变量(它甚至不是`undefined`)。

如果我们将那个`var`声明移到函数之外，它将在全局范围内，每个人都知道它，我们可以在函数内外访问它:

```
// Example 2 (credit MDN)

var x = "declared outside function";

exampleFunction();

function exampleFunction() { 
 console.log("Inside function");
 console.log(x);
}

console.log("Outside function");
console.log(x); 
```

随着 ECMAScript2015(又名“ES6”)的出现，引入了两种声明变量的新方法:`let`和`const`，这两种方法非常重要，因为它们能够对变量的可用范围进行更细粒度的控制。`let`和`const`都定义了局部变量，这些变量只在定义它们的层次上可用(无论是代码块还是函数，以及任何包含的子块)。

### 吊装

在下面的例子中，`x`是用`var`声明的，这个名为`x`的变量在整个函数中都是已知的(甚至在它之前的行中也是如此！)和子块中。如果用较新的`let`或`const`声明`x`，那么外部作用域不能访问它，如果我们在子块中再次使用`let x;`，它实际上是一个不同的变量(就像人类双胞胎出生时分开，但给了相同的名字，不是同一个人)。

```
// Example 3 (credit MDN) 
function varTest() { 
 var x = 1; 
 if (true) { 
   var x = 2; // same variable! 
   console.log(x); // 2 
 }
 console.log(x); // 2 
}

function letTest() {
 let x = 1; 
 if (true) {
   let x = 2; // different variable
   console.log(x); // 2
 }
 console.log(x); // 1
} 
```

这是相关的，因为词法分析步骤紧接在执行代码之前发生，并且事实上:

```
var x = 5; 
```

一行列出了两个步骤:变量`x`的*声明*，以及整数 5 对该变量的*赋值*。我们也可以这样写:

```
var x;  // declaration
x = 5;  // assignment 
```

当变量用`var`声明时，其值为`undefined`，但已知是变量。然后下一行使`x`的值等于 5。但是，`let`和`const`却不是这样。欢迎来到[时间死区](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let#Temporal_Dead_Zone)。

```
// Example 4 (credit MDN)
function doSomething() {
 console.log(bar); // undefined
 console.log(foo); // ReferenceError
 var bar = 1; 
 let foo = 2;
} 
```

在这个函数中，`bar`的声明是 ***将*** 提升到作用域的顶部，这里是函数`doSomething()`中包含的代码。因此，实际上，它是这样执行的:

```
// Example 5 
function doSomething() {
 var bar;
 console.log(bar); // undefined
 console.log(foo); // ReferenceError
 bar = 1; 
 let foo = 2;
} 
```

这就是为什么尝试`console.log(bar)`会产生`undefined`(我们知道它是一个变量，只是没有值)，而`console.log(foo)`会抛出一个引用错误(“一个叫做‘foo’的变量？你到底在说什么，人类？”)

这使得这样的事情成为可能:

```
// Example 6 (credit MDN)
num = 6;
console.log(num); // returns 6
var num; 
```

还有:

```
// Example 7 (credit MDN) 
dogName("Watson");

function dogName(name) {
 console.log("My dog's name is " + name);
}
// "My dog's name is Watson" 
```

在第一个例子中，尽管从计算机的角度来看,`var num`似乎是在我们给它赋值后声明的，但它注意到我们已经在相关的作用域(全局)中声明了它，将其固定在顶部，然后继续执行其余的代码。在第二个例子中，尽管我们在定义函数之前就调用了它，但是这个定义被提升到了作用域的顶部，所以当我们真正开始执行代码时，解释器已经知道了`dogName()`是什么。

对于`var`变量，注意只有声明被提升，而不是赋值。所以，在例 6 中，这样写:

```
console.log(num); // returns undefined 
var num = 6; 
```

返回`undefined`。由于这个原因，通常建议总是在变量所在的作用域的顶部声明变量，这样你就记住了解释器执行代码的顺序。或者，使用`let`和`const`可以防止这种行为，因为这样声明的变量没有用值`undefined`初始化。所以即使它们被提升，你仍然会得到一个引用错误，因为它们在被赋值之前不会被初始化。就好像它们根本没有被吊起来一样。`const`有一个额外的优点，可以防止意外的重新分配(尽管以这种方式声明的对象可能仍然会修改其属性)，比如:

```
// Example 8 (credit Digital Ocean)
// Create a CAR object with two properties
const CAR = {
 color: "blue", 
 price: 15000 
}

// Modify a property of CAR
CAR.price = 20000;

console.log(CAR);

// Output
// { color: 'blue', price: 20000 } 
```

类似地，函数遵循相似的规则。函数*声明*被挂起:

```
// Example 9 (credit Elizabeth Mabishi at Scotch.io)

hoisted(); // Output: "This function has been hoisted."

function hoisted() {
 console.log('This function has been hoisted.');
}; 
```

…而函数*表达式*不是:

```
// Example 10 (credit Elizabeth Mabishi at Scotch.io)

expression(); //Output: "TypeError: expression is not a function

var expression = function() {
  console.log('Will this work?');
}; 
```

### `this`如何融入其中

我将讨论的一个相关主题是`this`，因为它与范围和提升相关。戈登·朱写了一份不错的[小抄](https://github.com/gordonmzhu/cheatsheet-js)，总结了他*改变人生*课程中关于[手表&代码](https://watchandcode.com/)的相应教训。基本上`this`是依赖于范围的(上下文)。如果它在一个常规函数中被调用或者只是在外面被调用，`this`指向窗口。如果你在一个作为方法被调用的函数中，`this`指向被操作的对象(点左边的任何东西)。

```
// Example 11 (credit Gordon Zhu)
var myObject = {
  myMethod: function() { 
    console.log(this); 
  } 
};

myObject.myMethod(); // --\> myObject 
```

这种情况的一个扩展是在构造函数中使用，如例 12，其中`this`指的是类 Person:
的实例

```
// Example 12 (credit MDN) 
function Person(name, age) {
  this.name = name; 
  this.age = age;
} 
```

使用 call、bind、apply 显式设置`this`的值超出了范围(哈哈！)的这篇博文。

根据前面的例子，在回调函数中，它取决于在哪里(什么范围)`this`被调用。

### 参考文献

*   [Javascript 的词法作用域、提升和闭包并不神秘― @nickbalestra](https://medium.com/@nickbalestra/javascripts-lexical-scope-hoisting-and-closures-without-mystery-c2324681d4be)
*   [理解 JavaScript 中的变量、范围和提升――数字海洋](https://www.digitalocean.com/community/tutorials/understanding-variables-scope-hoisting-in-javascript#difference-between-var,-let,-and-const)
*   [理解 JavaScript 中的提升――Elizabeth mabi Shi at scotch . io](https://scotch.io/tutorials/understanding-hoisting-in-javascript)
*   [吊装— MDN 网络文档词汇表:网络相关术语定义](https://developer.mozilla.org/en-US/docs/Glossary/Hoisting)
*   [范围— MDN 网络文档词汇表:网络相关术语的定义](https://developer.mozilla.org/en-US/docs/Glossary/Scope)
*   [语法和类型— JavaScript MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Grammar_and_types#Declarations)

*原载于 2018 年 8 月 18 日*[*【donnacodes.com】*](http://donnacodes.com/scope_and_hoisting_of_variables_functions_and_this_in_javascript)