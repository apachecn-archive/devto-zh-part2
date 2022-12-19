# 数组引用...又如何不是！

> 原文：<https://dev.to/genta/array-reference-and-how-to-not-54ng>

# 有时候很黏...

看看这段代码...你认为*品种*阵列会发生什么？

```
let breeds = ["Labrador","Akita","Siamese"]

// Let's make an array of only Dogs Breed... splice out Siamese.

let doggy = breeds;

doggy.splice(2)

console.log(doggy)
// -> ["Labrador", "Akita"]

console.log(breeds)
// -> ["Labrador", "Akita"] 
```

Enter fullscreen mode Exit fullscreen mode

因此...我们只是想改变狗类*阵列*，而不是*品种*阵列。

我们告诉 javascript:

1.  我想要一个新的数组；
2.  叫它*doggy*；
3.  给狗狗*品种*的**相同值**；

```
let doggy = breeds; 
```

Enter fullscreen mode Exit fullscreen mode

但是带有 *"="* 的 Javascript 创建了一个**引用**。

随着我们的声明*狗狗*和*品种*通过引用指向“同一个对象”...同样的记忆，改变一个，你就改变了两个！

<center>**evil TWINS**</center>

[![twins](../Images/4cb0b73dcdc7d48c7192c62e7a536d8d.png)T2】](https://i.giphy.com/media/iGei6Nd3NlwnC/giphy.gif)

# 我们来列个清单吧...如何不创建参考

如果我们只想将一个数组的*值*传递给另一个数组，同时创建一个*“新对象”*。
我们可以使用这种有用的方法。

```
//1\. Create a copy with slice.
let doggy = breeds.slice();

//2\. Create a concatenation with an empty array.
let doggy = [].concat(breeds);

//3\. Spread an array into another one.
let doggy = [...breeds];

//4\. Use the Array.from() method, to create an array, with the same value of //another one
let doggy = Array.from(breeds); 
```

Enter fullscreen mode Exit fullscreen mode

所有的方法，在这里，正在创建一个完全**新的** *狗狗*数组，没有任何参考*品种*的。你现在可以脱下*连体*了，没有任何附带效果。

```
let breeds = ["Labrador","Akita","Siamese"]

let doggy = breeds.slice();

doggy.splice(2)

console.log(doggy)
// -> ["Labrador", "Akita"]

console.log(breeds)
// -> ["Labrador", "Akita", "Siamese"] 
```

Enter fullscreen mode Exit fullscreen mode

<center>Now they're in different arrays... but can still love each other!</center>

[![love](../Images/769e57ea5f61038e1546ffb49c898357.png)T2】](https://i.giphy.com/media/796g6BvEXi7v2/giphy.gif)