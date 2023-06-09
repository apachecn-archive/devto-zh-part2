# 我如何学会停止循环并爱上迭代器

> 原文：<https://dev.to/kepta/how-i-learned-to-stop-looping-and-love-the-iterator-463j>

让我们都从繁忙的生活中抽出一分钟来回顾过去，想想我们浪费了多少时间来修理那个东西。如果你的内存返回一个空数组，看看这个。 **(* *剧透预警！* *，它将一个数组中两个连续的元素相加，对于第一个元素，它将其与最后一个元素配对。)**T9】

```
 for (var i = 0, len = grid.length, j = len - 1, p1, p2; i < len; j = i++) {
    p1 = grid[i];
    p2 = grid[j];
    sum += p1 + p2;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 问题与`for loop`

最大的问题，在我看来，就是他们太强大了。你可以很容易地以一个错误的`for loop`配置结束，它可能会欺骗性地工作，只是在你的生产栈中爆炸。

> 简而言之，`for loop`的表面积如此之大，简直就是 bug 磁铁。

## 功能替代

90%的情况下，你总能把那辆老爷车`for loop`变成好看的`.map` / `.reduce`。这种方法很少有出错的地方。

```
for (var i = 0; i < array.length; i++) {
    array[i] = array[i] * 2;
}

array = array.map(o => o * 2); 
```

Enter fullscreen mode Exit fullscreen mode

## 可迭代的方法

一个实用的替代品应该是你摆脱`for loop` s 的第一选择，但是有时候你想坚持它:

1.  你觉得性能很重要。For 循环仍然是最快的，但只有很小的差距。
2.  你想轻松地脱离循环。
3.  某些复杂的对象(`Sets`、`Map`、`NodeList`)并没有直接的循环方式。

聪明的 ECMAScript 人员知道这些限制，可能还有更多，所以他们在 ES2015 中提出了一个迭代协议。

### 究竟什么是协议？

Javascript 不像其他语言那样有正式的协议。可以把它看作一种约定，就像节点程序员喜欢遵循错误优先回调一样。

## 引入可重复项

javascript 中到处都是可迭代对象，你一直在不知不觉中使用它们。任何具有`Symbol.iterator`属性的东西都是可迭代的。

让我们看看最简单的 iterable，一个字符串！

```
 str = 'hello world';
