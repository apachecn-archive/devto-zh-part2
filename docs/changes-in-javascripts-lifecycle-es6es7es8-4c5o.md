# Es6 与 ES7 在 Javascript 生命周期中的变化[ES6，Es7，ES8]

> 原文：<https://dev.to/marpme/changes-in-javascripts-lifecycle-es6es7es8-4c5o>

首先，我想更深入地了解 Javascript 实际上是如何构建的，然后我们可以深入了解一些关键的变化，这些变化可能会在某个时候应用，以保持项目的可持续性，或者使代码片段更可读/更容易理解。

## Javascript 究竟是什么？

好吧，为了确定是什么 Javascript，我们必须更深入地挖掘。你听说过 ECMAScript 吗？它是实际实现背后的标准化语言，由 Javascript 表示。因此，我们讨论的所有变化都将在 ECMAScript 标准中可用，但可能目前在 Javascript 中不可用，或者更好地说，在您当前的项目中不可用。将 babeling 你的项目看作是一个可能的解决方案。

ECMAScript 也是大多数开发人员在他们的项目中呼吁**ES5(ECMAScript 5)**/**ES6(ECMAScript 6)**支持的原因。

基本上就是他们项目的标准化水平。相比之下，您实际上可以将 USB3 支持棒插入 USB2 插槽，但它可能缺少 USB3 的一些功能，但反过来，您也可以将 USB2 设备插入 USB3 支持的插槽，但这也可能缺少 USB3 的一些功能。在计算机科学中，我们称之为向上/向下兼容性。

这些兼容性无处不在。这些都是由 TC39 委员会运行的 ECMAScript 标准保证的。这个委员会也在决定什么应该达到他们的最终标准，什么不能。

## ES6 vs ES7(又名。2016)

### Array.indexOf vs 数组。包含

```
// ES6 way of searching for values in an array
let numbers = [1, 2, 3, 4];
if(numbers.indexOf(2) !== -1) {
  console.log('Array contains value');
}

// ES7 way of searching for values in an array
if(numbers.includes(2)) {
  console.log('Array contains value');
} 
```

Enter fullscreen mode Exit fullscreen mode

### 取幂运算符

```
let base = 3;
let exponent = 4;

// old way Math.pow()
console.log(Math.pow(base ,exponent)) //81

// new way
console.log(base**exponent); //81 
```

Enter fullscreen mode Exit fullscreen mode

### 解构

```
// Before:
var width = 200;
var height = 400;

// After:
let [width, height] = [200, 400];

// before: 
const calculateArea = (areaParameters) => areaParameters[0] * areaParameters[1]
calculateArea([200, 400]);

// or for better naming in older functions:
const calculateArea = ([width, height]) => width * height
calculateArea([200, 400]); 
```

Enter fullscreen mode Exit fullscreen mode

### 默认参数

```
// before:
const myFunction = (name) => name ? `I love ${name}` : 'I love marpme'

// now:
const myFunction = (name = "marpme") => `I love ${name}` 
```

Enter fullscreen mode Exit fullscreen mode

### 在函数内传播自变量

```
// before with an arraylike object, very difficult:
const sumAll = () => arguments.reduce((sum, next) => sum + next, 0)

// now, real array also more way clear
const sumAll = (...numbers) => numbers.reduce((sum, next) => sum + next, 0) 
```

Enter fullscreen mode Exit fullscreen mode

### 数组串联

```
//before: 
const arrayOne = [1,2,3]
const arrayTwo = [4,5,6]
Array.prototype.concat(arrayOne, arrayTwo) // [1, 2, 3, 4, 5, 6]

// now shorthand:
const arrayOne = [1,2,3]
const arrayTwo = [4,5,6]
const new = [...arrayOne, ...arrayTwo] // [1, 2, 3, 4, 5, 6] 
```

Enter fullscreen mode Exit fullscreen mode

### 对象分配/合并

```
//before: 
const objectOne = { love: true, number: 6 }
const objectTwo = { es7: false, number: 8 }
Object.assign({}, objectOne, objectTwo) // {love: true, number: 8, es7: false}

// now shorthand:
const objectOne = { love: true, number: 6 }
const objectTwo = { es7: false, number: 8 }
const new = {...objectOne, ...objectTwo} // {love: true, number: 8, es7: false} 
```

Enter fullscreen mode Exit fullscreen mode

### 类，继承...

查看您的旧 java 类，在 javascript 中我们不需要它。保持真实，采用基本的功能方法，你会做得很好。

```
// Before:
var Person = function(name) {
 this.name = name; 
}

Person.prototype.sayHi = function() {
 console.log(`hi, I'm ${this.name}!`); 
}

// After - this produces exactly the same as above:
class Person {
  constructor(name) {
    this.name = name; 
  }

  sayHi() {
   console.log(`hi, I'm ${this.name}!`);
  }
}

// Let's test it:
var jack = new Person('Jack');
jack.sayHi(); 
```

Enter fullscreen mode Exit fullscreen mode

## ECMAScript 8 (ES8/2017)

任何 Javascript 实现(V8-Chrome、NodeJS 等)都完全支持它...).它基本上伴随着`async/await`模式，这种模式消除了以“链式”方式处理承诺的痛苦。

`Object.values` / `Object.entries`，返回所有对象值的数组或所有对象键。

支持在对象上拥有`getOwnPropertyDescriptors(obj);`。最终，这将允许我们有可能围绕这些对象构建装饰器。

还有像`SharedArrayBuffer`这样的内存共享对象，实际上可以同时被很多线程使用。还有一个名为`Atomic`的对象的名称空间，用一些静态方法来处理这样的线程机制。