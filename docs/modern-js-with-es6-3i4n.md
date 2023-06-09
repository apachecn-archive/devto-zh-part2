# 带 ES6+的现代 JS

> 原文：<https://dev.to/kevinlangleyjr/modern-js-with-es6-3i4n>

这是我在 2018 年[word camp Miami](https://miami.wordcamp.org/2018/)上讲授的 [*深度学习 JavaScript*](https://twitter.com/wordcampmiami/status/961616862214672384)课程的 [Modern JS with ES6](https://www.youtube.com/watch?v=2CQO-o5lXSE) 的附带博文。

根据 StackOverflow 2018 年开发者调查，在 2018 年的今天，JavaScript 是网络上最受欢迎的语言。

但是我们今天所知道和喜爱的 JavaScript，已经不是 2000 年甚至 2010 年初的那个 JavaScript 了。

让我们开始吧！

## ES_？？？

ES 是 ECMAScript 的缩写。每当您看到 ES 后面跟一个数字时，它就引用该版本的 ECMAScript。

直到最近，在 ES6 或 ES2015 中，JavaScript 才在语法和增加的功能上有了最大的飞跃。这些添加旨在使大规模 JavaScript 开发变得更加容易。

*   ES1:1997 年 6 月
*   ES2:1998 年 6 月
*   es3:1999 年 12 月
*   ES4:废弃
*   es5:2009 年 12 月
*   es 6/es 2015:2015 年 6 月
*   es 7/es 2016:2016 年 6 月
*   it ' s 8/es 2017:2017 年 6 月
*   是 2018 年 9 月:2018 年 6 月
*   是 2019 年 10 月:2019 年 6 月
*   es 11/es 2020:2020 年 6 月
*   是的。下一个:这个术语是动态的，指的是 ECMAScript 的下一个版本。

## TC39

[TC39](https://tc39.es/) 是负责推进 ECMAScript 规范并为 JavaScript 语言标准化这些规范的小组。

对规范的所有更改都是在一个过程中开发的，该过程将一个功能从概念阶段发展到完全成熟和指定的功能。

*   阶段 0 -斯特劳珀森
*   第一阶段-提案
*   第二阶段-草稿
*   第三阶段-候选人
*   第 4 阶段-完成

您可以在这里看到 TC39 的活动提案列表。

## 模块

模块允许您异步加载代码，并为您的代码提供一个抽象层。虽然 JavaScript 很久以前就有模块了，但它们以前是通过库实现的，并没有内置到语言本身中。ES6 是 JavaScript 语言首次拥有内置模块的时候。

从模块中导出有两种方法。

*   指定出口
*   默认导出

### 命名出口

使用命名导出，您只需用`export`预先考虑您想要导出的内容。

```
// mathlib.js

export function square(x) {
  return x * x;
}

export function add(x, y) {
  return x + y;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后可以使用与您导出的对象相同的名称导入它们。

```
// main.js

import { square, add } from './mathlib';

console.log(square(9)); // 81

console.log(add(4, 3)); // 7 
```

Enter fullscreen mode Exit fullscreen mode

### 默认导出

使用默认导出，您只需用`export default`预先考虑您想要导出的内容。

```
// foo.js
export default () => {
  console.log('Foo!');
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// main.js
import foo from './foo';

foo(); // Foo! 
```

Enter fullscreen mode Exit fullscreen mode

### 默认和命名出口

这两种方式甚至可以混合使用！

```
// foobar.js

export default () => {
  console.log('Foo!');
}

export const bar() => {
  console.log( 'Bar!' )
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// main.js

import foo, { bar } from './foobar';

foo(); // Foo!

bar(); // Bar! 
```

Enter fullscreen mode Exit fullscreen mode

## 变量作用域

### `var` vs `let` vs `const`

`var`是函数作用域还是全局作用域，取决于上下文。

如果在一个函数中定义了一个`var`,那么它的作用域将会是这个封闭函数以及其中声明的任何函数。如果在任何函数之外声明，那么它将是全局范围的。

```
if ( true ) {
  var foo = 'bar';
}

console.log( foo ); // bar

function hello() {
  var x = 'hello';
  function world() {
    var y = 'world';
    console.log(x); // hello (function `hello()` encloses `x`)
    console.log(y); // world (`y` is in scope)
  }
  world();
  console.log(x); // hello (`x` is in scope)
  console.log(y); // ReferenceError `y` is scoped to `world()`
}

hello(); 
```

Enter fullscreen mode Exit fullscreen mode

`let`和`const`是块范围的。

```
if ( true ) {
  let foo = 'bar';
  const bar = 'foo';
}

console.log( foo ); // ReferenceError.
console.log( bar ); // ReferenceError. 
```

Enter fullscreen mode Exit fullscreen mode

您可以用花括号`{}`创建新的块范围，如下面的代码示例所示。

`let`和`const`

```
let first = 'First string';

{ // Each layer of curly brackets gives us a new block scope.
  let second = 'Second string';
    {
      let third = 'Third string';
    }
    // Accessing third here would throw a ReferenceError.
}
// Accessing second here would throw a ReferenceError. 
```

Enter fullscreen mode Exit fullscreen mode

`const`变量只能赋值一次。但它不是一成不变的。

```
const foo = { bar: 1 };

foo = 'bar'; // 'foo' is read only. 
```

Enter fullscreen mode Exit fullscreen mode

但是，您可以更改属性！

```
const foo = { bar: 1 };

foo.bar = 2;
console.log(foo); // { bar: 2 } 
```

Enter fullscreen mode Exit fullscreen mode

`Object.freeze()`防止改变属性。冻结对象会返回最初传入的对象，并对该对象产生多种影响。

它将防止新的属性被添加到它，现有的属性被删除，改变属性值，甚至防止原型被改变。

使用`Object.freeze()`
使对象的属性不可变

```
const foo = { bar: 1 };

Object.freeze(foo);
foo.bar = 3; // Will silently fail or in strict mode, will return a TypeError
console.log(foo.bar); // 2 
```

Enter fullscreen mode Exit fullscreen mode

另一个选择是**密封**一个对象，以防止改变对象的结构。

[![Seal](img/16f5c76916f959115eb78377138dea0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xGaUchQk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/kevinlangleyjr-dev/image/upload/v1620847917/blog/modern-js/7093270813_fdafd6e704_o_f1brtj.jpg) 
[图片来源:伊娃·里纳尔迪](https://www.flickr.com/photos/evarinaldiphotography/7093270813)

等等，不，不是那只海豹！

[![Seal](img/b908a9cbc35c123004cf90a7bfe20836.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KaQxnerF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/kevinlangleyjr-dev/image/upload/v1620844147/blog/modern-js/amy-asher-giZJHm2m9yY-unsplash_ts6ic7.jpg) 
[图片来源:艾米·阿什](https://unsplash.com/photos/giZJHm2m9yY)

是的，不。也不是那一个...

```
const foo = { bar: 1 };

Object.seal(foo);
foo.baz = false; // Will silently fail or in strict mode, will return a TypeError
foo.bar = 2;
console.log( foo ); // { bar: 2 } 
```

Enter fullscreen mode Exit fullscreen mode

## 吊装

JavaScript 中的提升是一个过程，在代码执行之前，变量和函数声明被移动到它们的作用域的顶部。

这意味着你可以用函数和变量来做这件事:

```
sayHello();

function sayHello() {
  console.log('Hello!');
} 
```

Enter fullscreen mode Exit fullscreen mode

```
console.log( foobar ); // undefined (note: not ReferenceError!)
var foobar = 'Woot!' 
```

Enter fullscreen mode Exit fullscreen mode

在 ES6 中，类、`let`和`const`变量被提升，但是它们还没有初始化，不像`var`变量和函数。

```
new Thing(); // TypeError
class Thing{};

console.log(foo); // 'foo' was used before it was defined
let foo = true;

console.log(bar); // 'bar' was used before it was defined
const bar = true; 
```

Enter fullscreen mode Exit fullscreen mode

## 太阳穴死区

[![TARDIS](img/926c4b1153d0961206a106099a5284ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eG6wCDZi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/kevinlangleyjr-dev/image/upload/v1631465914/blog/modern-js/25031662202_51c0be73c9_o_klr3xa.jpg) 
[图片来源:贾尔斯·特恩布尔](https://www.flickr.com/photos/gilest/25031662202/)

虽然时间死区听起来像是《神秘博士》中的情节，但它远没有听起来那么可怕。

从程序块开始到初始化处理完毕，`const`或`let`变量处于时间死区。

```
if ( true ) {

  // TDZ starts!
  const doSomething = function () {
    console.log( thing ); // OK!
  };

  doSomething(); // ReferenceError
  let thing = 'test'; // TDZ ends.
  doSomething(); // Called outside TDZ!
} 
```

Enter fullscreen mode Exit fullscreen mode

在初始化之前引用块中的变量会产生一个`ReferenceError`，与用`var`声明的变量相反，它只有`undefined`值和类型。

## 但是，我该用什么呢？！？`var`？`let`？`const`？

`const`和`let`的唯一区别是`const`使契约不会发生重新绑定。

> 默认使用`const`。仅在需要重新绑定时使用`let`。`var`不应用于 ES2015。
> 
> 马蒂亚斯·拜恩斯- V8 工程师@谷歌
> 
> 将`var`用于顶级变量，将`let`用于较小范围内的局部变量。只有在编写了一些代码，并且你有理由确信不应该有变量重分配之后，才可以将`let`重构为`const`。
> 
> 凯尔·辛普森——Getify Solutions 的创始人

我个人遵循默认使用`const`的第一种方法，从不使用`var`，只有当我知道我需要重新分配变量时才使用`let`。

## 迭代式&循环

当使用`var`进行迭代或循环时，您会将一个全局变量泄漏到父作用域，并且该变量会在每次迭代中被覆盖。

```
for ( var i = 0; i < 10; i++ ) {
  setTimeout( function() {
    console.log( 'Number: ' + i );
  }, 1000 );
}

// Number: 10
// Number: 10
// Number: 10
// Number: 10
// Number: 10
// Number: 10
// Number: 10
// Number: 10
// Number: 10
// Number: 10 
```

Enter fullscreen mode Exit fullscreen mode

在一个`for`循环中使用`let`允许我们将变量的范围限定在它的块中。

```
for ( let i = 0; i < 10; i++ ) {
  setTimeout( function() {
    console.log( 'Number: ' + i );
  }, 1000 );
}

// Number: 0
// Number: 1
// Number: 2
// Number: 3
// Number: 4
// Number: 5
// Number: 6
// Number: 7
// Number: 8
// Number: 9 
```

Enter fullscreen mode Exit fullscreen mode

### ES6 也给了我们一种新的方法来循环遍历 iterables！

使用一个`for...of`语句以非常简洁的语法循环遍历一个 iterable。

```
const iterable = [10, 20, 30];

for (const value of iterable) {
  console.log(value);
}

// 10
// 20
// 30 
```

Enter fullscreen mode Exit fullscreen mode

您甚至可以迭代`NodeLists`而不必使用任何其他的诡计！🤯

```
const articleParagraphs = document.querySelectorAll('article > p');

for (const paragraph of articleParagraphs) {
  paragraph.classList.add('read');
} 
```

Enter fullscreen mode Exit fullscreen mode

[![Mind Blown Gif](img/e39b8cfb72003cf0d3223f235ad9b0f4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--96LMbxTC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/kevinlangleyjr-dev/image/upload/v1631466460/blog/modern-js/blow-mind-mind-blown_oz6qpc.gif)

甚至，用它来迭代字符串中的字母！

```
const foo = 'bar';
for (const letter of foo) {
  console.log(letter);
}

// b
// a
// r 
```

Enter fullscreen mode Exit fullscreen mode

## 箭头功能

箭头函数是传统函数表达式的一种更简洁的替代形式。

```
// Traditional function expression.
const addNumbers = function (num1, num2) {
  return num1 + num2;
}

// Arrow function expression.
const addNumbers = (num1, num2) => {
  return num1 + num2;
} 
```

Enter fullscreen mode Exit fullscreen mode

箭头函数可以有隐式返回:

```
// Arrow function with implicit return and without any arguments.
const sayHello = () => console.log( 'Hello!' );

// Arrow function with implicit return and a single argument.
const sayHello = name => console.log( `Hello ${name}!` );

// Arrow function with implicit return and multiple arguments.
const sayHello = (firstName, lastName) => console.log( `Hello ${firstName}  ${lastName}!` ); 
```

Enter fullscreen mode Exit fullscreen mode

### 那`this`呢？

随着 ES6 的引入，`this`的值是从它的周围环境或函数的封闭词法上下文中提取的。因此，你不再需要`bind()`、`that`或`self`！

```
function Person(){
  this.age = 0;

  setInterval(function() {
    this.age++; // `this`refers to the Window 😒
  }, 1000);
} 
```

Enter fullscreen mode Exit fullscreen mode

```
function Person(){
  var that = this;
  this.age = 0;

  setInterval(function() {
    that.age++; // Without arrow functions. Works, but is not ideal.
  }, 1000);
} 
```

Enter fullscreen mode Exit fullscreen mode

```
function Person(){
  this.age = 0;

  setInterval(() => {
    this.age++; // `this` properly refers to the person object. 🎉🎉🎉
  }, 1000);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 什么时候不应该使用箭头函数？

查看我的文章，关于在 JavaScript 中什么时候不使用箭头函数

## 默认参数

这是一个基本函数。

```
function calculateTotal( subtotal, tax, shipping ) {
return subtotal + shipping + (subtotal * tax);
}

const total = calculateTotal(100, 0.07, 10); 
```

Enter fullscreen mode Exit fullscreen mode

让我们为函数表达式中的参数添加一些默认值！

老办法😕

```
function calculateTotal( subtotal, tax, shipping ) {
  if ( tax === undefined ) {
    tax = 0.07;
  }

  if ( shipping === undefined ) {
    shipping = 10;
  }

  return subtotal + shipping + (subtotal * tax);
}

const total = calculateTotal(100); 
```

Enter fullscreen mode Exit fullscreen mode

稍微好一点

```
function calculateTotal( subtotal, tax, shipping ) {
  tax = tax || 0.07;
  shipping = shipping || 10;

  return subtotal + shipping + (subtotal * tax);
}

const total = calculateTotal(100); 
```

Enter fullscreen mode Exit fullscreen mode

现在有了 ES6+！🎉🎉🎉

```
function calculateTotal( subtotal, tax = 0.07, shipping = 10 ) {
  return subtotal + shipping + (subtotal * tax);
}

const total = calculateTotal(100); 
```

Enter fullscreen mode Exit fullscreen mode

### 如果我想只传第一个和第三个参数呢？

```
function calculateTotal( subtotal, tax = 0.07, shipping = 10 ) {
  return subtotal + shipping + (subtotal * tax);
}

const total = calculateTotal(100, , 20); // SyntaxError! Cannot pass empty spaces for argument.

const total = calculateTotal(100, undefined, 20); // 🎉🎉🎉🎉 
```

Enter fullscreen mode Exit fullscreen mode

## 解构

析构允许您从存储在对象或数组中的任何数据中提取多个值。

```
const person ={
  first: 'Kevin',
  last: 'Langley',
  location: {
    city: 'Beverly Hills',
    state: 'Florida'
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

使用上面的对象，让我们从对象的属性中创建一些变量。

```
const first = person.first;
const last = person.last; 
```

Enter fullscreen mode Exit fullscreen mode

在 ES6 之前，我们只能初始化一个变量，并将其分配给想要提取的对象属性。

```
const { first, last, location } = person;

const { city, state } = location; 
```

Enter fullscreen mode Exit fullscreen mode

但是在 ES6+中，我们能够从提取的数据中析构变量并创建新的变量。

您还可以将提取的数据命名为不同的变量名！

```
const { first: fName, last: lName } = person;

const { city: locationCity, state: locationState } = person.location; 
```

Enter fullscreen mode Exit fullscreen mode

它甚至可以处理嵌套属性！

```
const { first, last, location: { city } } = person;

console.log( city ); // Beverly Hills 
```

Enter fullscreen mode Exit fullscreen mode

### 如果我试图破坏一个不存在的财产会怎么样？

该变量的返回值将是`undefined`。

```
const settings = { color: 'white', height: 500 };
const { width, height, color } = settings;

console.log(width);// undefined
console.log(height); // 500
console.log(color);// white 
```

Enter fullscreen mode Exit fullscreen mode

你甚至可以在你的析构函数中设置默认值！

```
const settings = { color: 'white', height: 500 };
const { width = 200, height = 200, color = 'black' } = settings;

console.log(width);// 200
console.log(height); // 500
console.log(color);// white 
```

Enter fullscreen mode Exit fullscreen mode

你也可以析构数组！

```
const details = ['Kevin', 'Langley', 'kevinlangleyjr.com'];
const [first, last, website] = details;

console.log(first);// Kevin
console.log(last);// Langley
console.log(website); // kevinlangleyjr.com 
```

Enter fullscreen mode Exit fullscreen mode

## 传播开来...和...休息

在 ES6 之前，我们会运行`.apply()`来传入一组参数。

```
function doSomething (x, y, z) {
  console.log(x, y, z);
}

let args = [0, 1, 2];

// Call the function, passing args.
doSomething.apply(null, args); 
```

Enter fullscreen mode Exit fullscreen mode

### ...传播算子

但是在 ES6 中，我们可以使用扩展操作符`...`来传递参数。

```
function doSomething (x, y, z) {
  console.log(x, y, z);
}

let args = [0, 1, 2];

// Call the function, without `apply`, passing args with the spread operator!
doSomething(...args); 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以使用 spread 运算符来组合数组。

```
let array1 = ['one', 'two', 'three'];
let array2 = ['four', 'five'];

array1.push(...array2) // Adds array2 items to end of array.
array1.unshift(...array2) //Adds array2 items to beginning of array. 
```

Enter fullscreen mode Exit fullscreen mode

你可以在数组的任意点组合它们！

```
let array1 = ['two', 'three'];
let array2 = ['one', ...array1, 'four', 'five'];

console.log(array2); // ["one", "two", "three", "four", "five"] 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以使用 spread 操作符来创建数组的副本。

```
let array1 = [1,2,3];
let array2 = [...array1]; // like array1.slice()

array2.push(4);

console.log(array1); // [1,2,3]
console.log(array2); // [1,2,3,4] 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以在析构中使用 spread 运算符。

```
const players = ['Kevin', 'Bobby', 'Nicole', 'Naomi', 'Jim', 'Sherry'];
const [first, second, third, ...unplaced] = players;

console.log(first); // Kevin
console.log(second); // Bobby
console.log(third); // Nicole
console.log(unplaced); // ["Naomi", "Jim", "Sherry"] 
```

Enter fullscreen mode Exit fullscreen mode

```
const { x, y, ...z } = { x: 1, y: 2, a: 3, b: 4 };

console.log(x); // 1
console.log(y); // 2
console.log(z); // { a: 3, b: 4 } 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以使用 spread 操作符来扩展节点列表。

```
const elements = [...document.querySelectorAll('div')];

console.log(elements); // Lists all the div's on the page. 
```

Enter fullscreen mode Exit fullscreen mode

### ...Rest 运算符

rest 操作符允许我们更容易地处理可变数量的函数参数。

```
function doMath(operator, ...numbers) {
  console.log(operator); // 'add'
  console.log(numbers); // [1, 2, 3]
}

doMath('add', 1, 2, 3); 
```

Enter fullscreen mode Exit fullscreen mode

## 模板文字

ES6 中引入的模板文字是一种创建字符串的新方法。

```
const name = 'Kevin';

// The old way...
console.log('Hello, ' + name + '!'); // Hello, Kevin! 
```

Enter fullscreen mode Exit fullscreen mode

```
const name = 'Kevin';

// With ES6 template literals.
console.log(`Hello, ${name}!`); // Hello, Kevin! 
```

Enter fullscreen mode Exit fullscreen mode

在模板文本中，您可以计算表达式。

```
const price = 19.99;
const tax = 0.07;
const total = `The total price is ${price + (price * tax)}`;

console.log(total); // The total price is 21.3893 
```

Enter fullscreen mode Exit fullscreen mode

使用模板文字，您可以更容易地创建多行字符串。

```
console.log('This is some text that flows across\ntwo lines!');

// This is some text that flows across
// two lines!

console.log(`But so does
this text!`);

// But so does
// this text! 
```

Enter fullscreen mode Exit fullscreen mode

## 新的字符串方法

在 ES6 中，我们有一些非常有用的新的字符串方法。

### [T1。开始于()](#startswith)

这个方法返回一个 bool，指示字符串是否以子字符串开头，从作为第二个参数提供的索引开始，默认为`0`。

#### 语法

```
startsWith( searchString )
startsWith( searchString, position ) 
```

Enter fullscreen mode Exit fullscreen mode

#### 例子

```
const str = 'Learn JavaScript Deeply';

console.log(str.startsWith('Learn'));// true
console.log(str.startsWith('JavaScript'));// false
console.log(str.startsWith('Deeply', 17));// true 
```

Enter fullscreen mode Exit fullscreen mode

### [T1。端点()](#endswith)

这个方法返回一个 bool，指示字符串是否以子字符串结尾，可选参数 length 用作`str`的长度，默认为`str.length`。

#### 语法

```
endsWith( searchString )
endsWith( searchString, length ) 
```

Enter fullscreen mode Exit fullscreen mode

#### 例子

```
const str = 'Learn JavaScript Deeply';

console.log(str.endsWith('Deeply'));// true
console.log(str.endsWith('Learn'));// false
console.log(str.endsWith('JavaScript', 16));// true 
```

Enter fullscreen mode Exit fullscreen mode

### [T1。包括()](#includes)

这个方法返回一个 bool，指示字符串是否包含子字符串，从作为第二个参数提供的索引开始，默认为`0`。

#### 语法

```
includes( searchString )
includes( searchString, position ) 
```

Enter fullscreen mode Exit fullscreen mode

#### 例子

```
const str = 'Learn JavaScript Deeply';

console.log(str.includes('JavaScript'));// true
console.log(str.includes('Javascript'));// false
console.log(str.includes('PHP'));// false 
```

Enter fullscreen mode Exit fullscreen mode

### [T1。重复()](#repeat)

此方法返回一个新字符串，该字符串包含指定数量的原始字符串副本的串联。

#### 语法

```
repeat( count ) 
```

Enter fullscreen mode Exit fullscreen mode

#### 例子

```
const str = 'Deeply';

console.log(str.repeat(3));// DeeplyDeeplyDeeply
console.log(str.repeat(2.5));// DeeplyDeeply (converts to int)
console.log(str.repeat(-1));// RangeError 
```

Enter fullscreen mode Exit fullscreen mode

## 增强的对象文字量

```
const first = 'Kevin';
const last = 'Langley';
const age = 29; 
```

Enter fullscreen mode Exit fullscreen mode

让我们将变量分配给对象的属性！

```
const person = {
  first: first,
  last: last,
  age: age
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们再做一次，但是这次用的是对象文字。

```
const person = {
  first,
  last,
  age
}; 
```

Enter fullscreen mode Exit fullscreen mode

您甚至可以将对象文字与普通的键值对混合使用。

```
const person = {
  firstName: first,
  lastName: last,
  age
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以对对象初始化器上的方法定义使用较短的语法。

ES5 中我们都熟悉的语法...

```
var obj = {
  foo: function() {
    console.log('foo');
  },
  bar: function() {
console.log('bar');

}

}; 
```

Enter fullscreen mode Exit fullscreen mode

现在可以用新的方法定义语法来简化！

```
const obj = {
  foo() {
    console.log('foo');
  },
  bar() {
    console.log('bar');
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

或者甚至定义在运行时在对象文字中计算的键。

```
let i = 0;
const a = {
  ['foo' + ++i]: i,
  ['foo' + ++i]: i,
  ['foo' + ++i]: i
};

console.log(a.foo1); // 1
console.log(a.foo2); // 2
console.log(a.foo3); // 3 
```

Enter fullscreen mode Exit fullscreen mode

让我们用键的字符串模板文字来清理一下吧！

```
let i = 0;
const a = {
  [`foo${++i}`]: i,
  [`foo${++i}`]: i,
  [`foo${++i}`]: i
};

console.log(a.foo1); // 1
console.log(a.foo2); // 2
console.log(a.foo3); // 3 
```

Enter fullscreen mode Exit fullscreen mode

## 新的阵列方法！

### Array.find()

`Array.find`静态方法返回所提供的数组中满足测试函数的第一个值，如果没有元素匹配，则返回 undefined。

#### 语法

```
// Arrow function
Array.find( element => { conditional } )
Array.find((element, index) => { conditional } )
Array.find((element, index, array) => { conditional } )

// Callback function
Array.find(callbackFn)
Array.find(callbackFn, thisArg)

// Inline callback function
Array.find(function callbackFn(element) { conditional })
Array.find(function callbackFn(element, index) { conditional })
Array.find(function callbackFn(element, index, array){ conditional })
Array.find(function callbackFn(element, index, array) { conditional }, thisArg) 
```

Enter fullscreen mode Exit fullscreen mode

#### 例子

```
const posts = [
  {
    id: 1,
    title: 'Hello World!'
  },
  {
    id: 2,
    title: 'Learn JS Deeply!'
  }
];

const post = posts.find(post => post.id === 2);
console.log(post); // {id: 2, title: "Learn JS Deeply!"} 
```

Enter fullscreen mode Exit fullscreen mode

### Array.findIndex()

`Array.findIndex`静态方法返回所提供数组中满足测试函数的第一个值的索引，如果没有元素匹配，则返回 undefined。

#### 语法

```
// Arrow function
Array.findIndex( element => { conditional } )
Array.findIndex((element, index) => { conditional } )
Array.findIndex((element, index, array) => { conditional } )

// Callback function
Array.findIndex(callbackFn)
Array.findIndex(callbackFn, thisArg)

// Inline callback function
Array.findIndex(function callbackFn(element) { conditional })
Array.findIndex(function callbackFn(element, index) { conditional })
Array.findIndex(function callbackFn(element, index, array){ conditional })
Array.findIndex(function callbackFn(element, index, array) { conditional }, thisArg) 
```

Enter fullscreen mode Exit fullscreen mode

#### 例子

```
const posts = [
  {
    id: 1,
    title: 'Hello World!'
  },
  {
    id: 2,
    title: 'Learn JS Deeply!'
  }
];

const post = posts.findIndex(post => post.id === 2);
console.log(post); // 1 - Remember, this is zero based! 
```

Enter fullscreen mode Exit fullscreen mode

### Array.from()

静态方法让你从类似数组的对象和可迭代对象中创建。

#### 语法

```
// Simple without any mapping
Array.from(arrayLike);

// Arrow function mapping (Can't utilize the third argument for the `thisArg` argument since the mapping uses an arrow function)
Array.from(arrayLike, element => [...] );
Array.from(arrayLike, (element, index) => [...] );

// Mapping named function
Array.from(arrayLike, mapFn );
Array.from(arrayLike, mapFn, thisArg); 
```

Enter fullscreen mode Exit fullscreen mode

#### 例子

不幸的是，我们都知道不能简单地循环遍历一个节点列表。

```
const headers = document.querySelectorAll('h1');
const titles = headers.map(h1 => h1.textContent); // TypeError: headers.map is not a function 
```

Enter fullscreen mode Exit fullscreen mode

但是，使用`Array.from`我们可以很容易地循环它们！

```
const headers = document.querySelectorAll('h1');
const headersArray = Array.from(headers);
const titles = headersArray.map(h1 => h1.textContent);

// Or we can use the `mapFn` parameter of `Array.from`.

const titles = Array.from(
  document.querySelectorAll('h1'),
  h1 => h1.textContent
); 
```

Enter fullscreen mode Exit fullscreen mode

### Array.indexOf()

静态方法返回传入元素在数组中的索引。

#### 语法

```
Array.indexOf(searchElement)
Array.indexOf(searchElement, index) 
```

Enter fullscreen mode Exit fullscreen mode

#### 例子

```
const values = Array.of(123, 456, 789);

console.log(values); // [123,456,789] 
```

Enter fullscreen mode Exit fullscreen mode

### (数组编号)

静态方法`Array.of`创建一个新的`Array`,它包含传递给它的值，而不管参数的类型或数量。

这与`Array`构造函数方法之间的最大区别是它们如何处理单个整数参数。`Array.of(5)`将创建一个只有一个元素的数组，但是`Array(5)`创建一个长度为 5 的空数组。

#### 语法

```
Array.of(firstElement)
Array.of(firstElement, secondElement, ... , nthElement) 
```

Enter fullscreen mode Exit fullscreen mode

#### 例子

```
const values = Array.of(123, 456, 789);
console.log(values); // [123,456,789] 
```

Enter fullscreen mode Exit fullscreen mode

## 承诺

Promise 对象用于异步操作，表示该操作的最终完成或失败，及其结果值。 <sup>[1](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)</sup> 承诺常用于异步取数据。

承诺存在于四种状态之一:

*   **待定** -定义时，这是承诺的初始状态。
*   **完成** -当操作成功完成时。
*   **拒绝** -当操作失败时。
*   **结算** -当它完成或被拒绝时。

```
const promiseA = new Promise( ( resolve, reject ) => {
  setTimeout( () => {
    resolve('Resolved!');
  }, 300 );
} ); 
```

Enter fullscreen mode Exit fullscreen mode

[![Promise Harry Potter Gif](img/e28bd9a35dc74e73a15758188e015bb8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tqhjnrNp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/kevinlangleyjr-dev/image/upload/v1635020656/blog/modern-js/promise_z67bfm.gif)

### 承诺 API

Promise 类中有 6 个静态方法:

#### `Promise.all`

`.all()`方法接受`Promise`对象的 iterable 作为它唯一的参数，并返回一个单独的`Promise`对象，该对象解析为传递给它的`Promise`对象的结果数组。当*传入的所有*承诺解析**或**将拒绝时，从该方法返回的承诺将被解析，如果其中任何一个被拒绝的话。

#### `Promise.allSettled`

`.allSettled()`方法接受一个由`Promise`对象组成的 iterable 作为它唯一的参数，并将返回一个`Promise`对象，当所有对象都通过 resolve 或 reject 解决时，该对象将通过一个对象数组解析每个`Promise`对象的结果。`.allSettled()`和`.all()`最大的区别在于，后者根据传入承诺的结果来解决或拒绝，而前者将在所有传入的`Promise`对象都用`resolve` **或** `reject`解决后解决。

#### `Promise.any`

`.any()`方法接受一个`Promise`对象的 iterable 作为它唯一的参数，并将返回一个单个的`Promise`对象，当任何传入的`Promise`对象解析时，该对象解析，并带有被解析的原始`Promise`对象的值。

#### `Promise.race`

`.race()`方法接受一个`Promise`对象的 iterable 作为它唯一的参数，并且将返回一个`Promise`对象，一旦一个传入的`Promise`对象解析或拒绝，该对象就会解析或拒绝。

#### `Promise.resolve`

`.resolve()`方法返回一个用提供的值解析的`Promise`对象。如果一个`thenable`(有一个`then`方法)是返回值，那么返回的`Promise`对象将跟随那个`then`，直到返回最终值。

#### `Promise.reject`

`.reject()`方法返回一个被拒绝的`Promise`对象，并给出拒绝的原因。

### 我们来跳几个例子吧！

```
const p1 = new Promise( (resolve, reject) => {
  resolve('Learn JavaScript Deeply!');
} );

p1.then(data => console.log(data)); // Learn JavaScript Deeply 
```

Enter fullscreen mode Exit fullscreen mode

#### 承诺甚至可以连锁！

```
const p2 = new Promise( (resolve, reject) => {
  setTimeout( () => resolve( 1 ), 1000 ); // Resolve with `1` after 1 second.
} );

p2
  .then( value => {
    console.log( value ); // 1
    return value * 2;
  } )
  .then( value => {
    console.log( value ); // 2
    return value * 4;
  } )
  .then( value => {
    console.log( value ); // 8
    return value * 2;
  } ); 
```

Enter fullscreen mode Exit fullscreen mode

#### 让我们来看一些更深入的例子。

通常，在尝试获取数据时，您会遇到使用承诺的情况。

```
const postsPromise = fetch('https://miami.wordcamp.org/2018/wp-json/wp/v2/posts');

console.log(postsPromise);

// Promise {pending}
// __proto__: Promise
// [[PromiseStatus]]: "pending"
// [[PromiseValue]]: undefined

postsPromise
  .then(data => console.log(data));

// Response {type: "cors", url: "https://miami.wordcamp.org/2018/wp-json/wp/v2/posts", redirected: false, status: 200, ok: true, ...}

postsPromise
  .then(data => data.json()) // Get's JSON value from fetch.
  .then(data => console.log(data)) // Console log the value.
  .catch(err => console.error(err)); // Catch any errors.

// {id: 5060, date: "2018-03-15T17:41:09", ...}
// {id: 4954, date: "2018-03-14T00:21:10", ...}
// {id: 4943, date: "2018-03-13T19:16:11", ...}
// {id: 4702, date: "2018-03-10T11:04:36", ...}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用如下所示的`.catch`方法捕获承诺中抛出的错误。

```
const p = new Promise((resolve, reject) => {
  reject(Error('Uh oh!'));
});

p.then(data => console.log(data)); // Uncaught (in promise) Error: Uh oh!

p
  .then(data => console.log(data))
  .catch(err => console.error(err)); // Catch the error! 
```

Enter fullscreen mode Exit fullscreen mode

## 类

在幕后，ES6 类并不是什么全新的东西。它们主要提供更方便的语法来创建老式的构造函数。

```
// Class declaration
class Animal {

}

// Class expression
const Animal = class {} 
```

Enter fullscreen mode Exit fullscreen mode

```
class Animal {

  constructor(name) {
    this.name = name;
  }

  speak() {
    console.log(`${this.name} makes a noise.`);
  }
}

class Dog extends Animal {
  speak() {
    console.log(`${this.name} barks!`);
  }
}

const puppy = new Dog('Spot');
puppy.speak(); // Spot barks! 
```

Enter fullscreen mode Exit fullscreen mode

```
class Animal {
  constructor(name) {
    this.name = name;
  }
  speak() {
    console.log(`${this.name} makes a noise.`);
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    this.breed = breed;
  }

  speak() {
    console.log(`${this.name} barks!`);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 异步/等待

ES2017 或 ES8 中增加了`await`关键字和`async`功能。它们实际上只是承诺之上的语法糖，有助于使异步代码更容易、更干净地读写。将`async`关键字添加到函数中是为了表示它将返回一个要解决的承诺，而不是直接返回值。

目前，你只能在一个`async`函数中使用`await`关键字，但是有一个关于[顶级 await](https://github.com/tc39/proposal-top-level-await) 的开放提议。

让我们看看一些承诺，以及如何将它们转换成 async/await！

```
const getUser = username => {
  return fetch(`https://api.github.com/users/${username}`)
  .then( response => {
    if ( ! response.ok ) {
      throw new Error( `HTTP error! Response status: ${response.status}` );
    }
    return response.json();
  } )
  .catch( e => console.error( e ) );
}

const user = getUser( 'kevinlangleyjr' );
console.log( user ); 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们将它改为使用 async/await。

```
const getUser = async username => {

  try {

    const response = await fetch(`https://api.github.com/users/${username}`)

    if ( ! response.ok ) {
      throw new Error( `HTTP error! Response status: ${response.status}` );
    }

    const user = await response.json();

    return user;

  } catch ( e ) {
    console.error( e );
  }
}

const user = getUser( 'kevinlangleyjr' );
console.log( user ); 
```

Enter fullscreen mode Exit fullscreen mode

不同之处在于，我们现在将整个函数体包装在一个`try/catch`块中，在这里我们执行`await`操作。这将捕捉我们所等待的承诺中的任何错误。在这里，我们也在函数定义的前面放置了`async`关键字，这是`await`正常工作所必需的。

## Set/WeakSet

Set 对象允许您存储任何类型的唯一值。虽然它类似于数组，但有两个很大的区别。第一个是数组可以有重复的值，而集合不能。另一个是数组是按索引排序的，而集合只能按插入的顺序迭代。

您可以使用`.entries()`捕获集合中的条目，这将保持对原始插入对象的引用。

Map 和 Set 都有`keys()`和`values()`方法，但是在 Set 上，这些方法将返回相同的可迭代值，因为 Set 不是键值对，而只是值。

```
const set = new Set();

set.add( 9 );      // Set(1) { 9 }
set.add( 9 );      // Set(1) { 9 } Ignored because it already exists in the set.
set.add( 7 );      // Set(2) { 9, 7 }
set.add( 7 );      // Set(2) { 9, 7 } Ignored because it already exists in the set.
set.add( 'Text' ); // Set(3) { 9, 7, 'Text' }
set.add( true );   // Set(4) { 9, 7, 'Text', true }

const user = {
  name: 'Kevin',
  location: 'Florida'
};

set.add( user );   // Set(5) { 9, 7, 'Text', true, { value: { location: 'Florida', name: 'Kevin' } } }

set.has( 9 );    // true
set.has( true ); // true

const newUser = {
  name: 'Kevin',
  location: 'Florida'
};

set.has( newUser );  // false - This doesn't do object property matching, it is an object reference check.

set.has( user );  // true

// Use .entries() to capture all the values in the set.

const entries = set.entries();

console.log( entries ); // SetIterator {9 => 9, 7 => 7, 'Text' => 'Text', true => true, {…} => {…}}

set.delete( true );  // true - This change is still reflected in entries because it keeps the reference

set.has( true );  // false

console.log( entries ); // SetIterator {9 => 9, 7 => 7, 'Text' => 'Text', {…} => {…}}

set.size // 5

// Iterating over a set is pretty simple but you have a few options.
for ( let item of set.values() ) {
  console.log( item );
}

// Is the same as,
for ( let item of set.keys() ) {
  console.log( item );
}

// And is also the same as,
for ( let [key, value] of set.entries() ) {
  console.log( key );
} 
```

Enter fullscreen mode Exit fullscreen mode

Set 和 WeakSet 的主要区别在于，后者只接受对象，而前者可以接受任何类型的值。另一个大的区别是弱集合持有对其中对象的弱引用。因此，如果 WeakSet 中没有对某个对象的进一步引用，这些对象就可以被垃圾回收。WeakSet 也是不可枚举的，也没有办法列出其中的当前对象，但是您可以`.add()`、`.delete()`，并检查 WeakSet `.has()`是否是一个对象。

```
let ws = new WeakSet();
let user = { name: 'Kevin', location: 'Florida' };
ws.add( user );
console.log( ws.has( user ) ); // true
user = null; // The reference to `user` within the WeakSet will be garbage collected shortly after this point. 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用上面的代码示例，然后在 Chrome 开发工具中通过单击 Performance 选项卡下的垃圾桶来强制垃圾收集，您会看到`ws`不再保存对`user`的引用。

[![Force JavaScript garbage collection in Chrome](img/c8a2d3b2dd181e7b59192ac6552e7d89.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--liakRNSL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/kevinlangleyjr-dev/image/upload/v1637687473/blog/modern-js/garbage-collection_u4khbx.png)

## 地图/WeakMap

另一方面，Map 对象持有键-值对，跟踪插入顺序，并且可以将对象或基元值用于键和值。

虽然对象与地图非常相似，而且在 ES6 中引入对象之前，它们一直被用来代替地图，但还是有一些关键的区别。映射可以有任何类型值的键，包括函数和对象，而对象只能有字符串和符号作为键。您不能直接迭代对象，但有几种方法可以迭代地图。另外，`JSON.stringify()`没有原生支持，但是通过一点[的工作](https://stackoverflow.com/questions/29085197/how-do-you-json-stringify-an-es6-map)是可能的。

```
const map = new Map();

map.set( 1, 'Learn' );// Map(1) {1 => 'Learn'}
map.set( 2, 'JavaScript' );// Map(2) {1 => 'Learn', 2 => 'JavaScript'}
map.set( 3, 'Deeply' );// Map(3) {1 => 'Learn', 2 => 'JavaScript', 3 => 'Deeply'}
map.set( 3, 'Very Deeply' );// Map(3) {1 => 'Learn', 2 => 'JavaScript', 3 => 'Very Deeply'} - Overwrite value of Deeply set on line above.

map.set(
  '1',
  'Different string value for the string key of 1.'
);  // Map(4) {1 => 'Learn', 2 => 'JavaScript', 3 => 'Very Deeply', '1' => 'Different string value for the string key of 1.'}

map.set(
  true,
  'Different string value for the bool of true.'
);  // Map(5) {1 => 'Learn', 2 => 'JavaScript', 3 => 'Very Deeply', '1' => 'Different string value for the string key of 1.', true => 'Different string value for the bool of true.'}

map.size // 5

map.delete( true );  // Map(4) {1 => 'Learn', 2 => 'JavaScript', 3 => 'Very Deeply', '1' => 'Different string value for the string key of 1.'}

map.delete( '1' );  // Map(3) {1 => 'Learn', 2 => 'JavaScript', 3 => 'Very Deeply'}

map.clear();  // Map(0) {size: 0}

/* New example with objects for keys. */

const user1 = { name: 'James' };
const user2 = { name: 'Kevin' };

// New Map for votes.
const votes = new Map();
votes.set( user1, 10 );  // Map(1) {{ name: 'James' } => 10}
votes.has( user1 );  // true
votes.has( user2 );  // false - Not set yet.
votes.set( user2, 20 );  // Map(2) {{ name: 'James' } => 10, { name: 'Kevin' } => 20}

votes.has( user1 );  // true
votes.has( user2 );  // true

votes.get( user1 );  // 10
votes.get( user2 );  // 20

votes.size // 2 
```

Enter fullscreen mode Exit fullscreen mode

## 参考文献

1.  [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)