iterator = str[Symbol.iterator](); // StringIterator {}
iterator.next(); // gives `h`
...
...
iterator.next(); // gives `d`
iterator.next(); // gives `undefined` as no more string left. 
```

Enter fullscreen mode Exit fullscreen mode

javascript 中的字符串类型与迭代协议结合在一起，这意味着我们现在可以说字符串是可迭代的。

### [T1】什么是 Symbol 和 Symbol.iterator？](#what-is-symbol-and-symboliterator)

这个主题本身就值得写一篇文章，但简单来说,`Symbol`解决了将一个属性隐藏到一个对象中的问题，而您不希望这个对象与任何现有的属性发生冲突。请访问 [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)了解更多信息

> 所以不要再在你的对象里放`@@@@@\_my\_hidden\_property\_`！

`Symbol.iterator`是一个全局可用的常量，任何人都可以使用并实现迭代协议。所以你可以用它来让你自己的对象实现迭代。

## 如何为我的自定义对象实现迭代协议？

```
class Rand {
  [Symbol.iterator] () {
    let count = 0;
    return {
      next: () => ({ 
        value: count++, 
        done: count > 5
      })
    };
  }
}
var rand = new Rand();
var iterator = rand[Symbol.iterator]();
iterator.next();// {value: 0, done: false}
iterator.next();// {value: 1, done: false}
// .. 
iterator.next();// {value: 5, done: false}
iterator.next();// {value: undefined, done: true} 
```

Enter fullscreen mode Exit fullscreen mode

不要让语法困扰你。让我们来分解一下这个例子:

*   这种奇怪的语法不过是 ES2015 动态初始化属性的一种新方式。 **(点击[查看](https://babeljs.io/docs/plugins/transform-es2015-computed-properties/)更多信息。)**
*   `Symbol.iterator`方法必须返回一个对象`{ next }` **(别忘了这都是约定/协议)** 。我们称这个物体为`iterator`。(下一节将详细介绍)
*   每次被调用时，`.next()`只是增加计数，当`count`超过`5`时，它将`done`切换到`true`。

### `iterable`&`iterator`有什么区别？

跟我重复，

*   **Iterable** 是实现迭代协议的对象。`string`、`Array`、`Set`、`Map`都是可重复的！

```
class Rand {
  [Symbol.iterator] () { // Rand has `Symbol.iterator` method, hence it is an iterable!
    let count = 0;
    return { // The return value is called an `iterator`
      next: () => ({ 
        value: count++, 
        done: count > 5
      })
    };
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   **迭代器**是 iterable 的`[Symbol.iterator]()`返回的东西。
    *   它包含有用的状态信息，比如当前迭代在哪里，下一步提供什么值。
    *   任何迭代器都必须有一个`.next`方法(记住这都是约定？)，它将用于从中获取下一个值。
    *   `.next()`方法返回的对象必须是`{value, done}`，其中`value`是当前值，`done`表示迭代是否结束。

```
var iterator = rand[Symbol.iterator](); // I am an iterator

iterator.next(); //  {value: 0, done: false}
iterator.next(); //  {value: 1, done: false}
...
iterator.next(); //  {value: 4, done: false}
iterator.next(); //  {value: undefined, done: true} 
```

Enter fullscreen mode Exit fullscreen mode

## 我能从这个复杂的协议中得到什么？

如果你在你的自定义对象中启用迭代或者使用任何 Javascript 的内置迭代，比如`Array`、`string`、`Map`或者`Set`，你就可以免费获得很多超能力。

**1。超能力:传播开来**

还记得我们刚刚在上面定义的类`Rand`吗？因为它是可重复的，它继承了传播的超能力。访问 [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)了解更多关于传播的信息。

```
var rand = new Rand();
var myArray = [...rand]; // [0, 1, 2, 3, 4] 

// string can also be used since it is an iterable
[..."kushan"]; // ["k", "u", "s", "h", "a", "n"] 
```

Enter fullscreen mode Exit fullscreen mode

**注意:我们没有做`[...rand[Symbol.iterator]()]`，因为`...`期待的是`iterable`，而不是`iterator`。**

**2。超能力:使用**

```
Array.from(rand); // [0, 1, 2, 3, 4]
Array.from("kushan"); // ["k", "u", "s", "h", "a", "n"] 
```

Enter fullscreen mode Exit fullscreen mode

**3。超级电源:用于循环**

`for of`是 ES2015 引入的新循环机制，只理解 iterables。它自动调用`Symbol.iterator`，将迭代器存储在后台，并为您调用`.next`。当迭代器返回`{done:true}`时也停止。

```
for(const v of rand) {
  console.log(v); 
}
/*Output*/
// 0
// 1 
// ..
// 4 
```

Enter fullscreen mode Exit fullscreen mode

> 与`spread operator`不同，`for of`回路可以接受`iterable`或`iterator`。

```
var map = new Map([['a', 1], ['b', 2]]); 

map[Symbol.iterator];// map is iterable because it has the `Symbol.iterator` key 

// `for of` loop understands `iterable`
for (const [key, val] of map) { 
    console.log(key, val); // 'a', 1
}

// `for of` loop also understands iterators
var iterator = map[Symbol.iterator](); // returns an iterator
for (const [key, val] of iterator) {
    console.log(key, val); // 'a', 1
}

// .keys() is a part of `Map` api
var keyIterator = map.keys();   // returns an iterator
for (const key of keyIterator) {
    console.log(key); // 'a'
}
// .values() is a part of `Map` api
var valueIterator = map.values();   // returns an iterator
for (const val of valueIterator) {
     console.log(val); // 1'
} 
```

Enter fullscreen mode Exit fullscreen mode

**4。超能力:毁灭**

这是我最喜欢展示的`iterables`超能力之一。ES2015 引入了[析构赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)，它建立在 iterables 之上。你只能摧毁可重复的东西！

```
// array is iterable
[a, b] = [10, 20]; // a=10, b=20

// our custom class rand is Destructable :P
[a, b, c] = rand; // a = 0, b = 1, c = 2

// you can do fancy things like
[a, ...b] = rand; // a = 0, b = [1, 2, 3, 4] 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

请分享您在使用`iterables`时免费获得的任何其他超能力。希望这篇文章能帮助你了解`iterables` & `iterators`。

别忘了看看我以前的文章。

*   [Javascript WeakMap](https://dev.to/kepta/javascript-underdogs-part-1---the-weakmap-4jih)
*   [9 大承诺小技巧](https://dev.to/kepta/promising-promise-tips--c8f)

如果你❤️这篇文章，请分享这篇文章传播的话。