# 我希望知道的前 26 个 JavaScript 面试问题

> 原文：<https://dev.to/fullstackcafe/top-26-javascript-interview-questions-i-wish-i-knew-26k1>

[![Top 26 JavaScript Interview Questions I Wish I Knew](img/de30a41d115ce50bc4c74b39809d76c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lB2L_uWQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.pexels.com/photos/697662/pexels-photo-697662.jpeg%3Fauto%3Dcompress%26cs%3Dtinysrgb%26dpr%3D2%26h%3D650%26w%3D940) 
根据 Stack Overflow 的 2018 年度调查，JavaScript 成为最常用的编程语言，连续六年。让我们面对现实吧，JavaScript 是你所有开发人员技能的基石，在任何开发人员的面试中都是不可避免的。坚持读完[全栈。Cafe](https://www.fullstack.cafe) 汇编了最常见和最棘手的 JavaScript 面试问题和答案，帮助你找到下一份理想的工作。

> 🔴最初发表于 [FullStack。咖啡馆-杀死你的技术&编码面试](https://www.fullstack.cafe/?utm_source=dev&utm_medium=blog)

### Q1:JavaScript 中的强制是什么？

> 题目: **JavaScript**
> 难度:

在 JavaScript 中不同的两种内置类型之间的转换称为`coercion`。JavaScript 中的强制有两种形式:*显式*和*隐式*。

这里有一个显式强制的例子:

```
var a = "42";

var b = Number( a );

a;              // "42"
b;              // 42 -- the number! 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个隐含强制的例子:

```
var a = "42";

var b = a * 1;  // "42" implicitly coerced to 42 here

a;              // "42"
b;              // 42 -- the number! 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:** [FullStack。咖啡馆](https://www.fullstack.cafe)

### Q2:JavaScript 中的作用域是什么？

> 题目: **JavaScript**
> 难度:⭐

在 JavaScript 中，每个函数都有自己的*范围*。作用域基本上是变量以及如何通过名称访问这些变量的规则的集合。只有该函数内部的代码才能访问该函数的作用域变量。

变量名在同一范围内必须是唯一的。一个范围可以嵌套在另一个范围内。如果一个作用域嵌套在另一个作用域中，则最内层作用域中的代码可以访问任一作用域中的变量。

🔗**来源:** [FullStack。咖啡馆](https://www.fullstack.cafe)

### Q3:解释 JavaScript 中的等式

> 题目: **JavaScript**
> 难度:⭐

JavaScript 有严格的和类型转换的比较:

*   **严格比较(例如===)** 检查值是否相等，不允许*强制*
*   **抽象比较(例如==)** 在允许*强制*的情况下检查值是否相等

```
var a = "42";
var b = 42;

a == b;         // true
a === b;        // false 
```

Enter fullscreen mode Exit fullscreen mode

一些简单的等式规则:

*   如果比较中的任何一个值(aka 侧)可能是`true`或`false`值，则避免使用`==`而使用`===`。
*   如果比较中的任何一个值可以是这些特定值(`0`、`""`或`[]` -空数组)，则避免使用`==`并使用`===`。
*   在所有其他情况下，使用`==`是安全的。它不仅安全，而且在许多情况下，它以提高可读性的方式简化了您的代码。

🔗**来源:** [FullStack。咖啡馆](https://www.fullstack.cafe)

### Q4:解释什么是回调函数，并提供一个简单的例子。

> 题目: **JavaScript**
> 难度:⭐⭐

`callback`函数是作为参数传递给另一个函数的函数，在某个操作完成后执行。下面是一个简单的回调函数的例子，它在完成一些操作后记录到控制台*。* 

```
function modifyArray(arr, callback) {
  // do something to arr here
  arr.push(100);
  // then execute the callback function that was passed
  callback();
}

var arr = [1, 2, 3, 4, 5];

modifyArray(arr, function() {
  console.log("array has been modified", arr);
}); 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:**【coderbyte.com】T2

### Q5:“用严”是做什么的？

> 题目: **JavaScript**
> 难度:⭐⭐

`use strict`文字输入在 JavaScript 程序的顶部或函数的顶部，如果错误地创建了全局变量，它会抛出一个错误，从而帮助您编写更安全的 JavaScript 代码。例如，下面的程序会抛出一个错误:

```
function doSomething(val) {
  "use strict"; 
  x = val + 10;
}` 
```

Enter fullscreen mode Exit fullscreen mode

它会抛出一个错误，因为`x`没有被定义，它被设置为全局范围内的某个值，这是`use strict`不允许的。下面的小改动修复了抛出的错误:

```
function doSomething(val) {
  "use strict"; 
  var x = val + 10;
} 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:**【coderbyte.com】T2

### Q6:解释 JavaScript 中的空和未定义

> 题目: **JavaScript**
> 难度:⭐⭐

JavaScript(通过扩展 TypeScript)有两种底层类型:`null`和`undefined`。它们是*打算*用来表示不同的意思:

*   有些东西还没有初始化:`undefined`。
*   某些东西目前不可用:`null`。

🔗**来源:** [FullStack。咖啡馆](https://www.fullstack.cafe)

### Q7:写一个允许你这样做的函数。

> 题目: **JavaScript**
> 难度:⭐⭐

```
var addSix = createBase(6);
addSix(10); // returns 16
addSix(21); // returns 27 
```

Enter fullscreen mode Exit fullscreen mode

* * *

您可以创建一个闭包来保存传递给函数`createBase`的值，即使在内部函数返回之后。正在返回的内部函数是在外部函数中创建的，使它成为一个闭包，它可以访问外部函数中的变量，在本例中是变量`baseNumber`。

```
function createBase(baseNumber) {
  return function(N) {
    // we are referencing baseNumber here even though it was declared
    // outside of this function. Closures allow us to do this in JavaScript
    return baseNumber + N;
  }
}

var addSix = createBase(6);
addSix(10);
addSix(21); 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:**【coderbyte.com】T2

### Q8:解释 JavaScript 中的值和类型

> 题目: **JavaScript**
> 难度:⭐⭐

JavaScript 有类型化的值，没有类型化的变量。以下是可用的内置类型:

*   `string`
*   `number`
*   `boolean`
*   `null`和`undefined`
*   `object`
*   `symbol`(ES6 的新功能)

🔗**来源:** [FullStack。咖啡馆](https://www.fullstack.cafe)

### Q9:解释事件冒泡以及如何防止它

> 题目: **JavaScript**
> 难度:⭐⭐

**事件冒泡**是一个事件在尽可能深的元素处触发，并按嵌套顺序在父元素上触发的概念。结果，当点击一个子元素时，可以显示父元素的处理程序被激活。

防止事件冒泡的一种方法是在 IE < 9 上使用`event.stopPropagation()`或`event.cancelBubble`。

🔗**来源:**【https://github.com/kennymkchan】T2

### Q10:JavaScript 中的 let 关键字是什么？

> 题目: **JavaScript**
> 难度:⭐⭐

除了在函数级为变量创建声明之外，ES6 还允许您将变量声明为属于单独的块(成对的{..})，使用`let`关键字。

🔗**来源:**【github.com/getify】T2

### Q11:如何检查一个数是否是整数？

> 题目: **JavaScript**
> 难度:⭐⭐

检验一个数是小数还是整数的一个非常简单的方法是看你除以 1 时是否还有余数。

```
function isInt(num) {
  return num % 1 === 0;
}

console.log(isInt(4)); // true
console.log(isInt(12.2)); // false
console.log(isInt(0.3)); // false 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:**【coderbyte.com】T2

### Q12:什么是 IIFEs(立即调用函数表达式)？

> 题目: **JavaScript**
> 难度:⭐⭐⭐

这是一个立即调用的函数表达式，简称 IIFE。它在创建后立即执行:

```
(function IIFE(){
    console.log( "Hello!" );
})();
// "Hello!" 
```

Enter fullscreen mode Exit fullscreen mode

当试图避免污染全局名称空间时，经常使用这种模式，因为在 life 中使用的所有变量(像在任何其他普通函数中一样)在其作用域之外是不可见的。

🔗**来源:**【stackoverflow.com】T2

### Q13:如何在 JavaScript 中比较两个对象？

> 题目: **JavaScript**
> 难度:⭐⭐⭐

两个非原始值，像引用保存的对象(包括函数和数组)，所以`==`和`===`的比较只是检查引用是否匹配，而不是关于底层值的任何事情。

例如，默认情况下，`arrays`被强制转换为字符串，只需用逗号(`,`)将所有值连接起来。所以两个内容相同的数组不会`==`相等:

```
var a = [1,2,3];
var b = [1,2,3];
var c = "1,2,3";

a == c;     // true
b == c;     // true
a == b;     // false 
```

Enter fullscreen mode Exit fullscreen mode

对于*深度对象比较*，使用类似`deep-equal`的外部库或者实现你自己的递归等式算法。

🔗**来源:** [FullStack。咖啡馆](https://www.fullstack.cafe)

### Q14:你能解释一下 ES5 和 ES6 的区别吗

> 题目: **JavaScript**
> 难度:⭐⭐⭐

*   ECMAScript 5(ES5):ECMAScript 第五版，于 2009 年标准化。这个标准已经在所有现代浏览器中相当完整地实现了

*   **ECMAScript 6(ES6)/ECMAScript 2015(es 2015)**:ECMAScript 第 6 版，2015 年标准化。这个标准已经在大多数现代浏览器中部分实现。

以下是 ES5 和 ES6 的一些主要区别:

*   **箭头功能** & **字符串插值**:考虑:

```
const greetings = (name) => {
      return `hello ${name}`;
} 
```

Enter fullscreen mode Exit fullscreen mode

甚至:

```
const greetings = name => `hello ${name}`; 
```

Enter fullscreen mode Exit fullscreen mode

*   **常数**。Const 在许多方面都像其他语言中的常量一样工作，但是有一些注意事项。Const 代表对一个值的“常量引用”。所以用 const，你实际上可以改变被变量引用的对象的属性。你不能改变引用本身。

```
const NAMES = [];
NAMES.push("Jim");
console.log(NAMES.length === 1); // true
NAMES = ["Steve", "John"]; // error 
```

Enter fullscreen mode Exit fullscreen mode

*   **块范围的变量**。新的 ES6 关键字`let`允许开发人员在块级别确定变量的范围。`Let`的提升方式与`var`不同。
*   **默认参数值**默认参数允许我们用默认值初始化函数。当参数被省略或未定义时，将使用默认值，这意味着 null 是有效值。

```
// Basic syntax
function multiply (a, b = 2) {
     return a * b;
}
multiply(5); // 10 
```

Enter fullscreen mode Exit fullscreen mode

*   **类定义和继承**

    ES6 引入了对类(`class`关键字)、构造函数(`constructor`关键字)、继承的`extend`关键字的语言支持。

*   **for-of 运算符**

    The for...of 语句创建一个遍历可迭代对象的循环。

*   **扩散算子**
    用于对象合并

```
const obj1 = { a: 1, b: 2 }
const obj2 = { a: 2, c: 3, d: 4}
const obj3 = {...obj1, ...obj2} 
```

Enter fullscreen mode Exit fullscreen mode

*   **承诺**承诺提供了一种机制来处理异步操作的结果和错误。您可以用回调完成同样的事情，但是 promises 通过方法链接和简洁的错误处理提供了更好的可读性。

```
const isGreater = (a, b) => {
  return new Promise ((resolve, reject) => {
    if(a > b) {
      resolve(true)
    } else {
      reject(false)
    }
    })
}
isGreater(1, 2)
  .then(result => {
    console.log('greater')
  })
 .catch(result => {
    console.log('smaller')
 }) 
```

Enter fullscreen mode Exit fullscreen mode

*   **模块导出&导入**考虑模块导出:

```
const myModule = { x: 1, y: () => { console.log('This is ES5') }}
export default myModule; 
```

Enter fullscreen mode Exit fullscreen mode

和导入:

```
import myModule from './myModule'; 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:** [Bulby.io](https://dev.toundefined)

### Q15:解释 JavaScript 中“未定义”和“未定义”的区别

> 题目: **JavaScript**
> 难度:⭐⭐⭐

在 JavaScript 中，如果你试图使用一个不存在且未声明的变量，那么 JavaScript 将抛出一个错误`var name is not defined`，然后脚本将停止执行。但是如果你使用`typeof undeclared_variable`，那么它将返回`undefined`。

在开始进一步讨论之前，让我们理解声明和定义之间的区别。

是一个声明，因为你还没有定义它包含什么值，但是你声明了它的存在和内存分配的需要。

```
var x; // declaring x
console.log(x); //output: undefined 
```

Enter fullscreen mode Exit fullscreen mode

`var x = 1`既是声明又是定义(我们也可以说我们正在进行初始化)，这里变量 x 的声明和赋值是内联发生的，在 JavaScript 中，每个变量声明和函数声明都在它声明的当前作用域的顶部，然后赋值按顺序发生，这个术语称为`hoisting`。

一个已声明但未定义的变量，当我们试图访问它时，它将产生`undefined`。

```
var x; // Declaration
if(typeof x === 'undefined') // Will return true 
```

Enter fullscreen mode Exit fullscreen mode

> 一个既没有声明也没有定义的变量，当我们试图引用这样的变量时，结果是`not defined`。

```
console.log(y);  // Output: ReferenceError: y is not defined 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:**【stackoverflow.com】T2

### Q16:匿名函数和命名函数的区别？

> 题目: **JavaScript**
> 难度:⭐⭐⭐

考虑:

```
var foo = function() { // anonymous function assigned to variable foo
    // ..
};

var x = function bar(){ // named function (bar) assigned to variable x 
    // ..
};

foo(); // actual function execution
x(); 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:** [FullStack。咖啡馆](https://www.fullstack.cafe)

### Q17:JavaScript 中的“闭包”是什么？举个例子？

> 题目: **JavaScript**
> 难度:⭐⭐⭐⭐

*闭包*是定义在另一个函数(称为父函数)内部的函数，可以访问在父函数作用域中声明和定义的变量。

闭包可以访问三个范围内变量:

*   变量在自己的作用域中声明
*   父函数作用域中声明的变量
*   在全局名称空间中声明的变量

```
var globalVar = "abc";

// Parent self invoking function
(function outerFunction (outerArg) { // begin of scope outerFunction
  // Variable declared in outerFunction function scope
  var outerFuncVar = 'x';    
  // Closure self-invoking function
  (function innerFunction (innerArg) { // begin of scope innerFunction
    // variable declared in innerFunction function scope
    var innerFuncVar = "y";
    console.log(         
      "outerArg = " + outerArg + "\n" +
      "outerFuncVar = " + outerFuncVar + "\n" +
      "innerArg = " + innerArg + "\n" +
      "innerFuncVar = " + innerFuncVar + "\n" +
      "globalVar = " + globalVar);
  // end of scope innerFunction
  })(5); // Pass 5 as parameter
// end of scope outerFunction
})(7); // Pass 7 as parameter 
```

Enter fullscreen mode Exit fullscreen mode

`innerFunction`是定义在`outerFunction`内部的闭包，可以访问所有在 outerFunction 作用域中声明和定义的变量。除了这个函数之外，在函数内部定义为闭包的函数可以访问在`global namespace`中声明的变量。

上述代码的输出将是:

```
outerArg = 7
outerFuncVar = x
innerArg = 5
innerFuncVar = y
globalVar = abc 
```

Enter fullscreen mode Exit fullscreen mode

🔗**资料来源:**[github . com/ganqwerty](https://github.com/ganqqwerty/123-Essential-JavaScript-Interview-Question/blob/master/README.md)

### Q18:你如何在 JavaScript 中创建私有变量？

> 题目: **JavaScript**
> 难度:⭐⭐⭐⭐

要在 JavaScript 中创建一个不可更改的私有变量，您需要将其创建为函数中的局部变量。即使执行了函数，也不能在函数外部访问变量。例如:

```
function func() {
  var priv = "secret code";
}

console.log(priv); // throws error 
```

Enter fullscreen mode Exit fullscreen mode

要访问该变量，需要创建一个返回私有变量的帮助函数。

```
function func() {
  var priv = "secret code";
  return function() {
    return priv;
  }
}

var getPriv = func();
console.log(getPriv()); // => secret code 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:**【coderbyte.com】T2

### Q19:解释原型设计模式

> 题目: **JavaScript**
> 难度:⭐⭐⭐⭐

原型模式创建新的对象，但是它不是创建未初始化的对象，而是返回用它*从原型对象或样本对象复制*的值初始化的对象。原型模式也称为属性模式。

原型模式有用的一个例子是用与数据库中的默认值相匹配的值来初始化业务对象。prototype 对象保存着复制到新创建的业务对象中的默认值。

经典语言很少使用原型模式，但是作为原型语言的 JavaScript 在构造新对象及其原型时使用这种模式。

🔗**来源:**【dofactory.com】T2

### Q20:检查给定的字符串是否同构

> 题目: **JavaScript**
> 难度:⭐⭐⭐⭐

两个字符串要*同构*，字符串 A 中一个字符的所有出现都可以用另一个字符替换得到字符串 b，字符的顺序必须保持。字符串 A 的每个字符到字符串 b 的每个字符必须有一对一的映射。

*   `paper`和`title`将返回 true。
*   `egg`和`sad`将返回 false。
*   `dgg`和`add`将返回 true。

```
isIsomorphic("egg", 'add'); // true
isIsomorphic("paper", 'title'); // true
isIsomorphic("kick", 'side'); // false

function isIsomorphic(firstString, secondString) {

  // Check if the same lenght. If not, they cannot be isomorphic
  if (firstString.length !== secondString.length) return false

  var letterMap = {};

  for (var i = 0; i < firstString.length; i++) {
    var letterA = firstString[i],
        letterB = secondString[i];

    // If the letter does not exist, create a map and map it to the value
    // of the second letter
    if (letterMap[letterA] === undefined) {
      letterMap[letterA] = letterB;
    } else if (letterMap[letterA] !== letterB) {
      // Eles if letterA already exists in the map, but it does not map to
      // letterB, that means that A is mapping to more than one letter.
      return false;
    }
  }
  // If after iterating through and conditions are satisfied, return true.
  // They are isomorphic
  return true;
} 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:**【https://github.com/kennymkchan】T2

### Q21:术语“运输”代表什么？

> 题目: **JavaScript**
> 难度:⭐⭐⭐⭐

没有办法填充已经添加到语言中的新语法。因此，更好的选择是使用一个工具，将您的新代码转换成旧代码的等价物。这个过程俗称**trans pilling**，是*转换+编译*的一个术语。

通常，你将 *transpiler* 插入到你的构建过程中，类似于你的代码 linter 或 minifier。
有很多很棒的 transpilers 供你选择:

*   巴别塔:将文件 ES6+转换成 ES5
*   **Traceur** :将文件 ES6、ES7 和 beyond 传输到 ES5

🔗**来源:** [你不知道的 JS，涨&涨](https://github.com/getify/You-Dont-Know-JS/blob/master/up%20%26%20going/ch2.md)

### Q22:“这个”关键字是怎么用的？提供一些代码示例。

> 题目: **JavaScript**
> 难度:⭐⭐⭐⭐

在 JavaScript *中，这个*总是指我们正在执行的函数的“所有者”,或者更确切地说，是指函数是其方法的对象。

考虑:

```
function foo() {
    console.log( this.bar );
}

var bar = "global";

var obj1 = {
    bar: "obj1",
    foo: foo
};

var obj2 = {
    bar: "obj2"
};

foo();          // "global"
obj1.foo();     // "obj1"
foo.call( obj2 );  // "obj2"
new foo();       // undefined 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:**【quirksmode.org】T2

### Q23:你如何将你自己的方法添加到数组对象中，以便下面的代码能够工作？

> 题目: **JavaScript**
> 难度:⭐⭐⭐⭐

```
var arr = [1, 2, 3, 4, 5];
var avg = arr.average();
console.log(avg); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

JavaScript 不是基于类的，但它是一种基于原型的语言。这意味着每个对象都链接到另一个对象，即它的原型，并且它继承它的方法。您可以跟踪每个对象的原型链，直到到达没有原型的`null`对象。我们需要向全局`Array`对象添加一个方法，我们将通过修改`Array prototype`来实现。

```
Array.prototype.average = function() {
  // calculate sum
  var sum = this.reduce(function(prev, cur) { return prev + cur; });
  // return sum divided by number of elements
  return sum / this.length;
}

var arr = [1, 2, 3, 4, 5];
var avg = arr.average();
console.log(avg); // => 3 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:**【coderbyte.com】T2

### Q24:JavaScript 中的提升是什么？

> 题目: **JavaScript**
> 难度:⭐⭐⭐⭐

*提升*是 JavaScript 解释器将所有变量和函数声明移动到当前作用域顶部的动作。*吊装*有两种类型:

*   可变提升-罕见
*   功能提升-更常见

一个`var`(或函数声明)出现在一个作用域中的任何地方，该声明被认为属于整个作用域，并且可以在任何地方访问。

```
var a = 2;
foo();                 // works because `foo()`
                         // declaration is "hoisted"

function foo() {
    a = 3;
    console.log( a );   // 3
    var a;             // declaration is "hoisted"
                         // to the top of `foo()`
}

console.log( a );   // 2 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:** [FullStack。咖啡馆](https://www.fullstack.cafe)

### Q25:下面的代码会输出什么？

> 题目: **JavaScript**
> 难度:⭐⭐⭐⭐

```
0.1 + 0.2 === 0.3 
```

Enter fullscreen mode Exit fullscreen mode

* * *

这将令人惊讶地输出`false`,因为在内部表示某些数字时存在浮点错误。`0.1 + 0.2`没有很好地输出给`0.3`，而是结果实际上是`0.30000000000000004`，因为计算机不能在内部表示正确的数字。解决这个问题的一个办法是在用十进制数做算术时对结果进行舍入。

🔗**来源:**【coderbyte.com】T2

### Q26:描述揭示模块模式设计模式

> 题目: **JavaScript**
> 难度:⭐⭐⭐⭐⭐

**模块图案**的变体被称为**显露模块图案**。这样做的目的是维护封装，并显示在对象文本中返回的某些变量和方法。直接实现是这样的:

```
var Exposer = (function() {
  var privateVariable = 10;

  var privateMethod = function() {
    console.log('Inside a private method!');
    privateVariable++;
  }

  var methodToExpose = function() {
    console.log('This is a method I want to expose!');
  }

  var otherMethodIWantToExpose = function() {
    privateMethod();
  }

  return {
      first: methodToExpose,
      second: otherMethodIWantToExpose
  };
})();

Exposer.first();        // Output: This is a method I want to expose!
Exposer.second();       // Output: Inside a private method!
Exposer.methodToExpose; // undefined 
```

Enter fullscreen mode Exit fullscreen mode

它的一个明显缺点是无法引用私有方法

🔗**来源:** [scotch.io](https://scotch.io/bar-talk/4-javascript-design-patterns-you-should-know)

> 谢谢🙌阅读，祝你面试好运！
> *如果你喜欢这篇文章，请分享给你的开发者伙伴！*
> *查看更多全栈面试问题&答案上👉[www . full stack . cafe](https://www.fullstack.cafe)T9】*