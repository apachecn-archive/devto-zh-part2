# ECMAScript 6 (ES6)的注释

> 原文：<https://dev.to/hardy613/notes-on-ecmascript-6-es6-267l>

## 简介

*这并不意味着取代官方文件*。

这篇文章没有涵盖所有的 ES6 特性。

错别字和更正:[https://github.com/hardy613/es6-notes/issues](https://github.com/hardy613/es6-notes/issues)

## ES6 变量

### `var` vs `let`

传统上，关键字`var`用`value` :
初始化`identifier`

```
var my_variable = 'value';
//1 //2         //3 

//1 the var keyword
//2 the identifier
//3 the value 
```

Enter fullscreen mode Exit fullscreen mode

命名变量标识符是有规则的。这些是:

*   标识符不能是关键字
*   可以是字母数字，但不能以数字开头
*   `$`和`_`也是标识符允许的字符

由`var`标注的变量具有整个函数的范围。

```
function myFunc() {
    if(true) {
        var my_var = 'test';
    }
    console.log(my_var); // test
} 
```

Enter fullscreen mode Exit fullscreen mode

### `let`关键词

`let`优先于`var`。被`let`取消的变量在它们被定义的`block`内有它们的作用域
。

```
function myFunc() {
    if(true) {
        let my_var = 'test';
    }
    console.log(my_var); // TypeError
} 
```

Enter fullscreen mode Exit fullscreen mode

块作用域允许变量 [`shadowing`](https://en.wikipedia.org/wiki/Variable_shadowing) 。

```
function myFunc() {
    let my_var = 'test';
    if(true) {
        let my_var = 'new test';
        console.log(my_var); // new test
    }
    console.log(my_var); // test
} 
```

Enter fullscreen mode Exit fullscreen mode

### `const`关键词

ES6 还引入了一个新的变量关键字:`const`。用`const`关键字
声明的变量是块范围的，就像`let`一样，但是它们不能通过重新赋值
改变，也不能被重新声明；它们是不可改变的。

```
const version = '0.0.1';
version = '0.0.2'; // TypeError: invalid assignment to const

const name = 'bill';
const name = 'ted'; // SyntaxError: Identifier 'name' has already been declared 
```

Enter fullscreen mode Exit fullscreen mode

由`const`(常量)声明的变量不能改变。然而，使用
for 循环，作用域在每个循环开始时被重新声明，在这里可以初始化一个新的
`const`。

```
 function myFunc(items) {
    for(let i = 0; i < items.length; i++) {
        const message = items[i] + ' found at index: ' + i;
        console.log(message);
    } 
}

myFunc(['test', 100, 200]);
// test found at index: 0
// 100 found at index: 1
// 200 found at index: 2 
```

Enter fullscreen mode Exit fullscreen mode

## ES6 为/of

`for/of`循环使用[可迭代协议](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#The_iterable_protocol)创建一个循环。Strings、Array、TypedArray、Map、Set、NodeList 和自定义的 iterable 函数钩子都可以和`for/of`一起使用。

```
const arr = [1, 2, 3];
for(const number of arr) {
    console.log(number) // 1 2 3
} 
```

Enter fullscreen mode Exit fullscreen mode

要迭代一个对象，你可以使用协议`Object.entries()`。
这将给出`['key', 'value']`对的数组。与`for/in`不同的是，
不会遍历对象原型

```
const obj = { a:1, b:2, c:3 };
for(const prop of Object.entries(obj)) {
    console.log(prop); // ['a', 1] ['b', 2] ['c', 3]
} 
```

Enter fullscreen mode Exit fullscreen mode

## ES6 模板文字

对于使用变量的字符串，或者需要使用快速 javascript 表达式进行
的字符串，模板文字非常方便。模板文字用反勾号
括起来。模板文字也可以有`placeholders`、
这些用美元符号和花括号`${placeholder}`声明。

```
const number = 42;
const str = `Here's my favourite number: ${number}.`;
console.log(str) // Here's my favourite number: 42.

const count = 0;
console.log(`${count + 1}`); // 1 
```

Enter fullscreen mode Exit fullscreen mode

模板文字可以是在
反勾号之前带有函数标识符的`tagged`。该函数允许您解析模板文本。第一个
参数是一个字符串值数组，其余的参数与模板文本中的占位符
相关。

```
const name = 'Theodor Logan';
const age = 21;

function showNameAndAge(strings, nameHolder, ageHolder) {
    // strings[0] is empty because we started with a
    // ${name} placeholder, placeholders at the start or 
    // at the end of a template literal will have
    // an empty string before or after respectively 
    const piece1 = strings[1]; // is
    const piece2 = strings[2]; // years of age.
    let ageNotice = '';
    if(ageHolder < 25) {
        ageNotice = 'What a babyface. ';
    } else {
        ageNotice = 'What an oldtimer. ';
    }
    return `${ageNotice}${nameHolder}${piece1}${ageHolder}${piece2}`;
}

showNameAndAge`${name} is ${age} years of age.` 
// What a babyface. Theodor Loagn is 21 years of age. 
```

Enter fullscreen mode Exit fullscreen mode

标记模板文本不需要返回字符串。

## ES6 箭头功能

箭头函数是一种简写语法，用于不包含其
自身`this`、`arguments`、`super`或`new.target`的函数，并且不能用作
构造函数。

```
const arr = ['hammer', 'nails', 'pizza', 'test'];
console.log(arr.map(value => value.length)); // [6, 5, 5, 4] 
```

Enter fullscreen mode Exit fullscreen mode

箭头函数对匿名函数
很有用，但是它们的威力来自于`this`的词法作用域。

```
function es6LexicalScope() {
    this.timeSpentSeconds = 0;
    setInterval(() => {
        console.log(this.timeSpentSeconds++); // 1 2 3 ...
    }, 1000);
}
es6LexicalScope(); 
```

Enter fullscreen mode Exit fullscreen mode

箭头功能没有`prototype`。

```
const func = () => {};
console.log(func.prototype); // undefined 
```

Enter fullscreen mode Exit fullscreen mode

要将一个对象作为隐式返回，您可以将该对象包装在
[`grouping operator`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Grouping)(括号)中。

```
const returnObjBad = () => { test: 'value' };
console.log(returnObj); // undefined

const returnObjGood = () => ({test: 'value'});
console.log(returnObj); // { test: 'value' } 
```

Enter fullscreen mode Exit fullscreen mode

如果您注意到了，在提供的示例中，arrow
函数的用法有一点不同。`()`的用法:

*   没有参数的箭头函数需要`()`
*   带有一个参数`()`的箭头功能是可选的
*   带有两个或更多参数的箭头功能需要`()`
*   只返回的箭头函数，不需要`{}`、`return`或`;`

```
const fn1 = () => {[Native Code]};
const fn2 = param => {[Native Code]};
const fn2a = (param) => {[Native Code]};
const fn3 = (param1, param2) => {[Native Code]};
const fn4 = param => param; 
```

Enter fullscreen mode Exit fullscreen mode

## ES6 解构赋值

析构赋值使您可以从数组或对象中解包值。

```
const [x, y] = [1, 2, 3, 4, 5];
console.log(x); // 1
console.log(y); // 2;

const person = { name: 'Bill', age: 42, email: 'bill@example.ca', url: 'http://example.ca' };
const {name, age} = person;
console.log(name, age); // Bill, 42 
```

Enter fullscreen mode Exit fullscreen mode

有时候你想保留所有其他的东西。这就是传播
符 [`...`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) 派上用场的地方。

```
const [x, y, ...allTheRest] = [1, 2, 3, 4, 5];
console.log(x, y, allTheRest); // 1, 2, [3, 4, 5]

const person = { name: 'Bill', age: 42, email: 'bill@example.ca', url: 'http://example.ca' };
const {name, age, ...details} = person;
console.log(name, age, details); // Bill, 42, {email: 'bill@example.ca', url: 'http://example.ca'} 
```

Enter fullscreen mode Exit fullscreen mode

你也可以析构来构建新的变量！

```
const otherObj = {};
const person = { name: 'Bill', age: 42, email: 'bill@example.ca', url: 'http://example.ca' };
const obj = {...otherObj, person};
console.log(obj); // { person: {[...]} } 
```

Enter fullscreen mode Exit fullscreen mode

`obj`现在有我们`person`的财产与我们的人`Bill`在一起。如果在`otherObj`中已经设置了人员
属性，那么我们将覆盖该属性。
让我们看看用析构从字符串中解包长度属性。

```
const arr = ['hammer', 'nails', 'pizza', 'test'];
// without destructuring
console.log(arr.map(value => value.length)); // [6, 5, 5, 4]
// with destructuring
console.log(arr.map(({ length }) => length)); // [6, 5, 5, 4] 
```

Enter fullscreen mode Exit fullscreen mode

让我们分解一下刚刚添加的行。`console.log(arr.map(`是漂亮的
标准。`({ length })`是我们的 arrow 函数的参数，我们将
传入一个字符串，并从该字符串中析构长度属性，并将
作为一个名为`length`的变量传递。函数参数是字符串
的长度。`=> length));`我们剩下的箭头功能。属性也是变量标识符
，我们只返回`length`。如果你需要一个默认的带有析构的
，你也可以这么做！

```
const { name = 'Bill', age = 30 } = { name: 'Ted' };
console.log(name, age)// Ted, 30

const [x = 5, y = 10] = [20];
console.log(x, y) // 20, 10 
```

Enter fullscreen mode Exit fullscreen mode

## ES6 默认参数

函数接受默认参数和析构参数。

```
function addToFive(addTo = 0) {
    return addTo + 5;   
}
const ex1 = addToFive();
const ex2 = addToFive(5);
console.log(ex1, ex2); // 5, 10

function fullname ({firstname, lastname}) {
    return `${firstname lastname}`;
}
const user = { firstname: 'Theodore', lastname: 'Logan', age: '20' };
const fullname = fullname(user);
console.log(`Hello ${fullname}`); 
```

Enter fullscreen mode Exit fullscreen mode

当析构时，你也可以指定默认值。

```
function myFunc({age = 42}) {
    console.log(age); // 42
};
myFunc({name: 'Theodor'}); 
```

Enter fullscreen mode Exit fullscreen mode

## ES6 类

ES6 `class`是 ES2015 中引入的传统类的新语法。
ES6 类没有向 JavaScript 引入任何东西，相反*只是另一种方式
编写 JavaScript 类*。类主体受 JavaScript 的
`strict mode`约束，类主体有新的关键字，一些单词被
保留为关键字以备将来使用。

和函数一样，有两种方法来声明一个类，`expression`或
T1。

```
// expression
const Instrument = class {}; // or class Instrument {}
const instrument = new Instrument();

// declaration
class Instrument {}
const instrument = new Instrument(); 
```

Enter fullscreen mode Exit fullscreen mode

与函数不同，类在使用之前必须声明或表达。

### 构造函数

`constructor`是为类保留的关键字，代表在创建和初始化过程中调用的函数
。

```
class Instrument {
    constructor(props) {
        this._make = props.make;
        this._type = props.type;
    }

    get type() {
        return this._type;
    }
}

const noiseMaker = new Instrument({ make: 'Crafter', type: 'Guitar' });
console.log(noiseMaker.type); // Guitar 
```

Enter fullscreen mode Exit fullscreen mode

### getter 和 Setters

`getters`和`setters`允许对类属性进行读写访问，而
不必定义方法。Getters 和 setters 可由继承的
类访问。

```
class Instrument {
    constructor(props) {
        this._make = props.make;
        this._type = props.type;
    }

    set make(make) {
        this._make = make;
    }

    get make() {
        return this._make;
    }

    set type(type) {
     this._type = type;
    }

    get type() {
        return this._type;
    }

}

const noiseMaker = new Instrument({ make: 'Crafter', type: 'Guitar' });
noiseMaker.type = 'Drums';
noiseMaker.make = 'Yamaha';
console.log(noiseMaker.type); // Drums 
```

Enter fullscreen mode Exit fullscreen mode

### 继承

类可以继承父类。保持乐器，我们来做一个
吉他课。关键字`super`指的是被继承的类。

```
class Guitar extends Instrument {
    constructor(make) {
        super({make, type: 'Guitar'});
    }
    set make (make) {
        super.make = make
    }
    get make() {
        return `The make of the guitar is: ${super.make}`;
    }
}

const myGuitar = new Guitar('Fender');
console.log(myGuitar.make); // The make of the guitar is: Fender
myGuitar.make = 'Crafter';
console.log(myGuitar.make); // The make of the guitar is: Crafter
console.log(myGuitar.type); // Guitar 
```

Enter fullscreen mode Exit fullscreen mode

### 方法

类方法是去掉了关键字`function`的函数。

```
class Guitar extends Instrument {
    constructor(make) {
        super({make, type: 'Guitar'});
    }

    set make (make) {
        super.make = make
    }

    get make() {
        return `The make of the guitar is: ${super.make}`;
    }

    log() {
        console.log(this.make, this.type);
    }
}

const fender = new Guitar('Fender');
fender.log(); // The make of this guitar is: Fender, Guitar 
```

Enter fullscreen mode Exit fullscreen mode

### 对象定义

目前我们的对象`.toString()`定义将返回`[object Object]`。
我们可以用一个方法属性来改变定义。

```
class Guitar extends Instrument {
    constructor(make) {
        super({make, type: 'Guitar'});
    }

    set make (make) {
        super.make = make
    }

    get make() {
        return `The make of the guitar is: ${super.make}`;
    }

    toString() {
        return `[${super.name}  ${this.type}]`;
    }
}

const fender = new Guitar('Fender');
console.log(fender.toString()); // [Instrument Guitar] 
```

Enter fullscreen mode Exit fullscreen mode

### `super`和`this`

在你可以在继承类的构造函数中使用`this.property`之前，你的
必须首先调用`super()`。

```
class Guitar extends Instrument {
    constructor(make, stringCount) {
        super({make, type: 'Guitar'});
        this._stringCount = stringCount || 6;
    }

    set make (make) {
        super.make = make
    }

    get make() {
        return `The make of the guitar is: ${super.make}`;
    }

    get stringCount() {
        return this._stringCount;
    }

    set stringCount(stringCount) {
        this._stringCount = stringCount;
    }
}

const guitar = new Guitar('Fender', 12);
console.log(guitar.stringCount); // 12 
```

Enter fullscreen mode Exit fullscreen mode

## ES6 模块

ES6 模块使用`import`和`export`关键字，旨在将
用于浏览器或类似`NodeJs`
的服务器环境

```
// utils.js
export function add(left = 0, right = 0) {
    return left + right;    
};

export function times(left = 0, right = 0) {
    return left * right;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以导入我们的 utils 文件了。有几种方法可以导入。

```
// index.js
import * as utils from './utils.js'
// utils.add(), utils.times()

import { add, times } from './utils.js'
// add(), times() 
```

Enter fullscreen mode Exit fullscreen mode

您也可以导出变量或对象。

```
// my-module.js

const myVariable = 100;

const person = {
    name: 'Bill',
    age: 42
};

function trim(string = '') {
    return typeof string === 'string' && string.trim();
};

export { myVariable, person, trim };

// index.js
import { myVariable as maxAge, person, trim } from './my-module.js';

console.log(maxAge, person.age); // 100, 42

trim(' test '); // 'test' 
```

Enter fullscreen mode Exit fullscreen mode

有两种不同类型的导出，`named`和`default`。在一个模块中可以有
多个`named`导出，但只能有一个`default`导出。以上
的例子都是从`named`导出的，下面我们来看看`default`
的导出语法。

```
// a default funtion
export default function() {[...]}
export default function myFunc() {[...]}

// a default class
export default class MyClass {[...]} 
```

Enter fullscreen mode Exit fullscreen mode

你也可以有一个变量作为默认导出

```
// other-module.js
const mySuperLongNamedVariable = 100;
export default mySuperLongNamedVariable; 
```

Enter fullscreen mode Exit fullscreen mode

导入默认值时，您可以不使用`* as`关键字来命名它们。

```
// index.js
import theVariable from './other-module.js'
console.log(theVariable); // 100 
```

Enter fullscreen mode Exit fullscreen mode

## ES6 承诺

> 承诺是一个对象，表示一个
> 异步操作的最终完成或失败。
> 
> *   [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)

### 承诺工作

承诺是一种组织
程序操作顺序的便捷方式，并提供了一种替代方法来传递回调函数参数。
假设我们有一个函数`callToDb`，它调用数据库并返回一个
承诺

```
function success(result) {
    // do something with result
}

function failed(error) {
    // do something with error
}

callToDb('table_name').then(success, failed); 
```

Enter fullscreen mode Exit fullscreen mode

只有当返回一个`Error`时，才会调用`failed`。这两个参数都是
可选的，然而要使用前面承诺的结果，你至少需要
一个带有一个参数
的成功函数

```
 callToDb('table_name')
    .then(response => {
        // do something with response
    })
    .catch(error => {
        // do something with error
    }); 
```

Enter fullscreen mode Exit fullscreen mode

和上面的`failed`函数一样，`catch`只有在`Error`被
返回时才会被调用。`then`返回一个承诺，意味着我们现在可以创建一个承诺`chain`。

```
 callToDb('table_name')
    .then(response => {
        // do something with response
        response.changesMade = true;
        return response;
    })
    .then(response => {
        // do more work
    })
    .catch(error => {
        // do something with error
    }); 
```

Enter fullscreen mode Exit fullscreen mode

只要你需要，链子就可以有多长。`catch`也可以在一个承诺链中多次使用
，链中的下一个`catch`在`Error`的返回
时被调用，后面的`then`仍将被调用。

```
 callToDb('table_name')
    .then(response => {
        // do something with response
        response.changesMade = true;
        return response;
    })
    .then(response => {
        // do more work
    })
    .catch(error => {
        // only called for above thens
    })
    .then(response => {
        // do more work
        // will still happen after the catch, even if catch is called
    })
    .catch(error => {
        // do something with error
        // only called for the one above then if an Error is returned
    }); 
```

Enter fullscreen mode Exit fullscreen mode

### 创造承诺

promise 构造函数应该只用于包装不支持 promise 的函数。大多数库都内置了对 promises 的支持，
使你可以在没有 promise
构造函数的情况下，立即开始链接`then`。

promise 构造函数接受一个带有两个参数的函数:
`resolve`和`reject`。让我们创建`callToDb`，一个没有承诺支持的
函数的包装函数。

```
 function callToDb(table_name) {
    return new Promise((resolve, reject) => {
        return db_orm(`select * from ${table_name}`, (err, res) => {
            if(err) {
                reject(err);
            } else {
                resolve(res);
            }
        })
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了一些事情:

*   `db_orm`是我们的数据库库没有承诺支持，它需要回调
*   包装`db_orm`是我们的返回`Promise`，它与`resolve`和`reject`具有我们的执行器功能
*   一旦`db_orm`处于我们因错误而拒绝的回调中，这将触发`catch`或
*   我们用我们的结果`resolve`，这将触发下一个`then`

### 拒绝

Reject 返回用`reason`拒绝的承诺。为了便于调试
，建议将`reason`设为`instance of Error`

```
Promise.reject(new Error('My custom message'))
    .then(result => {
        // not called
    })
    .catch(result => {
        console.log(result); // Error: My custom message
    }) 
```

Enter fullscreen mode Exit fullscreen mode

要拒绝一个`then`链中的承诺，你可以返回一个`new Error`或
并抛出一个`Error`。

### 解决

Resolve 返回用`result`解决的承诺。`result`也可以
成为另一个`promise`，`thenable`或者值。

```
Promise.resolve('Sweet!')
    .then(result => {
        console.log(res); // Sweet!
    })
    .catch(result => {
        // not called
    }); 
```

Enter fullscreen mode Exit fullscreen mode

感谢汉娜和乔纳森帮助校对，并感谢你阅读。

希望这有帮助！

编辑:编码块

干杯。