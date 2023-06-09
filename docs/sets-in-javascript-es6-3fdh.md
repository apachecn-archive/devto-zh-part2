# Javascript ES6 中的设置

> 原文：<https://dev.to/damcosset/sets-in-javascript-es6-3fdh>

## 简介

在 ES6 之前，Javascript 没有 set 的本地实现。什么是集合？集合是不能包含重复值的值列表。让我们探索一下原生 ES6 集解决了哪些问题，以及我们如何使用它们。

### 构造函数

要创建一个新的集合，我们可以使用`new Set()`。你也可以在构造函数中直接给出一个迭代器。

```
const set = new Set()
console.log(set) // Set {}

const set = new Set([1, 2, 3])
console.log(set) // Set { 1, 2, 3 } 
```

Enter fullscreen mode Exit fullscreen mode

如果给定一个有重复项的 iterable，集合将忽略第一个 iterable 之后的重复项:

```
const set = new Set([1, 2, 2, 2, 3, 4, 5, 5, 5, 4])
// Set { 1, 2, 3, 4, 5 } 
```

Enter fullscreen mode Exit fullscreen mode

### *添加*方法和*大小*属性

集合有一个 *add* 方法，允许您向集合中添加单个项目。集合还有一个*大小*属性来检索集合中的项目数。

```
const set = new Set()
set.size // 0
set.add(2)
set.size // 1 
```

Enter fullscreen mode Exit fullscreen mode

如果集合已经有值:
，则忽略*添加*

```
const set = new Set(['Hello', 'World'])
set.add('Hello')
console.log(set) // Set { 'Hello', 'World' } 
```

Enter fullscreen mode Exit fullscreen mode

### 从变通办法中解决的问题

在设置之前，您必须使用普通对象来模拟设置。因为只有字符串可以用作键，所以可能会出现一些问题。5 将被强制为“5”，{}将是“[object Object]”。集合不强制值。5 和“5”是两个不同的值。

```
const set = new Set()
set.add({})
set.add({})

set.size // 2
console.log(set) // Set { {}, {} }

set.add(5)
set.add('5')
set.add(5) // this will be ignored

set.size // 4
console.log(set) // Set { {}, {}, 5, '5' } 
```

Enter fullscreen mode Exit fullscreen mode

因此，可以将多个对象添加到集合中。集合使用`Object.is()`来比较两个值:

```
 Object.is(5, 5) //true
Object.is(5, '5') //false
Object.is({},{}) //false 
```

Enter fullscreen mode Exit fullscreen mode

### *有*，*删除*和*清除*的方法

*   *has(value)* 检查值是否在集合中。返回真或假
*   *delete(value)* 从集合中删除值
*   *clear()* 删除集合中的所有值

```
const set = new Set()
set.add(5)

set.has(5) // true
set.has(8) // false

const set = new Set([1, 2, 3])
set.size // 3
set.delete(2)
set.size // 2
set.clear()
set.size // 0 
```

Enter fullscreen mode Exit fullscreen mode

### 迭代

要循环访问一个集合，可以使用 forEach()方法。与在对象/数组上使用它有一点不同。forEach()有三个参数:

*   价值
*   关键字(索引)
*   正在迭代的数组或对象

但是，在集合中，前两个参数是相同的。这是因为集合没有键。所以:

```
const set = new Set([1, 2, 3])
set.forEach((value, key, s) => {
    console.log(`${value}  ${key}`)
    console.log( set === s)
})

//1 1
//true
//2 2
//true
//3 3
//true 
```

Enter fullscreen mode Exit fullscreen mode

这样，您可以使用已经用于数组和对象的相同方法。

### 集合= >阵列

我们已经看到，通过将数组传递给 set 构造函数，可以将数组转换为集合。您也可以使用 spread 运算符将集合转换为数组:

```
const set = new Set([1, 2, 3])
const array = [...set]
console.log(array) // [ 1, 2, 3 ] 
```

Enter fullscreen mode Exit fullscreen mode

### 弱集

这些集合可以被称为*强*集合，因为它保存了对象引用。它的工作原理就像你将一个对象存储在一个变量中。只要 set 实例存在，就不能为了释放内存而对对象进行垃圾收集。

```
const set = new Set()
let obj = {}

set.add(obj)
set.size // 1
obj = null
set.size // 1

// We can retrieve the original reference
obj = [...set][0]
console.log(obj) // {} 
```

Enter fullscreen mode Exit fullscreen mode

在某些情况下，如果集合中的所有参照都消失了，您确实希望这些参照也消失。ES6 包括弱集。弱集合只能存储弱对象引用。因此，如果没有对一个对象的其他引用，集合中的引用将会消失。WeakSet 也不能包含原始值(无字符串或整数)

```
const set = new WeakSet()
let obj = {}

set.add(5) // ERROR
let obj = {}
set.add(obj)
set.has(obj) // true
obj = null // remove the reference to obj, also removes it in set
set.has(obj) // false 
```

Enter fullscreen mode Exit fullscreen mode

武器装备:

*   如果将非对象传递给 *add()* 、 *has()* 或 *delete()* ，将会抛出错误。
*   不是可重复的。不能使用 for-of 或 forEach()方法。
*   没有大小属性。

弱集合的*有限的*功能对于正确处理内存是必要的。

## 结论

ES6 为您提供了一种创建集合的新方法，并通过开发人员使用的变通方法解决了许多 ES5 问题。如果只需要跟踪对象引用和防止内存泄漏，请使用弱集。