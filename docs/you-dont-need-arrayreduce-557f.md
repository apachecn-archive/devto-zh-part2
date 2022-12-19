# 不需要 Array.reduce()

> 原文：<https://dev.to/trusktr/you-dont-need-arrayreduce-557f>

我正在阅读另一篇 dev.to 帖子，[揭开`Array.reduce()`](https://dev.to/nickyhajal/demystifying-arrayreduce-how-to-understand-it-and-when-to-use-it-in-your-code-5b96) 的神秘面纱，但是我对使用`Array.reduce()`并不信服。

也许我也没有以正确的方式使用`Array.reduce()`,但是每次我这样做的时候，我最终都不喜欢它并切换到一个简单的`for..of`循环。

前面是那篇文章中的三个例子，被转换成使用`for..of`并且在我看来更容易阅读和更干净。

以 sum 为例:

```
const array = [1, 2, 3, 4];
const sum = array.reduce((accumulator, currentItem) => {
    return accumulator + currentItem;
}, 0);

// sum = 10 
```

它可以写成

```
const array = [1, 2, 3, 4]
let sum = 0
for (const n of array) sum += n

// sum = 10 
```

那更简单！

下一个例子，

```
const trips = [{type: 'car', dist: 42}, {type: 'foot', dist: 3}, {type:'flight', dist: 212}, {type: 'car', dist: 90}, {type: 'foot', dist: 7}] 

const distanceByType = trip.reduce((out, curr) => {
    const { type, dist } = curr;
    if (out[type]) {
        out[type] += dist;
    } else {
        out[type] = dist;
    }
    return out;
}, {});

// distanceByType = {car: 132, foot: 10, flight: 212}; 
```

可以改写为

```
const trips = [{type: 'car', dist: 42}, {type: 'foot', dist: 3}, {type:'flight', dist: 212}, {type: 'car', dist: 90}, {type: 'foot', dist: 7}] 
const distanceByType = {}

for (const trip of trips) {
  const { type, dist } = trip
  if (distanceByType[type]) {
    distanceByType[type] += dist
  } else {
    distanceByType[type] = dist
  }
}

// distanceByType = {car: 132, foot: 10, flight: 212} 
```

简单！

最后，关于管道功能的注释中的例子，

```
const pipeOnce = (fn1, fn2) => (args) => (fn2(fn1(args)));
const pipe = (...ops) => ops.reduce(pipeOnce);

const addTwo = a => a + 2;
const mulTwo = a => a * 2;

const addTwoMulTwo = pipe(addTwo, mulTwo);
console.log(addTwoMulTwo(1));  // (1 + 2) * 2 => 6
console.log(addTwoMulTwo(2));  // (2 + 2) * 2 => 8
console.log(addTwoMulTwo(3));  // (3 + 2) * 2 => 10 
```

是 reduce 的一个更好的例子，但是它可以写成

```
const addTwo = a => a + 2;
const mulTwo = a => a * 2;
const addTwoMulTwo = n => mulTwo(addTwo(n))

console.log(addTwoMulTwo(1));  // (1 + 2) * 2 => 6
console.log(addTwoMulTwo(2));  // (2 + 2) * 2 => 8
console.log(addTwoMulTwo(3));  // (3 + 2) * 2 => 10 
```

如果我们想通过管道传输任意数量的函数，我们也可以用`for..of`来实现:

```
const pipe = (...fns) => arg => {
  for (const fn of fns) arg = fn(arg)
  return arg
}

const addTwoMulTwo = pipe(addTwo, mulTwo) 
```

这个没有那么短，但是更容易理解。

有哪些`Array.reduce()` *真的*胜过`for..of`的用例？

知道的请分享！