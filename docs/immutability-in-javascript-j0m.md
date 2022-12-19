# JavaScript 中的不变性

> 原文：<https://dev.to/saiicharan/immutability-in-javascript-j0m>

在我讨论不变性这个话题之前，让我们先来看看什么是突变。

## 突变

任何改变/转变一个物体的行为或结构的东西都叫做突变。就像上图中的原力变异悟空。

## JavaScript 中的突变

这个定义可能看起来与上面的例子非常相似。虽然，在 JavaScript 中，它看起来像这样，

```
let a = 1;

a = 2;

console.log(a); // 2

//In the above case, we are mutating(transforming) the value of the variable a. 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们改变了变量 a 的值。

### 但是，为什么 JavaScript 中突变是个问题？

在回答这个问题之前，我先说一下 JavaScript 中关于**按值传递**和**按引用传递**的几个概念。

### 按值传递

考虑下面的例子，

```
let a = 5;
let b = a;

console.log(a); // 5
console.log(b); // 5

a = 15;
console.log(a); // 15
console.log(b); // 5 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我创建了一个值为 5 的变量“a”，并将它赋给一个名为“b”的变量。那么,“a”和“b”的值都是 5。在这种情况下，我已经将“a”的值传递给了“b”。

现在假设我把 a 的值改为 15。这样做，我的“b”值不会改变，因为它最初是通过一个值**传递的。这种将变量的**值**传递给另一个变量的方式可以表述为**按值传递**。**

### 通过引用传递

考虑下面的例子，

```
let object1 = {name: 'sai'};
let object2 = object1;

console.log(object1); // {name: 'sai'}
console.log(object2); // {name: 'sai'}

object1.name = 'charan';

console.log(object1); // {name: 'charan'}
console.log(object2); // {name: 'charan'} WHAAAAAT? 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我创建了一个对象，并将其赋给变量“object1”。我创建了另一个变量“object2 ”,并将“object1”赋给它。

现在在第 7 行，我将“object1”的 name 属性改为“charan”。令人惊讶的是，当我记录“object2”的值时,“object2”的 name 属性也变成了“charan”。这里到底发生了什么？对象通常由引用传递**。因为 object2 等同于 object1，所以它是通过引用**传递的**，这意味着它们拥有相同的内存位置。因此，当我们改变 object1 的值时，object2 通常也会改变。**

[![Both object1 and object2 point to the same memory location](../Images/0155c6ae77e036c52cf42500143aa6c4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fnrccOOJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/er5yt4yflb170f9k55xc.png)

对象 1 和对象 2 都指向同一个内存位置。

我们可以在数组中发现类似的问题。考虑下面的例子，

```
let arr = [1,2,3,4,5];
let newArr = arr;

console.log(arr); // [1,2,3,4,5]
console.log(newArr); // [1,2,3,4,5]

arr.push(6);

console.log(arr); // [1,2,3,4,5,6]
console.log(newArr); // [1,2,3,4,5,6] WHAAAAAT? 
```

Enter fullscreen mode Exit fullscreen mode

数组和对象都是通过引用传递的。

这怎么成问题了？想象一下，你在不同的函数之间传递一个对象/数组，并且在某个时候对它进行了变异，同样的对象/数组可能在其他地方发生变异，导致代码的某些部分出现意外行为，从而产生 bug。

## 解决方法:去不可改变！

在 JavaScript 中实现不变性的一种方法是使用 [Object.assign](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) 。

```
let object1 = {name: 'sai'};
let object2 = Object.assign({}, object1, {name: 'charan'});

console.log(object1); // {name: 'sai'}
console.log(object2); // {name: 'charan'}

object1.name = 'batman';

console.log(object1); // {name: 'batman'}
console.log(object2); // {name: 'charan'} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们使用 Object.assign 来创建“object2”变量。Object.assign 通常接受三个值，初始值、源对象和替换对象。在我们的例子中，初始值是一个空对象，我们得到“object1”的内容，并向其中添加一个新对象{name: 'charan'}。如果 object1 中已经有一个属性“name”，它将被替换为现有值(在我们的例子中为“charan”)，并将其赋给初始值(这是一个空对象)。所以现在，我的 object2 是一个新鲜的对象。“object1”的突变不会影响 object2，因为现在两者都指向不同的内存位置。

在 JavaScript 中实现不变性的另一种方法是使用[扩展操作符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)。

```
let object1 = {name: 'sai'};
let object2 = {...object1, name: 'charan'};

console.log(object1); // {name: 'sai'}
console.log(object2); // {name: 'charan'}

object1.name = 'batman';

console.log(object1); // {name: 'batman'}
console.log(object2); // {name: 'charan'} 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子和 Object.assign 做了同样的事情，但是语法看起来更简洁。

在数组中，通过使用相同的扩展操作符可以实现不变性。

```
let arr = [1,2,3,4,5];
let newArr = [...arr, 6];

console.log(arr); // [1,2,3,4,5]
console.log(newArr); // [1,2,3,4,5,6]

arr.push(11);

console.log(arr); // [1,2,3,4,5,11]
console.log(newArr); // [1,2,3,4,5,6] 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，6 被添加到“newArr”变量中。第 7 行中“arr”的突变不影响“newArr”变量。同样，对于 JavaScript 中的数组， [map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) ， [filter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) ， [reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) 可以用来避免突变，因为它们在每次被调用时都会返回新的数组。

编写不可变的 Javascript 代码是一个很好的实践。我希望这篇文章能让您对变量如何存储在内存中以及不变性如何帮助您编写质量更好的代码有一个基本的了解。欢迎反馈和建议。:)

[媒体上也有文章](https://medium.com/@saiicharan/immutability-in-javascript-bf5ab5e9e79c)