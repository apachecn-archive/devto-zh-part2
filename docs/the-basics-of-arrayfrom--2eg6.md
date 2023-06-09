# Array.from()的基础知识

> 原文：<https://dev.to/domazet93/the-basics-of-arrayfrom--2eg6>

**Array.from()** 方法允许你创建一个浅层副本:

*   类似数组的对象(具有长度属性和索引元素的对象)
*   可迭代对象(如*映射*和*集合*

为了可迭代，对象必须实现@@iterator 方法，这意味着对象(或其原型链之前的对象之一)必须有一个带有@@iterator 键的属性，该键可通过常量符号获得

```
a = new Map([[ 1, 5 ]])
isIterable = typeof a[Symbol.iterator] === 'function'; //true

b = { 1: 5 }
isIterable = typeof b[Symbol.iterator] === 'function'; //false 
```

Enter fullscreen mode Exit fullscreen mode

#### 浅抄

浅层副本将复制顶级属性。如果任何属性是对象，则复制它们的引用地址。

对一个阵列进行更改不会感染另一个阵列。从原始数组中删除最后一个元素不会改变复制数组的长度，因为它将保持不变。

```
let arrayOne = [ 1, 2, 3 ]
let arrayTwo = Array.from(arrayOne)

arrayTwo.pop()

console.log(arrayOne) //[ 1, 2, 3]
console.log(arrayTwo) //[ 1, 2 ] 
```

Enter fullscreen mode Exit fullscreen mode

例外是在原始数组和复制数组之间共享的对象，因为它们指向存储在内存中某处的同一个引用。

```
let arrayOne = [ 1, 2, [3] ];
let arrayTwo = Array.from(arrayOne)

arrayOne[2][0] = "abc";

console.log(arrayOne) //[ 1, 2, ["abc"] ];
console.log(arrayTwo) //[ 1, 2, ["abc"] ] 
```

Enter fullscreen mode Exit fullscreen mode

#### 映射函数和数组

Array.from()有一个可选参数 **mapFn** ，它允许您对数组的每个元素执行一个函数。

使用 Array.from()创建一个所需长度的数组，并为数组中的每个元素调用一个 map 函数，将它们的平方值收集到一个新的数组:

```
Array.from({ length: 5 }, (v, i) =>  i * i); //[0, 1, 4, 9, 16] 
```

Enter fullscreen mode Exit fullscreen mode