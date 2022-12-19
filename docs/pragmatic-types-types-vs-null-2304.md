# 实用类型:类型 vs 空

> 原文：<https://dev.to/stereobooster/pragmatic-types-types-vs-null-2304>

> 空引用:十亿美元的错误
> 
> -托尼·霍尔，空引用的发明者。[见他的谈话](https://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare)

## 什么是空值，为什么你应该关心？

Null 表示缺少值。例如，如果您试图从数组(vector)中获取值，则值有可能会丢失(如果数组为空)。在这种情况下，系统能做什么？

*   抛出异常(或者返回异常，就像在 Go 中一样)
*   返回值表示缺少值-空值

此外，空值用于未初始化的值，您需要它们来构造循环结构。

## 有什么问题

主要的问题是当空值被认为是所有类型的一部分时，例如，空值是一个有效的数字或有效的字符串，但同时你不能对空值应用给定类型的任何操作。

```
"" + undefined
"undefined"

1 * undefined
NaN 
```

## 空值和 JavaScript

托尼·霍尔说空引用是十亿美元的错误。JavaScript 通过引入两个空值使其翻倍:`null`和`undefined`。我不确定为什么，我相信是有历史原因的，大概是 null 被引入到尽可能接近 Java 的品牌 JS 中(和名字选择一样)。

```
null === undefined
false
null == undefined
true
1 * undefined
NaN
1 * null
0 
```

## 基于类型的解决方案

Flow 和其他现代类型系统不认为`null`(或`undefined`)是任何“基本”类型的一部分:

流量:

```
let x:number = undefined;
                  ^ Cannot assign `undefined` to `x` because undefined [1] is incompatible with number [2]. 
```

打字稿:

```
let x:number = undefined;
Type 'undefined' is not assignable to type 'number'. 
```

此外，它还检查变量是否已初始化

流量:

```
let x:number;
x * 1;
^ Cannot perform arithmetic operation because uninitialized variable [1] is not a number. 
```

打字稿:

```
let x:number;
x * 1;
Variable 'x' is used before being assigned. 
```

但是要小心流动:

```
let x:number;
x + 1;
No errors! 
```

以打字打的文件

```
let x:number;
x + 1;
Variable 'x' is used before being assigned. 
```

### 选项类型或如何用类型表示值的缺失

表示`null`值的好方法是所谓的标记*联合——想象你有一个不同类型的值的集合，如果你将每个值附加一些标记，你可以清楚地区分一个值和另一个值，你可以安全地把它混合在一个袋子里。所以你用一个标签来标记所有的实际值，你有一个特殊值，用一个代表空值的标签。

你可以这样想象(粗略的例子，JS 里不这么做):

```
const taggedValues = [
  {
    type: "Some",
    value: 1
  },
  {
    type: "None"
  },
] 
```

在 ML 语言中，你不需要构造对象，你可以在类型的帮助下完成它

```
type 'a option = None | Some of 'a 
```

它可以粗略地翻译为

```
type None = void
type Some<a> = a
type Option<a> = None | Some<a>
// or simpler
type Option<a> = void | a
// even simpler - syntax sugar
type Option<a> = ?a 
```

流中的原生解称为[可能是类型](https://flow.org/en/docs/types/maybe/)。

打字稿:

```
type Option<a> = void | a 
```

如果使用选项类型，系统将确保不对该值应用任何操作，除非您检查该值是否确实存在。

```
let x:?number = 1;
x * 1;
^ Cannot perform arithmetic operation because null or undefined [1] is not a number.

let x:?number;
if (x != undefined) x * 1;
No errors! 
```

以打字打的文件

```
let x:?number = 1;
x * 1;
No errors 
```

但是:

```
const t = (x:number|void) => x * 1;
The left-hand side of an arithmetic operation must be of type 'any', 'number' or an enum type. 
```

选项类型-对于空值来说是更安全的选择。(还有一个选择——可能是单子。)

### 选项类型的详细程度

在能够在任何操作(需要精确类型)中使用选项类型值之前，您需要证明该值确实存在:

```
let x:?number;
// some code which touches x

// x can be number or null or undefined at this point of code
if (x != undefined) {
  // x only can be a number at this point of code,
  // otherwise we will not get into this branch.
  // We can say that if condition proves that inside of this scope x is number.
  x * 1;
} 
```

这种方法可能会很冗长，这就是为什么我只在需要时才尝试使用选项类型。例如，要描述表单的状态:

```
type State = {
  name?: string,
  age?: number
};
// initial state of the form
let state: State = {};
// user filled in first field
state = { name: 'Abc' };
// user filled in second field
state = { name: 'Abc', age: 20 }; 
```

但是一旦用户提交(以及所有需要填写的字段，在用户可以提交之前)，我们可以使用更严格的类型:

```
type User = {
  name: string,
  age: number
};
const onSubmit = (user: User) => { /* code */ }; 
```

这有助于与冗长作斗争

## 一句警告

我宣传了很多选项类型，但是在 Flow 和 TypeScript 中的选项类型实现中有一个警告:

```
const a: Array<{c:1}> = [];
const b = a[0]
const d = b.c;
No errors!
const e: {[key: string]: {c:1}} = {};
const f = e.f;
f.c;
No errors! 
```

它不会在这里捕捉错误！请记住这一点。

[这篇文章是](https://dev.to/t/pragmatictypes)系列文章的一部分。在[推特](https://twitter.com/stereobooster)和 [github](https://github.com/stereobooster) 上关注我。