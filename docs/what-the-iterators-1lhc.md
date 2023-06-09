# 搞什么...迭代器？

> 原文：<https://dev.to/genta/what-the-iterators-1lhc>

# 迭代器没那么可怕...

根据 *MDN web 文档*:

> *为...语句的 of* 创建一个循环，对**可迭代的**对象进行迭代，调用一个定制的迭代钩子，对对象的每个不同属性的值执行语句。

好的...百米...但是那个可迭代的对象是什么？

我们几乎每次编码都使用迭代...与类似的东西:*映射，过滤，for，forEach，ecc...*

但是*对于...*的陈述是特殊的，因为:

> 迭代器(...)将迭代的概念直接引入核心语言，并提供一种机制来定制...的循环。

所以你可以认为*为...循环知道如何迭代一个数组...但那不是真的！。
我们需要**迭代器**对象作为“接口”来遍历它:*

<center>*for...of <------> **ITERATOR** <------ array*</center>

现在我们知道了这一点...让我们编码吧:

```
const dogs = ["labrador","chow-chow","cavalier"];

for (let breed of dogs)
    {
        console.log(breed);
    }
//-> labrador, chow-chow, cavalier 
```

Enter fullscreen mode Exit fullscreen mode

在那后面...我们有看起来像这样的**迭代器**对象:

```
const dogs = ["labrador","chow-chow","cavalier"];

let iterator = dogs[Symbol.iterator]();

for (let breed of iterator )
    {
        console.log(breed);
    }

//-> labrador, chow-chow, cavalier (the same result) 
```

Enter fullscreen mode Exit fullscreen mode

### 等待...什么？

<center>![what](img/bbc1fac56fe723f9ccfd70085d7d0cda.png)</center>

是...这看起来很奇怪，但也没那么糟糕。
让我们再次引用:

> 为了可迭代，一个对象必须实现 **@@iterator** 方法，这意味着该对象(或其原型链上的一个对象)必须有一个带有**符号的属性。这个函数应该为每个调用返回一个新的迭代器，但是，这不是必需的。**

那个`Symbol.iterator`是每个**可迭代**对象需要的属性，以循环遍历它。

可迭代对象可以是:

字符串
数组
映射
集合
节点列表
...

所有这些都拥有幕后的财产。

# 符号迭代器

该属性为我们提供了`next()`方法，其工作方式如下:

```
const dogs = ["labrador","chow-chow","cavalier"];

let iterator = dogs[Symbol.iterator]();

console.log(iterator.next())

//-> {value: "labrador", done: false} 
```

Enter fullscreen mode Exit fullscreen mode

每次我们调用`next()`方法时，我们循环遍历**可迭代的**对象。

现在我们已经得到了两个键:
*值:【拉布拉多】*
(**iterable**对象的当前值)
*done: false*
(将为 *false* )直到我们得到它里面的值！)

```
const dogs = ["labrador","chow-chow","cavalier"];

let iterator = dogs[Symbol.iterator]();

console.log(iterator.next())
//-> {value: "labrador", done: false}
console.log(iterator.next())
//-> {value: "chow-chow", done: false}
console.log(iterator.next())
//-> {value: "cavalier", done: false}
console.log(iterator.next())
//-> {value: undefined, done: true} 
```

Enter fullscreen mode Exit fullscreen mode

`done:true`是**可迭代**对象的终点。

在 *JS* 中还有一些其他强大的工具需要对象是**可迭代**

```
//TAKE A LOOK AT THE SPREAD OPERATOR...

const number = 31;

let count = [...dogs];

//-> TypeError: dogs is not iterable

const dogs = "labrador";

let doggy = [...dogs];

console.log(doggy)

//-> ["l", "a", "b", "r", "a", "d", "o", "r"] 
```

Enter fullscreen mode Exit fullscreen mode

你必须*始终*小心这一点，并且只使用**可迭代**对象和类似的工具！。

<center>![danger](img/3e805ed35a0273221046d38192888aa2.png)</center>

我想说*拜拜*加点料:

`Symbol.iterator`是一个属性...**所以**...如果你愿意，你可以创建自己的**可迭代**对象！

欲了解更多信息:

[强大的 MDN 指南](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators)

<center>**Have fun with it!**</center>