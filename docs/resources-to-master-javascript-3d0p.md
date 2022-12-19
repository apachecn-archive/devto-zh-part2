# 掌握 Javascript 的资源

> 原文：<https://dev.to/deen_john/resources-to-master-javascript-3d0p>

# 精通 Javascript

[Fork，StarThe GITHub 链接](https://github.com/deenjohn/FullStack-Learning-Resources/edit/master/Javascript.md)

### 基础 Javascript 阅读:

[http://dmitrysoshnikov.com/category/ecmascript/](http://dmitrysoshnikov.com/category/ecmascript/)

*   **MDN**
    T3】https://developer.mozilla.org/bm/docs/Web/JavaScript)
    [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference)

*   **表达式和运算符**
    [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Guide/Expressions _ and _ Operators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators)

[http://exploring JS . com/心急-JS/ch _ syntax . html # statement-vs . expression](http://exploringjs.com/impatient-js/ch_syntax.html#statement-vs.expression)
[https://github . com/getify/You-Dont-Know-JS/blob/master/up % 20% 26% 20g oing/ch1 . MD](https://github.com/getify/You-Dont-Know-JS/blob/master/up%20%26%20going/ch1.md)
[https://stack overflow . com/questions/12703214/JavaScript-difference-between-a-statement-and-an-expression](https://stackoverflow.com/questions/12703214/javascript-difference-between-a-statement-and-an-expression)

*   **值&类型**

[http://speakingjs.com/es5/ch08.html](http://speakingjs.com/es5/ch08.html)
[https://github . com/getify/You-don-Know-JS/blob/master/up % 20% 26% 20g oing/ch1 . MD # values-types](https://github.com/getify/You-Dont-Know-JS/blob/master/up%20%26%20going/ch1.md#values--types)
[https://github . com/getify/You-don-Know-JS/blob/master/up % 20% 26% 20g oing/CH2 . MD](https://github.com/getify/You-Dont-Know-JS/blob/master/up%20%26%20going/ch2.md)

原始值对对象
[http://speakingjs.com/es5/ch01.html#basic_prim_vs_obj](http://speakingjs.com/es5/ch01.html#basic_prim_vs_obj)

手动转换为布尔值
有三种方法可以将任何值转换为布尔值:

```
1)Boolean(value)

(Invoked as a function, not as a constructor)

2)value ? true : false

3)!!value
https://stackoverflow.com/questions/10467475/double-negation-in-javascript-what-is-the-purpose
var x = "somevalue"
var isNotEmpty = !!x.length;
Let's break it to pieces:

x.length   // 9
!x.length  // false
!!x.length // true
So it's used convert a "truethy" \"falsy" value to a boolean. 
```

Enter fullscreen mode Exit fullscreen mode

单个“not”转换为取反的布尔值；对非负转换使用两次。

```
var a = "0";
var b = [];
var c = {};

var d = "";
var e = 0;
var f = null;
var g;

!!a;    // true
!!b;    // true
!!c;    // true

!!d;    // false
!!e;    // false
!!f;    // false
!!g;    // false 
```

Enter fullscreen mode Exit fullscreen mode

*   **真值**
    T3】https://developer.mozilla.org/en-US/docs/Glossary/TruthyT5】T6】http://speakingjs.com/es5/ch01.html#basic_prim_vs_obj

*   **如何在 JavaScript 中检查“未定义”？**

[https://stack overflow . com/questions/9959727/proto-vs-prototype-in-JavaScript](https://stackoverflow.com/questions/9959727/proto-vs-prototype-in-javascript)

[https://stack overflow . com/questions/27509/detecting-an-undefined-object-property](https://stackoverflow.com/questions/27509/detecting-an-undefined-object-property)

*   **JavaScript 数据类型和数据结构**
    [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Data _ structures](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures)

*   **parse int()和 Number()有什么区别？**
    [https://stack overflow . com/questions/4090518/parse int-and-number 的区别是什么](https://stackoverflow.com/questions/4090518/what-is-the-difference-between-parseint-and-number)

嗯，它们在语义上是不同的，作为函数调用
的数字构造函数执行类型转换，parseInt 执行解析。

但是在现实生活中，我们经常使用单位值，比如 CSS 中的“100px”或“12pt”。此外，在许多国家，货币符号跟在金额后面，因此我们有“19 ”,并希望从中提取一个数值。

这就是 parseInt 和 parseFloat 的用途。

他们从字符串中“读出”一个数字，直到读不出来为止。如果出现错误，将返回收集的号码。
函数 parseInt 返回一个整数，而 parseFloat 将返回一个浮点数:

```
 alert( parseInt('100px') ); // 100
alert( parseFloat('12.5em') ); // 12.5

alert( Number(" 123 ") ); // 123
alert( Number("123z") );      // NaN (error reading a number at "z")
alert( Number(true) );        // 1
alert( Number(false) );       // 0 
```

Enter fullscreen mode Exit fullscreen mode

*   **掌握 JavaScript 面试:什么是函数合成？**
    [https://medium . com/JavaScript-scene/master-the-JavaScript-interview-what-is-function-composition-20 DFB 109 a1a 0 # . I 84 zm 53 FB](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-function-composition-20dfb109a1a0#.i84zm53fb)

*   **在 JavaScript 中如何检查一个数字是 NaN？**
    [https://stack overflow . com/questions/2652319/how-do-you-check-a-number-is-nan-in-JavaScript](https://stackoverflow.com/questions/2652319/how-do-you-check-that-a-number-is-nan-in-javascript)

[http://speakingjs.com/es5/ch11.html#isNaN](http://speakingjs.com/es5/ch11.html#isNaN)最佳

[https://stack overflow . com/questions/2652319/how-do-you-check-a-number-is-nan-in-JavaScript https://stack overflow . com/questions/8965364/comparing-nan-values-for-equality-in-JavaScript](https://stackoverflow.com/questions/2652319/how-do-you-check-that-a-number-is-nan-in-javascripthttps://stackoverflow.com/questions/825402/why-does-isnan-equal-falsehttps://stackoverflow.com/questions/8965364/comparing-nan-values-for-equality-in-javascript)
[https://stack overflow . com/questions/8965364](https://stackoverflow.com/questions/6736476/how-to-turn-nan-from-parseint-into-0-for-an-empty-string)

我推荐:typeof value === 'number' &&！isNaN(值)。因为 NaN 的类型=== 'number '。

NaN 是唯一不等于自身的值:

```
 > NaN === NaN
false 
```

Enter fullscreen mode Exit fullscreen mode

所以我们可以使用如果要检查一个值是否是 NaN，就必须使用全局函数 isNaN():

```
> isNaN(NaN)
true
> isNaN(33)
false 
```

Enter fullscreen mode Exit fullscreen mode

但是:
isNaN 不能正确处理非数字，因为它首先将这些数字转换成数字。该转换会产生 NaN，然后函数错误地返回 true:

```
 > isNaN('xyz')
true 
```

Enter fullscreen mode Exit fullscreen mode

因此，最好将 isNaN 与类型检查结合起来:

```
function myIsNaN(value) {
    return typeof value === 'number' && isNaN(value);
} 
```

Enter fullscreen mode Exit fullscreen mode

或者，您可以检查该值是否与其自身不相等(因为 NaN 是唯一具有该特征的值)。但是这不太容易理解:

```
function myIsNaN(value) {
    return value !== value;
} 
```

Enter fullscreen mode Exit fullscreen mode

*   **是数字。IsNaN()比 IsNaN()**[https://stack overflow . com/questions/25176459/is-number-IsNaN-more-broken-than-IsNaN](https://stackoverflow.com/questions/25176459/is-number-isnan-more-broken-than-isnan)

isNaN()和 Number.isNaN()都测试值是否为 NaN 值(或者，在 isNaN()的情况下，可以转换为表示 NaN 值的数字类型值)。换句话说，“NaN”不仅仅意味着“该值不是一个数字”，它还特别意味着“根据 IEEE-754，该值是一个数值而不是一个数字值”。

上述所有测试返回 false 的原因是，所有给定值都可以转换成一个非 NaN:
的数值

```
Number('')    // 0
Number('  ') // 0
Number(true)  // 1
Number(false) // 0
Number([0])   // 0 
```

Enter fullscreen mode Exit fullscreen mode

isNaN()被“破坏”的原因是，从表面上看，类型转换不应该在测试值时发生。这就是问题编号. isNaN()旨在解决的问题。特别是，如果值是数字类型的值，Number.isNaN()将只尝试将该值与 NaN 进行比较。任何其他类型都将返回 false，即使它们实际上“不是数字”，因为值 NaN 的类型是数字。请参见 isNaN()和 Number.isNaN()各自的 MDN 文档。

如果您只想确定某个值是否属于数字类型，即使该值是 NaN，也可以使用 typeof:

type of ' raw RRR ' = = = ' number '//false

*   **null vs undefined**[https://basarat . git books . io/typescript/docs/JavaScript/null-undefined . html](https://basarat.gitbooks.io/typescript/docs/javascript/null-undefined.html)[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Equality _ comparisons _ and _ same ness # Loose _ Equality _ using](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness#Loose_equality_using)

有些东西还没有初始化:未定义。某些内容目前不可用:null。

*   为什么 null 是一个对象，null 和 undefined 有什么区别？
    [https://stack overflow . com/questions/801032/why-is-null-an-object-and-what-the-difference-in-null-undefined](https://stackoverflow.com/questions/801032/why-is-null-an-object-and-whats-the-difference-between-null-and-undefined))
    [https://stack overflow . com/questions/5076944/what-is-the-difference-in-null-undefined-in-JavaScript？no redirect = 1&LQ = 1](https://stackoverflow.com/questions/5076944/what-is-the-difference-between-null-and-undefined-in-javascript?noredirect=1&lq=1))
    [https://github . com/getify/You-Dont-Know-JS/blob/master/types % 20% 26% 20g rammar/ch1 . MD](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch1.md))
    [https://stack overflow . com/questions/2559318/how-to-check-for-an-undefined-or-null-variable-in-JavaScript](https://stackoverflow.com/questions/2559318/how-to-check-for-an-undefined-or-null-variable-in-javascript)no redirect = 1&LQ = 1
    [https://stack overflow . com/questions/4186906/check-if-object-exists-in-JavaScript](https://stackoverflow.com/questions/4186906/check-if-object-exists-in-javascript)
    [https://stack overflow . com/questions/16672743/JavaScript-null-check](https://stackoverflow.com/questions/16672743/javascript-null-check)
    [https://stack overflow . com/questions/5716973](https://stackoverflow.com/questions/5716976/javascript-undefined-vs-void-0)

*   **Test null**
    [https://github . com/getify/You-don-Know-JS/blob/master/types % 20% 26% 20g rammar/ch1 . MD](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch1.md)

如果您想使用 null 值的类型来测试它，您需要一个复合条件:

```
var a = null;

(!a && typeof a === "object"); // true

When you’re trying to identify null, use triple equals so that you can correctly identify the type.
ex : console.log(undefined == null); // true
console.log(undefined === null); // false 
```

Enter fullscreen mode Exit fullscreen mode

null 是唯一的原始值，它是“falsy”(也称为 false-like；见第 4 章)但也从检查类型返回“对象”。

陷阱:typeof null
不幸的是，typeof null 是‘object’。这被认为是一个错误(null 不是内部类型对象的成员)，但它不能被修复，因为这样做会破坏现有的代码。因此，您必须小心 null。例如，以下函数检查 value 是否为对象:

函数 is object(value){
return(value！= = null
&&(type of value = = = ' object '
| | type of value = = = ' function ')；

*   **在原型中使用空值**使用空原型来防止原型污染

```
 function C() { }
C.prototype = null;
But instantiating this constructor still results in instances of Object:
var o = new C();
Object.getPrototypeOf(o) === null; // false
Object.getPrototypeOf(o) === Object.prototype; // true 
```

Enter fullscreen mode Exit fullscreen mode

*   **空 vs 零**
    [https://javascript.info/comparison#strange-result-null-vs-0](https://javascript.info/comparison#strange-result-null-vs-0)

*   **https://javascript.info/type-conversions#toboolean**
    T3

*   **parseInt 和 parseFloat**

使用加号+或数字()的数值转换是严格的。如果值不完全是数字，则失败:
alert(+“100px”)；//楠

有些情况下 parseInt/parseFloat 会返回 NaN。当无法读取数字时，就会发生这种情况:

alert(parse int(' a123 ')；// NaN，第一个符号停止进程

*   **二元逻辑运算符**[http://speakingjs.com/es5/ch01.html#basic_prim_vs_obj](http://speakingjs.com/es5/ch01.html#basic_prim_vs_obj)[https://github . com/getify/You-Dont-Know-JS/blob/master/types % 20% 26% 20g rammar/CH4 . MD #运算符- and-](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch4.md#operators--and-)

And (&&)
如果第一个操作数是 falsy，则返回。否则，返回第二个操作数:

```
> NaN && 'abc'
NaN
> 123 && 'abc'
'abc'
Or (||)
If the first operand is truthy, return it. Otherwise, return the second operand:

> 'abc' || 123
'abc'
> '' || 123
123

var a = 42;
var b = "abc";
var c = null;

a || b;     // 42
a && b;     // "abc"

c || b;     // "abc"
c && b;     // null

a || b;
// roughly equivalent to:
a ? a : b;

a && b;
// roughly equivalent to:
a ? b : a; 
```

Enter fullscreen mode Exit fullscreen mode

*   **操作员**【https://javascript.info/operators】T2

[https://github . com/getify/You-Dont-Know-JS/blob/master/types % 20% 26% 20g rammar/CH2 . MD #整数测试](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch2.md#testing-for-integers)
42.3 % 1 是十进制的，但 42 % 1 是 0

```
Number.isInteger( 42 );     // true
Number.isInteger( 42.000 ); // true
Number.isInteger( 42.3 );   // false

this behaviour can be use in :  
To polyfill Number.isInteger(..) for pre-ES6:

if (!Number.isInteger) {
    Number.isInteger = function(num) {
        return typeof num == "number" && num % 1 == 0;
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

*   * *字符串串联，二进制+**
    T3】https://JavaScript . info/operators #字符串串联-二进制

*   **第二章。可变范围**

来自《有效的 JavaScript:利用 JavaScript 力量的 68 种具体方法》一书

这一章教会了我一些关于闭包和作用域的隐藏的东西。

[https://www . safari books online . com/library/view/effective-JavaScript-68/9780132902281/ch02 . html # ch 02 lev 1 sec 4](https://www.safaribooksonline.com/library/view/effective-javascript-68/9780132902281/ch02.html#ch02lev1sec4)

[https://stack overflow . com/questions/111102/how-do-JavaScript-closures-work](https://stackoverflow.com/questions/111102/how-do-javascript-closures-work)
[https://stack overflow . com/questions/750486/JavaScript-closure-inside-loops-simple-practical-example](https://stackoverflow.com/questions/750486/javascript-closure-inside-loops-simple-practical-example)
[https://stack overflow . com/questions/592396/what-is-the-the-the-the-purpose-of-a-self-executing-function-in-Java](https://stackoverflow.com/questions/592396/what-is-the-purpose-of-a-self-executing-function-in-javascript)

## 在 vs 中为...在

for-in 语句本身并不是一个“坏习惯”，但是它可能会被误用，
例如，迭代数组或类似数组的对象。

for-in 语句的目的是枚举对象属性。该语句将在原型链中上升，同时枚举继承的属性，这有时是不希望的。

为..in 不能保证保持元素顺序

[https://github . com/getify/You-Dont-Know-JS/blob/master/this % 20% 26% 20 object % 20 prototypes/ch5 . MD](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch5.md)

[https://www.w3schools.com/jsref/jsref_forin.asp](https://www.w3schools.com/jsref/jsref_forin.asp)
[https://stack overflow . com/questions/500504/why-is-use-for-in-with-array-iteration-a-bad-idea](https://stackoverflow.com/questions/500504/why-is-using-for-in-with-array-iteration-a-bad-idea)

[http://adripofjavascript . com/blog/drops/the-problem-with-for-in-and-JavaScript-arrays . html](http://adripofjavascript.com/blog/drips/the-problem-with-for-in-and-javascript-arrays.html)

## 威压

*   **相等比较和相同**

强迫

[http://speakingjs.com/es5/ch09.html](http://speakingjs.com/es5/ch09.html)
[http://speakingjs.com/es5/ch10.html](http://speakingjs.com/es5/ch10.html)
[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Equality _ comparisons _ and _ same ness](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness))
[http://ecma-international.org/ecma-262/5.1/#sec-11.9.3](http://ecma-international.org/ecma-262/5.1/#sec-11.9.3))
[http://getify.github.io/coercions-grid/](http://getify.github.io/coercions-grid/)

[http://speakingjs.com/es5/ch09.html](http://speakingjs.com/es5/ch09.html)

```
 if (x == 123) ... 
```

Enter fullscreen mode Exit fullscreen mode

前面的代码检查 x 是 123 还是“123”。同样，这是非常紧凑的，同样，最好是显式的:

```
if (Number(x) === 123) 
```

Enter fullscreen mode Exit fullscreen mode

用例:将包装器实例与原语进行比较
宽松等于允许您将原语与包装的原语进行比较:

```
 > 'abc' == new String('abc')
true 
```

Enter fullscreen mode Exit fullscreen mode

反对这种方法有三个原因。首先，宽松的相等在包装的原语之间不起作用:

```
> new String('abc') == new String('abc')
false
Second, you should avoid wrappers anyway. 
Third, if you do use them, it is better to be explicit:

if (wrapped.valueOf() === 'abc') 
```

Enter fullscreen mode Exit fullscreen mode

[https://github . com/getify/You-Dont-Know-JS/blob/master/types % 20% 26% 20g rammar/CH4 . MD #比较字符串与数字](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch4.md#comparing-strings-to-numbers)

```
var a = "42";

// bad (will fail!):
if (a == true) {
    // ..
}

// also bad (will fail!):
if (a === true) {
    // ..
}

// good enough (works implicitly):
if (a) {
    // ..
}

// better (works explicitly):
if (!!a) {
    // ..
}

// also great (works explicitly):
if (Boolean( a )) {
    // ..
} 
```

Enter fullscreen mode Exit fullscreen mode

 *[https://javascript.info/number](https://javascript.info/number)*

 ****这个 experssion 是怎么评价的？x<y*T3】*
算法([http://speakingjs.com/es5/ch08.html#toprimitive](http://speakingjs.com/es5/ch08.html#toprimitive))
T8】https://javascript.info/object-toprimitive**

你评个比较:

```
x < y 
```

Enter fullscreen mode Exit fullscreen mode

通过采取以下步骤:

确保两个操作数都是基元。对象 obj 通过内部操作 ToPrimitive(obj，Number)转换为原语(参考算法:top Primitive()—将值转换为原语)，该操作调用 obj.valueOf()并可能调用 obj.toString()来完成。
如果两个操作数都是字符串，那么通过字典上比较代表字符串的 JavaScript 字符的 16 位代码单元(见第 24 章)来比较它们。
否则，将两个操作数都转换为数字，并进行数值比较。
其他排序操作符的处理方式类似

*   **第四章:胁迫**
    [https://github . com/getify/You-don-Know-JS/blob/master/types % 20% 26% 20g rammar/CH4 . MD](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch4.md))
    [https://stack overflow . com/questions/154059/how-to-check-empty-undefined-null-string-in-JavaScript](https://stackoverflow.com/questions/154059/how-to-check-empty-undefined-null-string-in-javascript)

*   **第三项:谨防隐性胁迫**
    [https://www . safari books online . com/library/view/effective-JavaScript-68/9780132902281/ch01 . html # ch 01 lev 1 sec 3](https://www.safaribooksonline.com/library/view/effective-javascript-68/9780132902281/ch01.html#ch01lev1sec3)

如果强制为数字，null 将变为 0:

> Number(null)
> 0
> 5 + null
> 5
> 
> 数量(未定义)
> 楠
> 5 +未定义
> 楠
> 
> 3 +'次'
> '3 次'
> 
> 字符串(null)
> ‘null’
> 字符串(123.45)
> ‘123.45’
> 字符串(false)
> ‘false’
> 
> 数量({})
> 楠..为什么？算法:top Primitive()—将值转换为原语。[http://speakingjs.com/es5/ch08.html](http://speakingjs.com/es5/ch08.html)

### 类型 of vs 实例 of vs {}。toString

[https://javascript.info/instanceof](https://javascript.info/instanceof)
[https://stack overflow . com/questions/899574/哪个是](https://stackoverflow.com/questions/899574/which-is-best-to-use-typeof-or-instanceof)最好用的实例类型

* ***typeof:返回描述值的“类型”的字符串。*** *

```
> typeof true
'boolean'
> typeof 'abc'
'string'
> typeof {} // empty object literal
'object'
> typeof [] // empty array literal
'object'

typeof undefined
'undefined'

typeof null
'object'

var a = null;

(!a && typeof a === "object"); // true
null is the only primitive value that is "falsy" 
but that also returns "object" from the typeof check. 
```

Enter fullscreen mode Exit fullscreen mode

实际使用:Jquery 中的
[https://code.jquery.com/jquery-3.3.1.js](https://code.jquery.com/jquery-3.3.1.js)T3】

```
function isArrayLike( obj ) {

    // Support: real iOS 8.2 only (not reproducible in simulator)
    // `in` check used to prevent JIT error (gh-2145)
    // hasOwn isn't used here due to false negatives
    // regarding Nodelist length in IE
    var length = !!obj && "length" in obj && obj.length,
        type = toType( obj );

    if ( isFunction( obj ) || isWindow( obj ) ) {
        return false;
    }

    return type === "array" || length === 0 ||
        typeof length === "number" && length > 0 && ( length - 1 ) in obj;
} 
```

Enter fullscreen mode Exit fullscreen mode

*   **的实例**

[http://perfection kills . com/instance of-how-to-write-a-robust-is array/](http://perfectionkills.com/instanceof-considered-harmful-or-how-to-write-a-robust-isarray/)

[https://javascript.info/instanceof](https://javascript.info/instanceof)

instanceof 操作符主要检查左边对象的原型链中的任何东西是否与右边对象的原型属性所引用的对象是同一个对象。

var arr =[]；
arr 数组的实例；// true
该语句返回`true`，因为 Array.prototype(是右手对象的原型属性)
引用了与左手对象的内部[[Prototype]]相同的对象([[Prototype]]
通过 arr“可见”)。 **proto** 在具有 **proto** 扩展的客户端中)。
我前面提到的另一种构造函数检查通常如下所示:

var arr =[]；
arr . constructor = = Array；//真

instanceof 运算符测试对象的原型链中是否存在 constructor.prototype，即 Array.prototype。

Foo 的一个实例；//真

instanceof 运算符将一个普通对象作为其左侧操作数，将一个函数作为其右侧操作数。
回答的问题实例是:在 a 的整个[[Prototype]]即 **proto** 链中，是否出现过 Foo.prototype 任意指向的对象？

[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Operators/instance of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/instanceof)

[http://speakingjs.com/es5/ch01.html#basic_prim_vs_obj](http://speakingjs.com/es5/ch01.html#basic_prim_vs_obj)
value instance of Constr
如果 value 是由构造函数 Constr 创建的对象，则返回 true(参见构造函数:对象工厂)。下面是一些例子:

```
> var b = new Bar();  // object created by constructor Bar
> b instanceof Bar
true

> {} instanceof Object
true
> [] instanceof Array
true
> [] instanceof Object  // Array is a subconstructor of Object
true

> undefined instanceof Object
false
> null instanceof Object
false 
```

Enter fullscreen mode Exit fullscreen mode

**实例的问题**

案例 1 :

```
function Rabbit() {}
let rabbit = new Rabbit();

// changed the prototype
Rabbit.prototype = {};
rabbit instanceof Rabbit //false 
```

Enter fullscreen mode Exit fullscreen mode

这也是避免改变原型的原因之一。只是为了安全起见。

案例 2 :
在多框架 DOM 环境中编写脚本时会出现问题。简而言之，在一个 iframe 中创建的数组对象不与在另一个 iframe 中创建的数组共享[[Prototype]]。它们的构造函数是不同的对象，因此 instanceof 和构造函数检查都失败:

```
var iframe = document.createElement('iframe');
document.body.appendChild(iframe);
xArray = window.frames[window.frames.length-1].Array;
var arr = new xArray(1,2,3); // [1,2,3]

// Boom!
arr instanceof Array; // false

// Boom!
arr.constructor === Array; // false 
```

Enter fullscreen mode Exit fullscreen mode

*   **{}。toString**[https://github . com/getify/You-don-Know-JS/blob/master/types % 20% 26% 20g rammar/CH3 . MD # internal-class](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch3.md#internal-class)

object toString for type
//为了方便起见，将 toString 方法复制到一个变量中

```
 let objectToString = Object.prototype.toString;

// what type is this?
let arr = [];

alert( objectToString.call(arr) ); // [object Array]

Object.prototype.toString.call( [1,2,3] );
Object.prototype.toString.call( null );         // "[object Null]"
Object.prototype.toString.call( undefined );    // "[object Undefined]" 
```

Enter fullscreen mode Exit fullscreen mode

对于数字，它将是[对象编号]
对于布尔型，它将是[对象布尔型]
对于 Null:[对象 Null]
对于 Undefined:[对象未定义]
对于数组:[对象数组]
…等等(可定制)。

我们可以看到，{}。toString 在技术上是一种“更高级”的。

## 功能

#### 人生

*   **使用 JavaScript 中的函数控制变量范围**

[http://speakingjs.com/es5/ch01.html#basic_prim_vs_obj](http://speakingjs.com/es5/ch01.html#basic_prim_vs_obj)
T3】http://speakingjs.com/es5/ch16.html#iifeT5】

```
function f() {
    if (condition) {
        var tmp = ...;
        ...
    }
    // tmp still exists here
    // => not what we want
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您想为 then 块引入一个新的范围，您可以定义一个函数并立即调用它。
这是一种变通方法，模拟块范围:

```
function f() {
    if (condition) {
        (function () {  // open block
            var tmp = ...;
            ...
        }());  // close block
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

IIFE Variation:带有参数的 life
你可以使用参数来定义 life 内部的变量:

```
var x = 23;
(function (twice) {
    console.log(twice);
}(x * 2));
This is similar to:

var x = 23;
(function () {
    var twice = x * 2;
    console.log(twice);
}()); 
```

Enter fullscreen mode Exit fullscreen mode

生活用例:通过闭包无意中共享
闭包保持它们与外部变量的连接，这有时不是你想要的:

```
var result = [];
for (var i=0; i < 5; i++) {
    result.push(function () { return i });  // (1)
}
console.log(result[1]()); // 5 (not 1)
console.log(result[3]()); // 5 (not 3)
The value returned in line (1) is always the current value of i, not the value it had when the function was created. After the loop is finished, i has the value 5, which is why all functions in the array return that value. If you want the function in line (1) to receive a snapshot of the current value of i, you can use an IIFE:

for (var i=0; i < 5; i++) {
    (function () {
        var i2 = i; // copy current i
        result.push(function () { return i2 });
    }());
} 
```

Enter fullscreen mode Exit fullscreen mode

你怎么纠正这个功能？

```
 var myAlerts = [];

for (var i = 0; i < 5; i++) {
    myAlerts.push(
        function inner() {
            alert(i);
        }
    );
}

myAlerts[0](); // 5
myAlerts[1](); // 5
myAlerts[2](); // 5
myAlerts[3](); // 5
myAlerts[4](); // 5

Answer :

var myAlerts = [];

for (var i = 0; i < 5; i++) {
    myAlerts.push(
       (function iife(){
         var j = i ;
         return function inner() {
            console.log(j);
          }
       })()

    );
}
myAlerts[1]() 
```

Enter fullscreen mode Exit fullscreen mode

[https://content . my Emma . com/Emma tech/using-iifes-in-JavaScript-to-control-variable-scope](https://content.myemma.com/emmatech/using-iifes-in-javascript-to-control-variable-scope))
[https://stack overflow . com/questions/2421911/what-of-the-the-pursing-what-the-the-pursing-what-the-the-the-the-pursing-JavaScript-files-in-anonymous-functions-Li](https://stackoverflow.com/questions/2421911/what-is-the-purpose-of-wrapping-whole-javascript-files-in-anonymous-functions-li)
[https://stack overflow . com/questions/13341698/中](https://stackoverflow.com/questions/13341698/javascript-plus-sign-in-front-of-function-name)

*   **函数参数对象**[https://github . com/deen John/Javascript-revision/blob/master/General/3-% 20 function % 20 arguments % 20 object % 20 . MD](https://github.com/deenjohn/Javascript-revision/blob/master/General/3-%20function%20arguments%20object%20.md)

[http://speakingjs.com/es5/ch01.html#basic_prim_vs_obj](http://speakingjs.com/es5/ch01.html#basic_prim_vs_obj)

*   * ***参数过多或过少*** *

```
function f(x, y) {
    console.log(x, y);
    return toArray(arguments);
}
Additional parameters will be ignored (except by arguments):

> f('a', 'b', 'c')
a b
[ 'a', 'b', 'c' ] 
```

Enter fullscreen mode Exit fullscreen mode

*   * ***可选参数*** *以下是为参数分配默认值的常见模式:

```
function pair(x, y) {
    x = x || 0;  // (1)
    y = y || 0;
    return [ x, y ];
}
In line (1), the || operator returns x if it is truthy (not null, undefined, etc.). Otherwise, it returns the second operand:

> pair()
[ 0, 0 ]
> pair(3)
[ 3, 0 ]
> pair(3, 5)
[ 3, 5 ] 
```

Enter fullscreen mode Exit fullscreen mode

*   **函数长度 vs 参数**[https://github . com/deen John/Javascript-revision/blob/master/General/Function % 20 length % 20 vs % 20 arguments](https://github.com/deenjohn/Javascript-revision/blob/master/General/Function%20length%20vs%20arguments)强制 arity 如果您想要强制 Arity(特定数量的参数)，可以检查参数。长度:

```
function pair(x, y) {
    if (arguments.length !== 2) {
        throw new Error('Need exactly 2 arguments');
    }
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

- **方法内部的函数**

```
 var jane = {
    name: 'Jane',
    friends: [ 'Tarzan', 'Cheeta' ],
    logHiToFriends: function () {
        'use strict';
        this.friends.forEach(function (friend) {
            // `this` is undefined here
            console.log(this.name+' says hi to '+friend);
        });
    }
}
Calling logHiToFriends produces an error: why ?

> jane.logHiToFriends()
TypeError: Cannot read property 'name' of undefined

Ans :
 jane.logHiToFriends() runs like 

 var callback = function (friend) {
            // `this` is undefined here
            console.log(this.name+' says hi to '+friend);
        }

 var jane = {
    name: 'Jane',
    friends: [ 'Tarzan', 'Cheeta' ],
    logHiToFriends: function () {
        'use strict';
        this.friends.forEach(callback);
    }
}

as callback funs like callback() i.e without any context , this points to global object i.e window 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看解决这个问题的两种方法。首先，我们可以将它存储在一个不同的变量中:

```
 logHiToFriends: function () {
    'use strict';
    var that = this;
    this.friends.forEach(function (friend) {
        console.log(that.name+' says hi to '+friend);
    });
}
Or, forEach has a second parameter that allows you to provide a value for this:

logHiToFriends: function () {
    'use strict';
    this.friends.forEach(function (friend) {
        console.log(this.name+' says hi to '+friend);
    }, this);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 调用、应用、绑定

[https://www . smashingmagazine . com/2014/01/understanding-JavaScript-function-prototype-bind/](https://www.smashingmagazine.com/2014/01/understanding-javascript-function-prototype-bind/))

[https://github . com/getify/You-Dont-Know-JS/blob/master/this % 20% 26% 20 object % 20 prototypes/CH2 . MD](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch2.md)

*   **我的笔记**[https://github . com/deen John/Javascript-revision/blob/master/General/1-% 20 bind % 20 call % 20 apply % 20-借款%20method.md](https://github.com/deenjohn/Javascript-revision/blob/master/General/1-%20bind%20call%20apply%20-Borrowing%20method.md)

### 调用、应用、绑定-了解更多

*   **[http://davidshariff . com/blog/借款-方法-在-javascript/](http://davidshariff.com/blog/borrowing-methods-in-javascript/)**

var score = function . prototype . call . bind(score calculator . get score)；//绑定调用 score calculator . get score
score(player 1)；

正在做这个

```
scoreCalculator.getScore.call(player1) 
```

Enter fullscreen mode Exit fullscreen mode

您可以从任何地方借用`call`方法，在本例中是从 Function.prototype 借用，但是在哪里将它绑定到 getScore 函数并不重要。

*   **使用 call 来调用带有自定义接收方的方法**
    [https://www . safaribookonline . com/library/view/effective-JavaScript-68/9780132902281/ch03 . html # ch 03 lev 1 sec 3](https://www.safaribooksonline.com/library/view/effective-javascript-68/9780132902281/ch03.html#ch03lev1sec3)

*   **使用 apply 调用不同参数个数的函数**
    [https://www . safari books online . com/library/view/effective-JavaScript-68/9780132902281/ch03 . html # ch 03 lev 1 sec 4](https://www.safaribooksonline.com/library/view/effective-javascript-68/9780132902281/ch03.html#ch03lev1sec4)

*   **使用绑定提取固定接收方的方法**
    [https://www . safaribookonline . com/library/view/effective-JavaScript-68/9780132902281/ch03 . html # ch 03 lev 1 sec 9](https://www.safaribooksonline.com/library/view/effective-javascript-68/9780132902281/ch03.html#ch03lev1sec9))
    [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Function/bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)

*   **function . prototype . call . bind 是如何工作的？**
    [https://stack overflow . com/questions/11121586/how-does-function-prototype-call-bind-work/50874883 # 50874883](https://stackoverflow.com/questions/11121586/how-does-function-prototype-call-bind-work/50874883#50874883)

打电话

通话用途:

```
inArray: function( elem, arr, i ) {
        return arr == null ? -1 : indexOf.call( arr, elem, i );
    } 
```

Enter fullscreen mode Exit fullscreen mode

```
var elems = {
   length: 0,
   add: function(elem){
       Array.prototype.push.call(this, elem);
   },
   gather: function(id){
       this.add(document.getElementById(id));
   }
 };

 elems.gather("first");

 console.log(elems.length == 1 && elems[0].nodeType,
 "Verify that we have an element in our stash");

 elems.gather("second");

 console.log(elems.length == 2 && elems[1].nodeType,
 "Verify the other insertion"); 
```

Enter fullscreen mode Exit fullscreen mode

应用

```
 function smallest(array){
     return Math.min.apply(Math, array);
 }
 function largest(array){
     return Math.max.apply(Math, array);
 }

 assert(smallest([0, 1, 2, 3]) == 0,
 "Located the smallest value.");

 assert(largest([0, 1, 2, 3]) == 3,
 "Located the largest value."); 
```

Enter fullscreen mode Exit fullscreen mode

约束

手动模拟构造函数的 apply()，我们可以分两步模拟 apply()。

步骤 1
通过方法调用传递到目前为止的参数(它们还不在数组中):

```
 new (Date.bind(null, 2011, 11, 24))

     or 
     new (Date.bind.call(Date,null, 2011, 11, 24)) 
```

Enter fullscreen mode Exit fullscreen mode

前面的代码使用 bind()创建一个不带参数的构造函数，并通过 new 调用它。

步骤 2
使用 apply()将数组传递给 bind()。因为 bind()是一个方法调用，所以我们可以使用 apply():

```
new (Function.prototype.bind.apply(
         Date, [null, 2011, 11, 24])) 
```

Enter fullscreen mode Exit fullscreen mode

前面的数组包含 null，后跟 arr 的元素。我们可以使用 concat()通过在 arr:
前添加 null 来创建它

```
var arr = [2011, 11, 24];
new (Function.prototype.bind.apply(
         Date, [null].concat(arr))) 
```

Enter fullscreen mode Exit fullscreen mode

```
 'use strict';
var jane = {
    name: 'Jane',

    describe: function () {
        return 'Person named '+this.name;
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们希望从 jane 中提取 describe 方法，将其放入变量 func 中，并调用它。但是，那不行:

```
> var func = jane.describe;
> func()
TypeError: Cannot read property 'name' of undefined
The solution is to use the method bind() that all functions have. It creates a new function whose this always has the given value:

> var func2 = jane.describe.bind(jane);
> func2()
'Person named Jane' 
```

Enter fullscreen mode Exit fullscreen mode

*   **如何删除功能**

```
var animals = [
  { species: 'Lion', name: 'King' },
  { species: 'Whale', name: 'Fail' }
];

for (var i = 0; i < animals.length; i++) {
  (function(i) {
    this.print = function() {
      console.log('#' + i + '  ' + this.species
                  + ': ' + this.name);
    }
    this.print();
    this.print = null ;
    delete this.print ;
  }).call(animals[i], i);
} 
```

Enter fullscreen mode Exit fullscreen mode

*   **实例失败的地方**

如果你有两个任意的对象，比如说 a 和 b，想要找出对象之间是否通过一个[[Prototype]]链相互关联，单靠 instanceof 是帮不上忙的。

干净多了，接近[[原型]]反射的方法是:

```
 function Foo() {  }

      Foo.prototype = { 
         c :2

       }; // create a new prototype object

      var a1 = new Foo();

      Foo.prototype.isPrototypeOf( a ); // true 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在这种情况下，我们并不真正关心(甚至不需要)Foo，我们只需要一个对象(在我们的例子中，任意标记为 Foo.prototype)来测试另一个对象。问题是 PrototypeOf(..)答案是:在 a 的整个[[Prototype]]链中，Foo.prototype 有没有出现过？

[https://github . com/getify/You-Dont-Know-JS/blob/6 EFD 08 c 9 db 8 e 9808 a 9046204 c 719 c 99 CB 4702 c 18/this % 20% 26% 20 object % 20 prototypes/ch5 . MD # L487](https://github.com/getify/You-Dont-Know-JS/blob/6efd08c9db8e9808a9046204c719c99cb4702c18/this%20%26%20object%20prototypes/ch5.md#L487)

## let，const 箭头功能

[https://github . com/nzakas/understanding ings 6/blob/master/subscript/01-Block-bindings . MD](https://github.com/nzakas/understandinges6/blob/master/manuscript/01-Block-Bindings.md)
T3】https://www.w3schools.com/js/js_arrow_function.asp

## json.stringify

[https://github . com/getify/You-Dont-Know-JS/blob/master/types % 20% 26% 20g rammar/CH4 . MD](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch4.md)

### 严格

[http://speakingjs.com/es5/ch07.html#strict_mode](http://speakingjs.com/es5/ch07.html#strict_mode)
[https://stack overflow . com/questions/1335851/what-does-strict-use-do-in-JavaScript-and-what-the-reasoning-behind-it](https://stackoverflow.com/questions/1335851/what-does-use-strict-do-in-javascript-and-what-is-the-reasoning-behind-it)

## 此在物

第一章:[https://github . com/getify/You-Dont-Know-JS/blob/master/this % 20% 26% 20 object % 20 prototypes/ch1 . MD](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch1.md)T2】第二章:[https://github . com/getify/You-Dont-Know-JS/blob/master/this % 20% 26% 20 object % 20 prototypes/CH2 . MD](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch2.md)

[http://davidshariff.com/blog/javascript-this-keyword/](http://davidshariff.com/blog/javascript-this-keyword/)

有效的 Javascript:第 4 章

它的范围总是由其最近的封闭函数
决定。

```
 function CSVReader(separators) {
    this.separators = separators || [","];
    this.regexp =
    new RegExp(this.separators.map(function(sep) {
      return "\\" + sep[0];
       }).join("|"));
}

CSVReader.prototype.read = function(str) {
    var lines = str.trim().split(/\n/);
    return lines.map(function(line) {
        console.log(this)
       return line.split(this.regexp); // wrong this!
     });
};
var reader = new CSVReader();
reader.read("a,b,c\nd,e,f\n"); 
```

Enter fullscreen mode Exit fullscreen mode

[
[
“a、b、c”
]，
[
“d、e、f”
]
]

```
 function CSVReader(separators) {
    this.separators = separators || [","];
    this.regexp =
    new RegExp(this.separators.map(function(sep) {
      return "\\" + sep[0];
       }).join("|"));
}

CSVReader.prototype.read = function(str) {
    var lines = str.trim().split(/\n/);
    return lines.map(function(line) {
        console.log(this)
       return line.split(this.regexp); // wrong this!
     },this);
};
var reader = new CSVReader();
reader.read("a,b,c\nd,e,f\n"); 
```

Enter fullscreen mode Exit fullscreen mode

【

【a】
【b】
【c】

【d】
【e】
【f】

】

*   **JavaScript 中的借用方法**
    [http://davidshariff . com/blog/Borrowing-Methods-in-JavaScript/# first-article](http://davidshariff.com/blog/borrowing-methods-in-javascript/#first-article)

*   **JavaScript 中的执行上下文&栈是什么？**
    [http://davidshariff . com/blog/what-is-the-execution-context-in-JavaScript/# first-article](http://davidshariff.com/blog/what-is-the-execution-context-in-javascript/#first-article)

*   **标识符解析、执行上下文和范围链**
    [http://jibbering.com/faq/notes/closures/#clIRExSc](http://jibbering.com/faq/notes/closures/#clIRExSc)

### 关闭

*   **JavaScript 作用域链中的标识符解析和闭包**[http://davidshariff . com/blog/JavaScript-Scope-Chain-and-Closures/](http://davidshariff.com/blog/javascript-scope-chain-and-closures/)

注意:
作用域链只适用于函数内部的函数，不适用于对象
内部的函数

```
 var StartStopCounter = {
          counter : 0,
          start : function(){
                StartStopCounter.stopCounter = setInterval(function(){
                    console.log(StartStopCounter.counter);
                    //que : why only counter won't work ?
                    StartStopCounter.counter=StartStopCounter.counter + 1 ;
                },1000);

          },
          stop : function(){
              clearInterval(StartStopCounter.stopCounter);

          }
      } 
```

Enter fullscreen mode Exit fullscreen mode

*   **你不知道 JS: Scope &闭包**

[https://github . com/getify/You-Dont-Know-JS/blob/master/up % 20% 26% 20 going/ch1 . MD # scope](https://github.com/getify/You-Dont-Know-JS/blob/master/up%20%26%20going/ch1.md#scope)
[https://github . com/getify/You-Dont-Know-JS/tree/master/scope % 20% 26% 20 closes](https://github.com/getify/You-Dont-Know-JS/tree/master/scope%20%26%20closures)

[http://javascript.info/closure](http://javascript.info/closure)

*   **[https://www . Amazon . com/Functional-JavaScript-简介-编程-下划线-js-ebook/dp/B00D624AQO](https://www.amazon.com/Functional-JavaScript-Introducing-Programming-Underscore-js-ebook/dp/B00D624AQO)**

有时封装被用来限制某些元素的可见性，这种行为被称为数据隐藏。JavaScript 的对象系统
没有提供直接隐藏数据的方法，所以数据是使用一种叫做闭包的东西来隐藏的。

通过使用包含闭包的函数技术，您可以实现与大多数面向对象语言提供的相同功能一样有效的数据隐藏

[https://stack overflow . com/questions/500431/what-the-scope-of-variables-in-JavaScript](https://stackoverflow.com/questions/500431/what-is-the-scope-of-variables-in-javascript)

*   **闭包实例的实际使用**

例 1:私有变量

```
 function Ninja() {                                            //#1

        var feints = 0;                                             //#2

        this.getFeints = function(){                                //#3
          return feints;                                            //#3
        };                                                          //#3

        this.feint = function(){                                    //#4
          feints++;                                                 //#4
        };                                                          //#4
      }

      var ninja = new Ninja();                                      //#5

      ninja.feint();                                                //#6

      assert(ninja.feints === undefined,                            //#7
          "And the private data is inaccessible to us." );          //#7

      assert(ninja.getFeints() == 1,                                //#8
             "We're able to access the internal feint count." );    //#8 
```

Enter fullscreen mode Exit fullscreen mode

例 2 :

```
 function animateIt(elementId) {

    var elem = document.getElementById(elementId);              //#2
    var tick = 0;                                               //#3

    var timer = setInterval(function(){                         //#4
      if (tick < 100) {
        elem.style.left = elem.style.top = tick + "px";
        tick++;
      }
      else {
        clearInterval(timer);
        assert(tick == 100,                                      //#5
               "Tick accessed via a closure.");
        assert(elem,
               "Element also accessed via a closure.");
        assert(timer,
               "Timer reference also obtained via a closure." );
      }
    }, 10);

  }

  animateIt('box') 
```

Enter fullscreen mode Exit fullscreen mode

例 3:

const createStore =(reducer)= > {
let state；
let listeners =[]；

const getState =()= > state；

const dispatch =(action)= > {
state = reducer(state，action)
listeners . foreach(listener =>listener())；
}；

const subscribe =(listener)= > {
listeners . push(listener)；
return()=>{
listeners = listeners . filter(l =>l！==听者)；
}
}；

调度({ })；//虚拟调度

返回{ getState，dispatch，subscribe }；

};

闭包使用示例:重载函数

```
 var ninjas = {
    values: ["Dean Edwards", "Sam Stephenson", "Alex Russell"]
 };

function addMethod(object, name, fn) {
 var old = object[name];
 object[name] = function(){
   if (fn.length == arguments.length)
      return fn.apply(this, arguments)
   else if (typeof old == 'function')
      return old.apply(this, arguments);
  };
}

 addMethod(ninjas, "find", function(){
          return this.values;
 });

 addMethod(ninjas, "find", function(name){
      var ret = [];
      for (var i = 0; i < this.values.length; i++)
      if (this.values[i].indexOf(name) == 0)
      ret.push(this.values[i]);
      return ret;
 });

 addMethod(ninjas, "find", function(first, last){
      var ret = [];
      for (var i = 0; i < this.values.length; i++)
      if (this.values[i] == (first + "  " + last))
      ret.push(this.values[i]);
      return ret;
 });

 assert(ninjas.find().length == 3,
 "Found all ninjas");
 assert(ninjas.find("Sam").length == 1,
 "Found ninja by first name");
 assert(ninjas.find("Dean", "Edwards").length == 1,
 "Found ninja by first and last name");
 assert(ninjas.find("Alex", "Russell", "Jr") == 
 ,
 "Found nothing"); 
```

Enter fullscreen mode Exit fullscreen mode

示例 2 :
创建一个自包含的作用域

考虑下面的代码片段:

```
 (function(){
 var numClicks = 0;
 document.addEventListener("click", function(){
 alert( ++numClicks );
 }, false);
})();

/*
Because the immediate function is executed immediately (hence its name), the click
handler is also bound right away. The important thing to note is that a closure is created
for the handler that includes numClicks, allowing the numClicks variable to persist
along with the handler, and be referenceable by the handler but nowhere else.
 This is one of the most common ways in which immediate functions are used: as
simple, self-contained wrappers for functionality. The variables needed for the unit of
functionality are trapped in the closure, but they aren’t visible anywhere else.
*/ 
```

Enter fullscreen mode Exit fullscreen mode

## 物体

[http://speakingjs.com/es5/ch17.html](http://speakingjs.com/es5/ch17.html)
T3】https://stack overflow . com/questions/tagged/JavaScript+object

*   **MDN:Object**
    [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)

*   在 JavaScript 中深度克隆一个对象最有效的方法是什么？
    [https://stack overflow . com/questions/122102/what-a-the-most-efficient-way-to-deep-clone-an-object-in-JavaScript？rq=1](https://stackoverflow.com/questions/122102/what-is-the-most-efficient-way-to-deep-clone-an-object-in-javascript?rq=1)

复制一个对象
要创建一个对象的相同副本，你需要做好两件事:

副本必须与原件具有相同的原型(参见第 2 层:对象之间的原型关系)。
副本必须具有与原件相同的属性
以下函数执行此类复制:

函数 copyObject(orig) {
// 1。copy 与 orig
var copy = object . create(object . getprototypeof(orig))具有相同的原型；

```
// 2\. copy has all of orig’s properties
copyOwnPropertiesFrom(copy, orig);

return copy; 
```

Enter fullscreen mode Exit fullscreen mode

}
通过此功能将属性从 orig 复制到 copy:

函数 copyOwnPropertiesFrom(target，source){
object . getownpropertymanames(source)//(1)
。forEach(function(prop key){//(2)
var desc = object . getownpropertydescriptor(source，prop key)；//(3)
object . define property(target，propKey，desc)；//(4)
})；
返回目标；
}；
以下是相关步骤:

获取一个数组，其中包含 source 的所有自身属性的键。迭代那些键。
检索属性描述符。
使用属性描述符在目标中创建一个自己属性

*   **一个 JavaScript 对象的长度**
    T3】https://stack overflow . com/questions/5223/Length-of-a-JavaScript-object？rq=1

*   如何动态合并两个 JavaScript 对象的属性？
    [https://stack overflow . com/questions/171251/how-can-I-merge-properties-of-two-JavaScript-objects-动态？rq=1](https://stackoverflow.com/questions/171251/how-can-i-merge-properties-of-two-javascript-objects-dynamically?rq=1)

*   **检测一个未定义的对象属性**
    [https://stack overflow . com/questions/27509/Detecting-an-undefined-object-property？rq=1](https://stackoverflow.com/questions/27509/detecting-an-undefined-object-property?rq=1)

*   如何从 JavaScript 对象中移除属性？
    [https://stack overflow . com/questions/208105/how-do-I-remove-a-property-from-a-JavaScript-object？rq=1](https://stackoverflow.com/questions/208105/how-do-i-remove-a-property-from-a-javascript-object?rq=1)

*   如何从 JavaScript 对象中移除属性？
    [https://stack overflow . com/questions/208105/how-do-I-remove-a-property-from-a-JavaScript-object](https://stackoverflow.com/questions/208105/how-do-i-remove-a-property-from-a-javascript-object)

*   **检查对象是否为数组？**
    [https://stack overflow . com/questions/4775722/check-if-object-is-array](https://stackoverflow.com/questions/4775722/check-if-object-is-array)

*   如何在 JavaScript 中检查一个对象是否有属性？
    [https://stack overflow . com/questions/135448/how-do-I-check-if-a-object-has-a-property-in-JavaScript](https://stackoverflow.com/questions/135448/how-do-i-check-if-an-object-has-a-property-in-javascript)

*   如何在 JavaScript 中检查一个对象是否有键？
    [https://stack overflow . com/questions/455338/how-do-I-check-if-a-object-has-a-key-in-JavaScript？noredirect=1 & lq=1](https://stackoverflow.com/questions/455338/how-do-i-check-if-an-object-has-a-key-in-javascript?noredirect=1&lq=1)

[https://stack overflow . com/questions/3390396/how-to-check-for-undefined-in-JavaScript](https://stackoverflow.com/questions/3390396/how-to-check-for-undefined-in-javascript)

*   **检查 JavaScript 对象中是否存在关键字？**
    [https://stack overflow . com/questions/1098040/checking-if-a-key-exists-in-a-a-JavaScript-object](https://stackoverflow.com/questions/1098040/checking-if-a-key-exists-in-a-javascript-object)

*   如何从 JavaScript 对象中移除属性？
    [https://stack overflow . com/questions/208105/how-do-I-remove-a-property-from-a-JavaScript-object？noredirect=1 & lq=1](https://stackoverflow.com/questions/208105/how-do-i-remove-a-property-from-a-javascript-object?noredirect=1&lq=1)

如果删除该属性，它的键也会消失:

> delete obj . foo
> true
> object . keys(obj)
> [' bar ']
> 删除只影响一个对象的直接(" own，"非继承的")属性。它的原型没有被触及

*   **如果你只是将一个属性设置为未定义，该属性仍然存在，对象仍然包含它的关键字**

> 是 obj = { foo:a，bar:b ' }：
> 
> obj.foo = undefined
> Object.keys(obj)
> [ 'foo '，' bar' ]

*   **用 JavaScript 解析 JSON？**
    [https://stack overflow . com/questions/4935632/parse-JSON-in-JavaScript](https://stackoverflow.com/questions/4935632/parse-json-in-javascript)

*   **检测一个未定义的对象属性**
    [https://stack overflow . com/questions/27509/Detecting-an-undefined-object-property？rq=1](https://stackoverflow.com/questions/27509/detecting-an-undefined-object-property?rq=1)

*   **JavaScript 中的对象比较**
    [https://stack overflow . com/questions/1068834/Object-comparison-in-JavaScript](https://stackoverflow.com/questions/1068834/object-comparison-in-javascript)

*   **删除 JavaScript 中的对象**
    [https://stack overflow . com/questions/742623/Deleting-Objects-in-JavaScript](https://stackoverflow.com/questions/742623/deleting-objects-in-javascript)

*   **如何获取一个 JavaScript 对象的大小？**
    [https://stack overflow . com/questions/1248302/how-to-get-the-size-a-JavaScript-object](https://stackoverflow.com/questions/1248302/how-to-get-the-size-of-a-javascript-object)

*   **物体属性阴影**

[https://github . com/getify/You-Dont-Know-JS/blob/master/this % 20% 26% 20 object % 20 prototypes/ch5 . MD](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch5.md))

```
 'use strict';

      const anotherObject = {};

      Object.defineProperty(anotherObject, 'a', {
        value: 2,
        writable: false
      });

      var myObject = Object.create( anotherObject );

      myObject.a = 3; 
```

Enter fullscreen mode Exit fullscreen mode

[https://medium . freecodecamp . org/let-explore-objects-in-JavaScript-4a 4 ad 76 af 798](https://medium.freecodecamp.org/lets-explore-objects-in-javascript-4a4ad76af798)

*   **物体到图元的转换**【http://javascript.info/object-toprimitive】

*   **原型**

```
 function SuperType(name){
 this.name = name;
 this.colors = ["red","green"];
}

SuperType.prototype.sayName = function(){
 console.log(this.name);
};

function SubType(name, age){

 //inherit properties
 SuperType.call(this, name);

 this.age = age;
}

//inherit methods
SubType.prototype = new SuperType();  // problem as this creates color property on prototype 

SubType.prototype.sayAge = function(){
 console.log(this.age);
};

var instance1 = new SubType("Nicholas", 29);

problems with this :

console.log(instance1) > color property on prototype 

SubType {name: "Nicholas", colors: Array(2), age: 29}
    age: 29
    colors: (2) ["red", "green"]
    name: "Nicholas"
    __proto__: SuperType
        colors: (2) ["red", "green"]
        name: undefined
        sayAge: ƒ ()
        __proto__: Object 
```

Enter fullscreen mode Exit fullscreen mode

[https://github . com/getify/You-Dont-Know-JS/blob/master/this % 20% 26% 20 object % 20 prototypes/ch5 . MD](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch5.md))
T3】https://stack overflow . com/questions/310870/use-of-prototype-vs-this-in-JavaScript
[https://github . com/getify/You-Dont-Know-JS/blob/master/this](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch6.md)

使用 hasOwnProperty 防止原型污染

```
var dict = {};
"alice" in dict; // false
"bob" in dict; // false
"chris" in dict; // false
"toString" in dict; // true
"valueOf" in dict; // true
Luckily, Object.prototype provides the hasOwnProperty method, which
is just the tool we need to avoid prototype pollution when testing for
dictionary entries:
dict.hasOwnProperty("alice"); // false
dict.hasOwnProperty("toString"); // false
dict.hasOwnProperty("valueOf"); // false 
```

Enter fullscreen mode Exit fullscreen mode

***获取所有属性***

[https://javascript.info/getting-all-properties](https://javascript.info/getting-all-properties)
[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Object/values](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/values)
[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Reference/Global _ Objects/Object/keys](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)
[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Objects/entries](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/entries)

Object.values()方法返回给定对象自己的可枚举属性值的数组，
，其顺序与为提供的顺序相同...in 循环(不同之处在于 for-in 循环也枚举原型链中的
属性)。

object . keys(obj)/object . values(obj)/object . entries(obj)
这些方法只列出可枚举的属性，以及那些以字符串作为键的属性。1)Object.keys(Object.prototype)

[]
object . keys()方法返回一个给定对象自身属性名称的数组，
，其顺序与我们使用普通循环获得的顺序相同。

2)object . getownpropertynames(object . prototype)
–如果我们需要不可枚举的属性，则返回所有自己的字符串属性名称的数组
:

```
(12) ["constructor", "__defineGetter__", "__defineSetter__", "hasOwnProperty", "__lookupGetter__", "__lookupSetter__", "isPrototypeOf", "propertyIsEnumerable", "toString", "valueOf", "__proto__", "toLocaleString"] 
```

Enter fullscreen mode Exit fullscreen mode

3)reflect . ownkeys(object . prototype)
(12)[" constructor "，" **defineGetter** "，" **defineSetter** ，" hasOwnProperty "，" **lookupGetter** "，" **lookupSetter** ，" isPrototypeOf "，" propertyIsEnumerable "，" toString "，" valueOf "，" **proto** ，" toLocaleString"]

[https://stack overflow . com/questions/15283720/why-are-for-not-for-loops-when-loops-looping-through-A-array](https://stackoverflow.com/questions/15283720/why-are-for-in-faster-than-for-loops-when-looping-through-an-array)
如果在数组中找不到属性，for 循环也会在[[Prototype]]链上搜索属性。例如，[0，，2]上的 for 循环将一直搜索 1 到 Object.prototype[[Prototype]]

不要用于..在循环数组中。不是。曾经

**手动模拟构造函数的 apply()**

1)通过方法调用传递到目前为止的参数(它们还不在数组中):

new (Date.bind(null，2011，11，24))

前面的代码使用 bind()创建一个不带参数的构造函数，并通过 new 调用它。

2)
使用 apply()传递一个数组来绑定()。因为 bind()是一个方法调用，我们可以使用 apply():

new(function . prototype . bind . apply(
Date，[null，2011，11，24])
前面的数组包含 null，后面是 arr 的元素。我们可以使用 concat()通过在 arr 前添加 null 来创建它:

var arr = [2011，11，24]；
new(function . prototype . bind . apply(
Date，[null])。concat(arr)))

有效的 Javascript——第 4 章

***使用默认构造函数的问题***

[http://ragan wald . com/2014/07/09/JavaScript-constructor-problem . html](http://raganwald.com/2014/07/09/javascript-constructor-problem.html)

[https://javascript.info/function-prototype](https://javascript.info/function-prototype)T2】

```
 function Rabbit() {}

/* default prototype
Rabbit.prototype = { constructor: Rabbit };
*/ 
```

Enter fullscreen mode Exit fullscreen mode

```
 function Rabbit() {}
// by default:
// Rabbit.prototype = { constructor: Rabbit }

alert( Rabbit.prototype.constructor == Rabbit ); // true 
```

Enter fullscreen mode Exit fullscreen mode

…JavaScript 本身并不能确保正确的“构造函数”值。

是的，它存在于函数的默认“原型”中，但仅此而已。随后会发生什么，完全取决于我们。

特别是，如果我们把默认的原型整体替换掉，那么里面就没有“构造器”了。

例如:

```
function Rabbit() {}
Rabbit.prototype = {
  jumps: true
};

let rabbit = new Rabbit();
alert(rabbit.constructor === Rabbit); // false 
```

Enter fullscreen mode Exit fullscreen mode

因此，为了保留正确的“构造函数”，我们可以选择添加/删除默认“原型”的属性，而不是整体覆盖它:

```
function Rabbit() {} 
```

Enter fullscreen mode Exit fullscreen mode

//不完全覆盖 rabbit . prototype
//只添加到其中

```
Rabbit.prototype.jumps = true 
```

Enter fullscreen mode Exit fullscreen mode

//保留默认的 rabbit . prototype . constructor
，或者手动重新创建构造函数属性:

```
Rabbit.prototype = {
  jumps: true,
  constructor: Rabbit
}; 
```

Enter fullscreen mode Exit fullscreen mode

//现在构造函数也是正确的，因为我们添加了它

```
 function Person(){

    }

    var friend = new Person();

    Person.prototype = {
     name : "Nicholas",
     age : 29,
     job : "Software Engineer",
     sayName : function () {
        console.log(this.name);
       }
    };

Constructor is Object Constructor
(new Person()).constructor
ƒ Object() { [native code] } 
```

Enter fullscreen mode Exit fullscreen mode

## 数组

[https://javascript.info/array-methods](https://javascript.info/array-methods)
T3】http://speakingjs.com/es5/ch18.htmlT5[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
[https://stack overflow . com/questions/tagged/arrays+JavaScript](https://stackoverflow.com/questions/tagged/arrays+javascript)

***拼接***
[https://javascript.info/array-methods#splice](https://javascript.info/array-methods#splice)
[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Array/拼接](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)

```
 array.splice(start[, deleteCount[, item1[, item2[, ...]]]]) //deletecount is no of element to delete

1)
let arr = ["I", "study", "JavaScript"];

arr.splice(1, 1); // from index 1 remove 1 element

alert( arr ); // ["I", "JavaScript"]

2)
let arr = ["I", "study", "JavaScript", "right", "now"];

// remove 3 first elements and replace them with another
arr.splice(0, 3, "Let's", "dance");

alert( arr ) // now ["Let's", "dance", "right", "now"]

3)
let arr = ["I", "study", "JavaScript"];

// from index 2
// delete 0
// then insert "complex" and "language"
arr.splice(2, 0, "complex", "language");

alert( arr ); // "I", "study", "complex", "language", "JavaScript" 
```

Enter fullscreen mode Exit fullscreen mode

*   **删除 JavaScript**
    [中对象数组的重复项 https://stack overflow . com/questions/tagged/JavaScript+arrays](https://stackoverflow.com/questions/tagged/javascript+arrays)

*   **类数组 vs 数组**

[https://github . com/deen John/Javascript-revision/blob/master/General/array like % 20 vs % 20 array . MD](https://github.com/deenjohn/Javascript-revision/blob/master/General/arraylike%20vs%20array.md)

究竟是什么让一个对象“像数组”呢？数组对象的基本契约相当于两个简单的规则。
■它具有范围为 0 的整数长度属性...232 – 1.
■长度属性大于对象的最大索引。
索引是 0 范围内的整数...232–2，其字符串表示形式是对象属性的键

这是一个对象为了与 Array.prototype 的任何方法兼容而需要实现的所有行为

```
 var arrayLike = { 0: "a", 1: "b", 2: "c", length: 3 };
var result = Array.prototype.map.call(arrayLike, function(s) {
return s.toUpperCase();
}); // ["A", "B", "C"]

function toArray(arrayLikeObject) {
    return Array.prototype.slice.call(arrayLikeObject);
} 
```

Enter fullscreen mode Exit fullscreen mode

字符串也像不可变数组一样，因为它们可以被索引
，并且它们的长度可以作为长度属性被访问。所以不修改数组的
Array.prototype 方法使用
字符串:

```
var result = Array.prototype.map.call("abc", function(s) {
return s.toUpperCase();
}); // ["A", "B", "C"] 
```

Enter fullscreen mode Exit fullscreen mode

只有一个数组方法不是完全通用的:数组串联方法 concat。该方法可以在任何类似 arraylike 的接收器上调用，但是它测试其参数的[[Class]]。如果一个参数
是一个真数组，它的内容被连接到结果中；否则，
参数将作为单个元素添加。这意味着，例如，
我们不能简单地将一个数组和一个
arguments 对象的内容连接起来:

```
 function namesColumn() {
  return ["Names"].concat(arguments);
}
namesColumn("Alice", "Bob", "Chris");
// ["Names", { 0: "Alice", 1: "Bob", 2: "Chris" }] 
```

Enter fullscreen mode Exit fullscreen mode

为了说服 concat 将类似数组的对象作为真正的
数组对待，我们必须自己转换它。进行这种转换的一个流行而简洁的习语
是在类似数组的
对象上调用 slice 方法:

```
 function namesColumn() {
   return ["Names"].concat([].slice.call(arguments));
}
namesColumn("Alice", "Bob", "Chris");
// ["Names", "Alice", "Bob", "Chris"] 
```

Enter fullscreen mode Exit fullscreen mode

[https://stack overflow . com/questions/2218999/remove-duplicates-from-a-array-of-an-of-objects-in-JavaScript](https://stackoverflow.com/questions/2218999/remove-duplicates-from-an-array-of-objects-in-javascript)

*   如何在 JavaScript 中移除数组中的特定元素？
    [https://stack overflow . com/questions/5767325/how-do-I-remove-a-special-element-from-a-an-a-array-in-a-JavaScript](https://stackoverflow.com/questions/5767325/how-do-i-remove-a-particular-element-from-an-array-in-javascript)

*   如何在 JavaScript 中检查数组是否包含对象？
    [https://stack overflow . com/questions/237104/how-do-I-check-if-a-array-includes-an-object-in-JavaScript](https://stackoverflow.com/questions/237104/how-do-i-check-if-an-array-includes-an-object-in-javascript)

*   **检查对象是否为数组？**
    [https://stack overflow . com/questions/4775722/check-if-object-is-array](https://stackoverflow.com/questions/4775722/check-if-object-is-array)

*   为什么在数组迭代中使用“for…in”不是一个好主意？
    [https://stack overflow . com/questions/500504/why-is-use-for-in-with-array-iteration-a-bad-idea](https://stackoverflow.com/questions/500504/why-is-using-for-in-with-array-iteration-a-bad-idea)

*   如何在 Java 中连接两个数组？
    [https://stack overflow . com/questions/80476/how-can-I-concatenate-two-arrays-in-Java](https://stackoverflow.com/questions/80476/how-can-i-concatenate-two-arrays-in-java)

*   **从 JS 数组中移除重复值**
    [https://stack overflow . com/questions/9229645/Remove-duplicate-values-from-JS-array](https://stackoverflow.com/questions/9229645/remove-duplicate-values-from-js-array)

*   **在 JavaScript 中合并/展平一个数组的数组？**
    [https://stack overflow . com/questions/10865025/merge-flatten-an-array-of-arrays-in-JavaScript](https://stackoverflow.com/questions/10865025/merge-flatten-an-array-of-arrays-in-javascript)

*   * *对 JavaScript 对象数组进行分组的最有效方法是什么？
    [https://stack overflow . com/questions/14446511/what-the-most-efficient-method-to-group by-a-a-JavaScript-array-of-objects](https://stackoverflow.com/questions/14446511/what-is-the-most-efficient-method-to-groupby-on-a-javascript-array-of-objects)

*   * *从 JavaScript
    [中的对象数组中删除重复项 https://stack overflow . com/questions/2218999/Remove-duplicates-from-a-array-of-an-of-objects-in-JavaScript](https://stackoverflow.com/questions/2218999/remove-duplicates-from-an-array-of-objects-in-javascript)

*   * *从 JS 数组
    [中删除重复值 https://stack overflow . com/questions/1960473/get-all-unique-values-in-a-JavaScript-array-Remove-duplicates？noredirect=1 & lq=1](https://stackoverflow.com/questions/1960473/get-all-unique-values-in-a-javascript-array-remove-duplicates?noredirect=1&lq=1)

## [房子](#dom)

*   **揭秘原生 DOM API**
    [https://ponyfoo.com/articles/uncovering-the-native-dom-api](https://ponyfoo.com/articles/uncovering-the-native-dom-api)

*   **DOM 提示和技巧:父、子、兄弟**
    [https://www . site point . com/DOM-Tips-Techniques-Parent-Child-Siblings/](https://www.sitepoint.com/dom-tips-techniques-parent-child-siblings/)

阙:
安:

```
 var tr = Array.from(document.querySelectorAll('tr'));
    for(var i =0 ;i<tr.length ;i++){
       for(var j =0 ;j<tr.length ;j++){
         if(i ==j){
           console.log("i ",i ,j);
           console.log((Array.from(tr[i].querySelectorAll('td'))[i]).style.background ='red');
           console.log('.......')
         }      
       }
    }

    // your code 
```

Enter fullscreen mode Exit fullscreen mode

*   **现场收藏**

所有方法“getElementsBy*”都返回一个活动集合。这样的集合总是反映文档
的当前状态，并在它改变时“自动更新”

[http://JavaScript . info/searching-elements-DOM # live-collections](http://javascript.info/searching-elements-dom#live-collections)

*   **香草 JavaScript 中 DOM 操纵的基础知识(无 jQuery)**
    [https://www . site point . com/DOM-Manipulation-Vanilla-JavaScript-No-jQuery/](https://www.sitepoint.com/dom-manipulation-vanilla-javascript-no-jquery/)

*   **DOM 事件简介——轰动一时的杂志**
    [https://www . smashingmagazine . com/2013/11/An-DOM 事件简介/](https://www.smashingmagazine.com/2013/11/an-introduction-to-dom-events/)

*   **CSS 布局-显示属性**【https://www.w3schools.com/css/css_display_visibility.asp】  

*   **JavaScript 事件-w3school**
    [https://www.w3schools.com/js/js_events.asp](https://www.w3schools.com/js/js_events.asp)

适合快速查看 javascript 中的所有事件

[https://www.w3schools.com/jsref/obj_event.asp](https://www.w3schools.com/jsref/obj_event.asp)

[https://www . w3 . org/tr/uievents/# widl-mouse event-relatedtarget](https://www.w3.org/TR/uievents/#widl-MouseEvent-relatedTarget)

*   **事件()构造函数**
    [https://developer . Mozilla . org/en-US/docs/Web/API/Event/Event](https://developer.mozilla.org/en-US/docs/Web/API/Event/Event)

*   **事件界面**
    [https://developer.mozilla.org/en-US/docs/Web/API/Event](https://developer.mozilla.org/en-US/docs/Web/API/Event)

[https://www.w3schools.com/js/js_events.asp](https://www.w3schools.com/js/js_events.asp)

*   **事件介绍**[https://developer . Mozilla . org/en-US/docs/Learn/JavaScript/Building _ blocks/Events](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Events)

[https://www.w3schools.com/jsref/event_cancelable.asp](https://www.w3schools.com/jsref/event_cancelable.asp)

[https://developer . apple . com/library/archive/documentation/apple applications/Reference/safariweb content/handling events/handling events . html](https://developer.apple.com/library/archive/documentation/AppleApplications/Reference/SafariWebContent/HandlingEvents/HandlingEvents.html)

*   **touch event 中的 targetTouches、Touches 和 changedTouches 有什么区别？https://segmentfault.com/q/1010000002870710**T2

[https://github.com/krasimir/EventBus](https://github.com/krasimir/EventBus)
T3】https://stack overflow . com/questions/2863547/JavaScript-scroll-event-for-iphone-ipad

*   **区分 click vs mousedown/mouseup**
    T3】https://stack overflow . com/questions/12572644/区分-click-vs-mousedown-mouseup？noredirect=1 & lq=1

*   **HTML DOM 事件**
    T3】https://www.w3schools.com/jsref/dom_obj_event.aspT5[https://www.w3schools.com/jsref/obj_events.asp](https://www.w3schools.com/jsref/obj_events.asp)

[http://javascriptkit.com/javatutors/touchevents.shtml](http://javascriptkit.com/javatutors/touchevents.shtml)

[https://stack overflow . com/questions/31865416/event-target-event-to element-and-event-srcelemen 之间的区别是什么](https://stackoverflow.com/questions/31865416/what-is-the-difference-between-event-target-event-toelement-and-event-srcelemen)

*   **在 JS** [中创建自己的长按事件 https://stack overflow . com/questions/2625210/long-press-in-JavaScript](https://stackoverflow.com/questions/2625210/long-press-in-javascript)[https://github . com/John-Doherty/long-press-event/blob/master/src/long-press-event . JS](https://github.com/john-doherty/long-press-event/blob/master/src/long-press-event.js)

var pressTimer
document . getelementbyid(" demo ")。addEventListener("mousedown "，鼠标按下)；
document . getelementbyid(" demo ")。addEventListener("mouseup "，mouseUp)；

function mouseDown(){
press timer = window . settimeout(function(){
document . getelementbyid(" demo ")。innerHTML = "按住鼠标按钮。"；
}，1000)；

}

函数 mouseUp(){
clear time out(press timer)；
}

*   **可见:隐藏和显示:无有什么区别？**[https://developer . Mozilla . org/en-US/docs/Web/API/html Element/hidden](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/hidden)[https://developer . Mozilla . org/en-US/docs/Web/API/Element/closest](https://developer.mozilla.org/en-US/docs/Web/API/Element/closest)[https://stack overflow . com/questions/133051/what-is-the-difference-being-visibility hidden-and-display none](https://stackoverflow.com/questions/133051/what-is-the-difference-between-visibilityhidden-and-displaynone)

[https://javascript.info/task/sliding-tree](https://javascript.info/task/sliding-tree)T2】

```
 var el = document.getElementById('tree');
console.log(el);
el.addEventListener("click", handler);

function handler(e){
  if(e.target.children.length == 1){
    e.target.children[0].hidden = !e.target.children[0].hidden;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   **mouseleave**[https://developer . Mozilla . org/en-US/docs/Web/Events/mouseleave](https://developer.mozilla.org/en-US/docs/Web/Events/mouseleave)

鼠标离开 vs 鼠标悬停
T1】https://codepen.io/deen_john/pen/jONJwJz?editors=0010T3】https://www.youtube.com/watch?v=pzT4hAY82q4[T5】](https://www.youtube.com/watch?v=pzT4hAY82q4)

*   **prevent default**【https://javascript.info/default-browser-action[https://bugzilla.mozilla.org/show_bug.cgi?id=653009](https://bugzilla.mozilla.org/show_bug.cgi?id=653009)[https://stack overflow . com/questions/30426523/why-does-prevent default-on-checkbox-click-event-returns-true-for-the-checked-att](https://stackoverflow.com/questions/30426523/why-does-preventdefault-on-checkbox-click-event-returns-true-for-the-checked-att)[https://TechNet . Microsoft . com/en-us/windows/ff 974940(v = vs . 60)](https://technet.microsoft.com/en-us/windows/ff974940(v=vs.60))

Safari 4、Android 2.2 WebKit 和 Opera Mobile 11 都有以下行为:

touchstart 事件上的 preventDefault 防止滚动、双击缩放和鼠标抬起/鼠标放下/单击事件。

touchmove 事件上的 preventDefault 防止滚动。

touchend 事件上的 preventDefault 不会阻止滚动或鼠标事件。

4)这些都不能防止收缩变焦。Safari 实现了一个单独的“gesturechange”事件，该事件是为收缩手势触发的，可用于防止缩放。此事件不是由 Android 或 Opera 实现的。

示例:

```
 <!DOCTYPE html>
<html>
<body>

<h1>Show checkboxes:</h1>

<form action="/action_page.php">
  <input type="checkbox" name="vehicle1" value="Bike"> I have a bike<br>
  <input type="checkbox" name="vehicle2" value="Car"> I have a car<br>
  <input type="checkbox" name="vehicle3" value="Boat" checked> I have a boat<br><br>
  <input type="submit" value="Submit">
</form>

<script>
var fe = document.querySelector('form');

fe.addEventListener("mousedown", function(e){
  console.log(e);
  e.preventDefault()
});

fe.addEventListener("mouseup", function(e){
e.preventDefault()
  console.log(e)
});

fe.addEventListener("click", function(e){
e.preventDefault()
  console.log(e)
});

</script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
 <body>

<p>This example uses the addEventListener() method to attach a "mousedown" and "mouseup" event to a p element.</p>

<p id="demo">Click me.</p>

<script>
document.getElementById("demo").addEventListener("mousedown", mouseDown);
document.getElementById("demo").addEventListener("mouseup", mouseUp);

function mouseDown(e) {
e.preventDefault();
//return false ;
 // document.getElementById("demo").innerHTML = "The mouse button is held down.";
}

function mouseUp(e) {
e.preventDefault();
//return false
  //document.getElementById("demo").innerHTML = "You released the mouse button.";
}
</script>

</body> 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，事件顺序是鼠标按下>鼠标抬起>点击
preventdefault 只对点击有效

被动处理程序
[https://JavaScript . info/default-browser-action # the-passive-handler-option](https://javascript.info/default-browser-action#the-passive-handler-option)

*   **停止事件传播**https://css-tricks.com/dangers-stopping-event-propagation/T2[http://jsfiddle.net/tedp/aL7Xe/1/](http://jsfiddle.net/tedp/aL7Xe/1/)

Element.closest()

```
 $(document).on('click', function(event) {
  if (!$(event.target).closest('#menucontainer').length) {
    // Hide the menus.
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

上面的处理程序监听文档上的点击，并检查事件目标
是否是#menucontainer 或者是否有#menucontainer 作为父对象。如果没有，您知道点击来自#menucontainer 外部的
，因此如果菜单可见，您可以隐藏它们。

[https://developer . Mozilla . org/en-US/docs/Web/API/Element/closest](https://developer.mozilla.org/en-US/docs/Web/API/Element/closest)

取消气泡
如果气泡属性设置为假，气泡阶段将被跳过

```
 <body>

<p>Click the button to find out if the onclick event is a bubbling event.</p>

<button id ='button'>Try it</button>

<p id="demo"></p>

<script>

document.addEventListener("click", myFunction1);
button.addEventListener("click", myFunction1);
button.addEventListener("click", myFunction2);

function myFunction1() {
  console.log(event.cancelBubble);
  console.log('event ',  event.currentTarget)
  event.cancelBubble=true;
  document.getElementById("demo").innerHTML = event.cancelBubble;
}

function myFunction2(event) { 

  console.log('event ',  event.currentTarget)
  console.log(event.cancelBubble);

  document.getElementById("demo").innerHTML = event.cancelBubble;
}
</script>

</body> 
```

Enter fullscreen mode Exit fullscreen mode

*   **禁用 Android 上长点击的上下文菜单**
    [https://stack overflow . com/questions/3413683/disable-the-context-menu-on-long-taps-on-Android？noredirect=1 & lq=1](https://stackoverflow.com/questions/3413683/disabling-the-context-menu-on-long-taps-on-android?noredirect=1&lq=1)

*   **clientX vs PageX vs ScreenX**
    [https://heeestorys.tistory.com/778](https://heeestorys.tistory.com/778)
    [https://stack overflow . com/questions/9262741/what-is-the-difference-PageX-y-clientX-y-ScreenX-y-in-JavaScript/17705548](https://stackoverflow.com/questions/9262741/what-is-the-difference-between-pagex-y-clientx-y-screenx-y-in-javascript/17705548)
    [https://blog.csdn.net/lzding/article/details/45437707](https://blog.csdn.net/lzding/article/details/45437707)
    [https://heeestorys.tistory.com/778](https://heeestorys.tistory.com/778)
    [https://developer . Mozilla](https://developer.mozilla.org/en-US/docs/Web/API/Element/clientHeight)

*   **事件顺序**
    事件顺序:

[https://www.html5rocks.com/en/mobile/touchandmouse/](https://www.html5rocks.com/en/mobile/touchandmouse/)
T3】https://www . w3 . org/TR/ui events/# events-mouse event-event-orderT5[https://www.w3.org/TR/uievents](https://www.w3.org/TR/uievents)

焦点事件:
在焦点事件被触发之前，mousedown 事件被触发

*   **泡沫 vs moueleve**
    [【https://JavaScript . info/mousemove-mousevo-mouseyeter-moueleev](https://javascript.info/mousemove-mouseover-mouseout-mouseenter-mouseleave)
    [【https://www . w3 schools . com/jsref/try it . ASP】？filename = try jsref _ onmouseover _ addevent listener](https://www.w3schools.com/jsref/tryit.asp?filename=tryjsref_onmouseover_addeventlistener)
    [【https://www . w3 . org/tr/uievents】](https://www.w3.org/TR/uievents)

*   **创造自己的可视性 change event**
    [https://www . html 5 rocks . com/en/tutorials/page visibility/intro/](https://www.html5rocks.com/en/tutorials/pagevisibility/intro/)

*   **指针事件 IE**
    T3】https://msdn . Microsoft . com/en-us/windows/dn 304886(v = vs . 71)

*   什么是视口？
    [https://www . ki rupa . com/html 5/viewport _ device _ document _ size . htm](https://www.kirupa.com/html5/viewport_device_document_size.htm)

*   **DOM in depth-1-my notes**
    T3】https://github . com/deen John/Javascript-revision/tree/master/DOM % 20in % 20 depth % 20-1

*   **innerText vs innerHTML vs text content**
    T3】https://www.w3schools.com/jsref/prop_node_textcontent.asp

textContent gets the content of all elements, including and*