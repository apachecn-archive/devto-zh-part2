# 在 JavaScript 中遍历对象

> 原文：<https://dev.to/zellwk/looping-through-objects-in-javascript-3c6b>

有时，您可能需要在 JavaScript 中遍历对象。在 ES6 之前这样做的唯一方法是使用一个`for...in`循环。

一个`for...in`循环的问题是它遍历原型链中的属性。当您使用`for...in`循环遍历一个对象时，您需要检查该属性是否属于该对象。你可以用`hasOwnProperty`做到这一点。

```
for (var property in object) {
  if (object.hasOwnProperty(property)) {
    // Do things here
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在再也不用依赖`for...in`和`hasOwnProperty`了。有更好的方法。

## 循环遍历对象的更好方式

遍历对象的更好方法是先用**将对象转换成数组。然后，循环遍历数组。**

有三种方法可以将对象转换为数组:

1.  `Object.keys`
2.  `Object.values`
3.  `Object.entries`

### Object.keys

创建一个包含对象属性的数组。这里有一个例子。

```
const fruits = {
  apple: 28,
  orange: 17,
  pear: 54,
}

const keys = Object.keys(fruits)
console.log(keys) // [apple, orange, pear] 
```

Enter fullscreen mode Exit fullscreen mode

### 对象值

创建一个包含对象中每个属性的值的数组。这里有一个例子:

```
const fruits = {
  apple: 28,
  orange: 17,
  pear: 54,
}

const values = Object.values(fruits)
console.log(values) // [28, 17, 54] 
```

Enter fullscreen mode Exit fullscreen mode

### 对象.条目

创建一个数组的数组。每个内部数组有两项。第一项是财产；第二项是价值。

这里有一个例子:

```
const fruits = {
  apple: 28,
  orange: 17,
  pear: 54,
}

const entries = Object.entries(fruits)
console.log(entries)
// [
//   [apple, 28],
//   [orange, 17],
//   [pear, 54]
// ] 
```

Enter fullscreen mode Exit fullscreen mode

这三个中我最喜欢的是`Object.entries`,因为你可以同时获得键值和属性值。

## 循环遍历数组

一旦你用`Object.keys`、`Object.values`或`Object.entries`将对象转换成一个数组，你就可以像一个普通数组一样循环遍历它。

```
// Looping through arrays created from Object.keys
const keys = Object.keys(fruits)
for (const key of keys) {
  console.log(key)
}

// Results:
// apple
// orange
// pear 
```

Enter fullscreen mode Exit fullscreen mode

如果你使用`Object.entries`，你可能想要[将数组分解](https://zellwk.com/blog/es6)成它的键和属性。

```
for (const [fruit, count] of entries) {
  console.log(`There are ${count}  ${fruit}s`)
}

// Result
// There are 28 apples
// There are 17 oranges
// There are 54 pears 
```

Enter fullscreen mode Exit fullscreen mode

## 包装完毕

循环遍历对象的更好方法是首先用这三种方法之一将其转换成数组。

1.  `Object.keys`
2.  `Object.values`
3.  `Object.entries`

然后，像普通数组一样遍历结果。

如果这一课对你有帮助，你可能会喜欢[学习 JavaScript](https://learnjavascript.today) ，在这里你将学习如何从头开始构建任何你想要的东西。学习 JavaScript 的报名于 2018 年 7 月开放(两周后！).

* * *

感谢阅读。这篇文章最初发表在我的博客上。如果你想要更多的文章来帮助你成为一个更好的前端开发者，请注册[我的时事通讯](https://zellwk.com)