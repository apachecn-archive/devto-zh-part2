# 如何使用集合在 JavaScript 中创建唯一值的数组

> 原文：<https://dev.to/clairecodes/how-to-create-an-array-of-unique-values-in-javascript-using-sets-5dg6>

## TL；速度三角形定位法(dead reckoning)

```
let uniqueArray = [...new Set([5,5,2,2,2,4,2])];
// [5,2,4] 
```

Enter fullscreen mode Exit fullscreen mode

## 恕罪？

抱歉，标题冗长——有时用代码示例可以解释得更清楚。

假设您有一个包含许多元素的 JavaScript 数组，其中一些是重复的:

```
let dupeArray = [1,1,4,5,4,4,2,1,5]; 
```

Enter fullscreen mode Exit fullscreen mode

您的目标是删除重复项，每个值只留下一个条目:

```
let uniqueArray = [1,4,5,2]; 
```

Enter fullscreen mode Exit fullscreen mode

你可以写一个 for 循环，或者使用一个`map`或者`filter`来得到这个结果。有一百万种不同的方法来解决计算机编程中的一个问题(这就是为什么它如此有趣！).ES6 引入了许多新特性，包括集合，我们也可以用一行代码来解决这个问题。

*注意:*元素类型可以是字符串，或者是数字和字符串的混合，但是我在这里使用整数，因为它们打字更快！

## 什么是集合？

集合是 ES6 中引入的新数据结构。直接引自 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set) :

> 集合对象是值的集合。您可以按插入顺序循环访问集合中的元素。集合中的值只能出现一次；它在该系列中独一无二。

在这里，我们可以利用唯一值的规则。

让我们创建一个集合，向它添加一些值并查询大小。(您可以在现代浏览器的开发工具控制台中跟随):

```
let mySet = new Set().add(1).add(3).add(2).add(1);
// Set(3) {1, 3, 2}
mySet.size
// 3 
```

Enter fullscreen mode Exit fullscreen mode

注意最后的`1`没有被添加，集合的大小仍然是 3 而不是 4。在数组中，它将被添加，长度为 4。

向集合中添加值有两种方法。首先使用上述`add`方法。其次，通过将一个数组传递给构造函数(`new Set()` ):

```
let arraySet1 = new Set([3,2,1]);
// Set(3) {3, 2, 1} 
```

Enter fullscreen mode Exit fullscreen mode

如果重复的值包含在初始数组中，它们也将被删除:

```
let arraySet2 = new Set([8,8,9,2]);
// Set(3) {8,9,2}
arraySet2.size;
// 3 
```

Enter fullscreen mode Exit fullscreen mode

你大概可以看到这是怎么回事。剩下要做的就是将这个集合转换成一个数组，我们已经实现了最初的目标。有两种方法可以做到这一点:都使用 ES6 方法。

## `Array.from()`

[`Array.from`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from) 方法从一个类似数组的结构中创建一个新数组:

```
let dupeArray = [3,2,3,3,5,2];
let uniqueArray = Array.from(new Set(dupeArray)); 
```

Enter fullscreen mode Exit fullscreen mode

## 传播算子`...`

这三个点在 ES6 中无处不在。它们无处不在，有多种用途(对谷歌来说，它们是一种痛苦)。当我们将它们用作[扩展操作符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)时，它们可以用来创建一个数组:

```
let uniqueArray = [...new Set(dupeArray)]; 
```

Enter fullscreen mode Exit fullscreen mode

你应该使用这两种方法中的哪一种？spread 语法看起来很酷，但是`Array.from`的目的更明确，也更容易阅读。他们在这里完成同样的事情，所以选择是你的！

## 结论

在 ES6 中，一些需要很多行代码和变量的事情现在可以在一行代码中执行。活着是多么美好的时光。

```
let uniqueArray = [...new Set([5,5,2,4,2])];
// [5,2,4] 
```

Enter fullscreen mode Exit fullscreen mode