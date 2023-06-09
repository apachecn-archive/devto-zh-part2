# JavaScript 中的迭代器

> 原文：<https://dev.to/akshendra/iterators-in-javascript-3kg9>

很多次你醒来，意识到今天你将遍历一个数组，或者可能是很多个数组。但是你不要担心，你以前做过，并没有那么难。你也有很多选择，你可以用好的老循环，或者精彩的`map`、`reduce`、`fitler`。或者可以用`iterators`。

是一种允许我们遍历列表或集合的设计模式。在 JavaScript 中，像大多数东西一样，它们被实现为对象。在详细介绍之前，这里有一个简单的例子。

```
const arr = [1, 2, 3, 4, 5];

for (const num of arr) {
  console.log(num);
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`for..of`循环，您可以迭代任何实现了`iterable`协议的`object`。

### `Iterable`协议

为了遵循这个协议，对象必须定义一个特殊的方法`@@iterator`(作为`Symbol.iterator`键)，该方法采用零参数并返回一个对象，该对象本身应该遵循`iterator`协议。

### `Iterator`协议

为了遵循这个协议，对象必须定义一个名为`next`的方法，该方法本身返回一个具有两个属性的对象:

1.  `value`:当前迭代项
2.  `done`:boolean，表示迭代是否完成。`done=true`表示迭代完成。

> 数组、字符串、映射、集合、类型始终遵循迭代器协议。

### 实施协议

这是一个返回`iterable`的函数，它允许我们迭代第一个`n`自然数。

```
function numbers(till = 100) {
  let i = 0;
  const iteratorFx = () => {
    const iterator = {
      next() {
        i += 1;
        if (i <= till) {
          return { done: false, value: i };
        }
        return { done: true };
      },
    };
    return iterator;
  };
  return {
    [Symbol.iterator]: iteratorFx,
  };
}

const numbersTill10 = numbers(10);
for (const i for numbersTill10) {
  // 1, 2, 3, 4, 5, 6, 7, 8, 9, 10
} 
```

Enter fullscreen mode Exit fullscreen mode

> `@@iterator`方法只在`for..of`循环开始时调用一次。所以下面同上

```
for (const i for numbers(10)) {
  // 1, 2, 3, 4, 5, 6, 7, 8, 9, 10
} 
```

Enter fullscreen mode Exit fullscreen mode

因为我们知道对象有一个实现迭代细节的`next`方法，我们可以简单地自己调用这个方法。

```
const numbersTill5 = number(5);
numbersTill5.next(); // { done: false, value : 1 }
numbersTill5.next(); // { done: false, value : 2 }
numbersTill5.next(); // { done: false, value : 3 }
numbersTill5.next(); // { done: false, value : 4 }
numbersTill5.next(); // { done: false, value : 5 }
numbersTill5.next(); // { done: true } 
```

Enter fullscreen mode Exit fullscreen mode

> 通过将值传递给`next`方法，这可以用来从外部控制迭代。

我们可以像上面一样实现我们的自定义迭代器。然而，JavaScript 提供了另一种创建`iterables`的方法。

## 发电机

生成器是特殊的函数，当被调用时返回一个`Generator`对象。`generator`对象遵循迭代协议。所以要用发电机实现上面的例子，

```
function* generateNumbers(till = 100) {
  let i = 1;
  while (i <= till) {
    yield i;
    i += 1;
  }
}

const numbersTill10 = generateNumbers(10); // iterator
// rest is same 
```

Enter fullscreen mode Exit fullscreen mode

由`yield`(这里是`i`)发送的值，将是存储在由`next`方法返回的对象中的`value`。当生成器完成时，它返回`{ done: true }`。

从上面的例子可以清楚地看出，生成器提供了一种创建`iterables`的简洁方法。他们抽象出协议，我们只需要担心迭代逻辑。

## 结论

因为我们在这篇文章的开头夸张地描述了遍历数组。我们包括一个涉及数组的例子是公平的。`Arrays`已经是`iterable`，所以我们将创建一个`iterable`值映射器。

```
function* mapOver(arr, mapper = (v) => v) {
  for (let i = 0; i < arr.length; i += 1) {
    yield mapper(arr[i]);
  }
}

const twices = mapOver([...numbers(5)], (v) => v + 2);
for (const num of twices) {
  // 2, 4, 6, 8, 10
} 
```

Enter fullscreen mode Exit fullscreen mode

> [流传](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)(...)运算符，根据定义，它作用于`iterables`。