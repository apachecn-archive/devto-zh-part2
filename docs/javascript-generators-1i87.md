# JavaScript 生成器

> 原文：<https://dev.to/ajmeyghani/javascript-generators-1i87>

[![cover](img/72b4be0fd8b5b2151750ea8d1566ec33.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ckDmSwd1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1485075792961-9ea033ad04fc%3Fixlib%3Drb-0.3.5%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ%26s%3D27199f55b7e17b540bb68285c450ce41)

> 下面是我的免费电子书 Async JavaScript 的摘录。你可以阅读关于 https://asyncjsbook.com 的整本书

生成器是特殊的函数，当你需要它们的时候，它会生成值。当你调用一个生成器时，它不会像普通函数一样执行。它将执行到看到一个`yield`语句的时候，然后退出，直到你需要一个新的值。当您想要一个新值时，您向生成器请求下一个值，它将从停止的地方再次执行该函数，直到没有更多的值要生成。在接下来的部分中，我们将学习如何创建生成器，如何迭代它们，如何停止它们等等。

## 创建发电机

您可以通过在函数关键字:
后放置一个`*`来创建一个生成器

```
function* myGenerator() {
 //...
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，在生成器函数的主体中，我们可以使用`yield`语句生成值:

```
// [file]: code/generators/simple.js
function* simpleGenerator() {
  yield 1;
  yield 5;
}
const g = simpleGenerator();
const v1 = g.next().value; // --> 1
const v2 = g.next().value; // --> 5
const v3 = g.next().value; // --> undefined 
```

Enter fullscreen mode Exit fullscreen mode

你甚至可以定义一个无限循环并生成值:

```
// [file]: code/generators/inf-loop.js
function* myGenerator() {
  let i = 0;
  while(true) {
    i += 1;
    yield i;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果这是一个普通函数，它会陷入无限循环。但是因为这是一个生成器，我们可以通过调用返回的生成器对象 next 来读取生成的值:

```
const g = myGenerator();
const v1 = g.next(); // --> { value: 1, done: false }
const v2 = g.next(); // --> { value: 2, done: false }
const v3 = g.next(); // --> { value: 3, done: false }
// and so on... 
```

Enter fullscreen mode Exit fullscreen mode

本质上，我们每次调用`next`时都要进入和退出函数，并从上次停止的地方重新开始。注意每次我们调用 next 时,`i`的值是如何被“记住”的。现在让我们更新上面的代码，让生成器完成值的生成。如果`i`大于`2` :
，我们就让它不产生任何值

```
function* myGenerator() {
  let i = 0;
  while(true) {
    i += 1;
    if(i > 2) {
      return;
    }
    yield i;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

或者我们可以简化上面的代码，将条件移到 while 循环:

```
// [file]: code/generators/inf-loop-terminate.js
function* myGenerator() {
  let i = 0;
  while(i < 2) {
    i += 1;
    yield i;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们读取生成的值，我们只会得到两个值:

```
const g = myGenerator();
const v1 = g.next(); // --> { value: 1, done: false }
const v2 = g.next(); // --> { value: 2, done: false }
const v3 = g.next(); // --> { value: undefined, done: true } 
```

Enter fullscreen mode Exit fullscreen mode

注意，在第二个值之后，如果我们继续调用 next，我们将得到相同的结果。也就是说，一个值为`undefined`且`done`属性设置为`true`的生成器对象，表示将不再生成任何值。

## 返回报表

生成器中的`return`语句标记最后一个值，之后不会生成任何值:

```
// [file]: code/generators/return-statement.js
function* withReturn() {
  yield 1;
  yield 55;
  return 250;
  yield 500;
}
const g = withReturn();
const v1 = g.next().value; // --> 1
const v2 = g.next().value; // --> 55
const v3 = g.next().value; // --> 250
const v4 = g.next().value; // --> undefined 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码会生成`1`、`55`和`250`。它不会到达最后的`yield`语句，因为`return`语句标志着生成器的结束。

## 将值传递给下一个

使用生成器，您可以向`next`回调传递一个值，以代替之前计算的`yield`语句。让我们看一个简单的例子来说明这是什么意思。

```
// [file]: code/generators/pass-next.js
function* myGenerator(n) {
  const a = (yield 10) + n;
  yield a;
}

const g = myGenerator(1);
g.next().value; // --> 10
g.next(100).value; // --> 101 
```

Enter fullscreen mode Exit fullscreen mode

让我们仔细阅读上面的片段，一步一步地探索发生了什么:

*   首先我们调用生成器，为`n`传递`1`，并将迭代器对象存储在`g`中。这里没什么新鲜的。
*   然后，我们调用`g.next`来启动发电机。该函数一直执行到第一个`yield`语句:`const a = (yield 10)`。此时会生成`yeild`旁边的值，即`10`。
*   然后我们调用`g.next`并传递`100`。该功能从它停止的地方恢复:`+ n`，但是它将把`(yield 10`替换为`100`，产生`const a = 100 + n`，其中`n`是`1`。它将继续下去，直到碰到下一个`yield`。在这种情况下`yield a`将产生`100 + 1 = 101`。

我们将在后面的章节中使用生成器的这种特殊行为来实现一个助手来处理异步流。

## 调用一台发电机内的另一台发电机

如果想调用另一个生成器，可以在生成器内部使用`yield*`。在下面的例子中，我们有两个生成器，`g1`和`g2`。我们想在`g1`中调用`g2`，并读取生成的值:

```
// [file]: code/generators/call-another.js
function* g2() {
  yield 2;
  yield 3;
}
function* g1() {
  yield 1;
  yield* g2();
  yield 4;
}

const vals = [...g1()];

console.log(vals); // -> [1,2,3,4] 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，我们调用了`g1`生成器，下面是发生的事情的摘要:

*   `1`值由第一条`yield`语句生成
*   接下来，我们点击`yield* g2()`，它将生成`g2`将生成的所有值，即`2`和`3`
*   接下来，我们回到`g1`并生成最终值，即`4`

## 遍历值

### 使用`for-of`

由于生成器函数返回一个 iterable，我们可以使用`for-of`循环来读取每个生成的值。使用上面的简单生成器，我们可以编写一个循环来记录每个生成的值:

```
// [file]: code/generators/use-for-of.js
function* myGenerator() {
  let i = 0;
  while(i < 2) {
    i += 1;
    yield i;
  }
}

const g = myGenerator();
for(const v of g) {
  console.log(v);
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码会输出`1`，然后输出`2`。

### 使用`while`循环

您还可以使用一个`while`循环来遍历一个生成器对象:

```
// [file]: code/generators/use-while-loop.js
const g = myGenerator();
let next = g.next().value;
while(next) {
  console.log(next);
  next = g.next().value;
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的`while`循环中，首先我们获得第一个生成的值，并将其分配给`next`。然后在`while`循环中，我们将`next`设置为下一个生成的值。当发生器产生最后一个值时，`while`循环将继续，直到`next`变为未定义。

### 传播算子和`Array.from`

因为生成器对象是可迭代的，所以你也可以使用 spread 操作符来读取值:

```
// [file]: code/generators/use-spread.js
function* myGenerator() {
  let i = 0;
  while(i < 2) {
    i += 1;
    yield i;
  }
}
const vals = [...myGenerator()]; // -> [1, 2] 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们首先调用生成器`myGenerator()`，并把它放在一个数组中。最后，我们在它之前使用 spread 操作符来读取每个值。结果以两个值`[1, 2]`的数组形式存储在`vals`变量中。

除了 spread 操作符之外，您还可以使用`Array.from`方法读取值并将它们放入一个数组:

```
// [file]: code/generators/use-array-from.js
function* myGenerator() {
  let i = 0;
  while(i < 2) {
    i += 1;
    yield i;
  }
}
const vals = Array.from(myGenerator()); // --> [1, 2] 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，我们调用生成器并将其传递给`Array.from`，它将读取每个值并将它们存储在一个数组中，从而产生`[1, 2]`。

值得一提的是，如果您正在遍历一个包含终止序列的 return 语句的生成器对象，那么如果您使用任何内部迭代方法，如`for-of`循环或 spread 运算符:
，您将无法读取最后一个值

```
function* withReturn() {
  yield 1;
  yield 55;
  return 250;
  yield 500;
}
for(const v of withReturn()) {
  console.log(v);
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码会输出`1`，然后输出`55`，但不会输出`250`。如果您使用 spread 运算符:
，情况也是如此

```
function* withReturn() {
  yield 1;
  yield 55;
  return 250;
  yield 500;
}
const vals = [...withReturn()];
console.log(vals); 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码会输出`[1, 55]`，不包括`250`。但是请注意，如果我们使用一个`while`循环，我们可以读取所有的值，直到返回语句中的值:

```
function* withReturn() {
  yield 1;
  yield 55;
  return 250;
  yield 500;
}

const g = withReturn();
let next = g.next().value;

while(next) {
  console.log(next);
  next = g.next().value;
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的`while`循环将读取所有值，包括 return 语句中的值，将`1`、`55`和`250`记录到控制台。

## 生成无限序列

在这一节中，我们将看看如何使用一个生成函数来创建一个斐波那契数列。请注意，本节中使用的代码仅用于演示目的。出于实用目的，您可能希望使用预先生成的列表来检索值，以获得更好的性能。

斐波纳契数列是以 0 和 1 开始的数字序列。序列中剩余的数字通过将当前值与前一个值相加来计算:

```
0, 1, 1, 2, 3, 5, 8, 13, 21, ... 
```

Enter fullscreen mode Exit fullscreen mode

或者递归地，序列可以被定义为:

```
fib(n) = fib(n - 1) + fib(n - 2) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用上面的定义并定义一个生成器来产生`n`个值:

```
// [file]: code/generators/fibo.js
function* fibo(n, prev = 0, current = 1) {
  if (n === 0) {
    return prev;
  }
  yield prev;
  yield* fibo(n - 1, current, prev + current);
}

let vals = [...fibo(5)];
console.log(vals); //-> [ 0, 1, 1, 2, 3 ] 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，我们使用`prev = 0`和`current = 1`将前两个数字定义为默认参数值。下面是对`n = 5`发生的事情的总结:

1.  第一个`yield`将产生 prev 值，即`0`。注意`n`现在是`4`。
2.  接下来，`fibo(4 - 1, 1, 0 + 1) = fib(3, 1, 1)`会生成`1`。
3.  接下来，`fibo(3 - 1, 1, 1 + 1) = fibo(2, 1, 2)`会生成`1`。
4.  接下来，`fibo(2 - 1, 2, 1 + 2) = fibo(1, 2, 3)`会生成`2`。
5.  接下来，`fibo(1 - 1, 3, 2 + 3) = fibo(0, 3, 5)`将生成`3`，标志着结束，因为`n`是`0`，我们点击返回语句。

## 发电机和异步运行

我们可以利用生成器的独特特性，在转移到函数的其他部分之前等待异步操作完成。在本节中，我们将编写一个助手函数来实现这一点。但是，首先让我们回顾一下当你通过一个论点时会发生什么。如果你还记得前面的章节，如果你传递一个参数给`g.next`，它将用之前产生的结果来替换给定值:

```
function* myGenerator(n) {
  const a = (yield 10) + n;
  yield a;
}

const g = myGenerator(1);
g.next().value; // --> 10
g.next(100).value; // --> 101 
```

Enter fullscreen mode Exit fullscreen mode

我们将把它作为我们助手函数的基础。现在，让我们首先创建一个异步函数，它返回一个承诺:

```
const asynTask1 = () => new Promise((r, j) => setTimeout(() => r(1), 1000)); 
```

Enter fullscreen mode Exit fullscreen mode

该函数返回一个承诺，该承诺在 1 秒钟后解析为值`1`。现在，让我们创建一个生成器函数，并在其中调用我们的异步函数:

```
const asynTask1 = () => new Promise((r, j) => setTimeout(() => r(1), 1000));

function* main() {
  const result = yield asynTask1();
}

const g = main();
console.log(g.next()); 
```

Enter fullscreen mode Exit fullscreen mode

你觉得上面的代码会输出什么？让我们仔细检查一下，看看会发生什么:

*   首先，我们调用生成器并将生成器对象存储在`g`中。
*   然后，我们调用`next`来获得第一个`yield`结果。在这种情况下，这将是一个承诺，因为`asynTask1`返回承诺。
*   最后，我们将该值记录到控制台:`{ value: Promise { <pending> }, done: false }`。
*   1 秒钟后，程序结束。

程序结束后，我们将无法访问解析后的值。但是想象一下，如果我们可以再次调用`next`并在“正确的”时间将解析的值传递给它。在这种情况下，`yield asynTask1()`将被解析后的值替换，并被分配给`result`！让我们更新上面的代码，用一个承诺来实现它:

```
const asynTask1 = () => new Promise((r, j) => setTimeout(() => r(1), 1000));

function* main() {
  const result = yield asynTask1();
  return result; //<-- return the resolved value and mark the end.
}

const g = main();
const next = g.next();
console.log(next); // --> { value: Promise { <pending> }, done: false }
next.value.then(v => { // Resolve promise.
  const r = g.next(v); // passing the resolved value to next.
  console.log(r); // -> { value: 1, done: true }
}); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，我们在生成器中添加了一个 return 语句，以简单地返回解析后的值。但重要的是我们何时兑现承诺。当我们解析承诺时，我们调用`g.next(v)`，它用解析的值替换`yield asynTask1()`，并将它分配给`result`。现在，我们准备编写我们的助手函数。这个助手函数将接受一个生成器，并执行我们上面讨论的内容。如果没有更多的值要生成，它将返回解析的值。我们将从定义助手函数开始:

```
const helper = (gen) => {
  const g = gen();
}; 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止没什么特别的，我们给助手传递了一个生成器函数，在助手内部我们调用生成器并将生成器对象分配给`g`。接下来，我们需要定义一个函数来为我们处理调用 next:

```
const helper = (gen) => {
  const g = gen();
  function callNext(resolved) {
    const next = g.next(resolved); // replace the last yield with the resolved value
    if(next.done) return next.value; // return the resolved value if not more items
    return next.value.then(callNext); // pass `callNext` back again.
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

这个函数将接受一个参数，一个承诺的解析值。然后，我们用解析后的值调用`g.next`，并将结果赋给`next`变量。之后，我们将检查发电机是否完成。如果是这样，我们将简单地返回值。最后，我们调用`next.value.then()`，并将`callNext`传回给它，以便递归调用下一个，直到不再有值生成。现在，要使用这个助手函数，我们只需调用它，并将我们的生成器传递给它:

```
helper(function* main() {
  const a = yield asynTask1();
  console.log(a);
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您运行上面的代码，您将看不到记录的结果，这是因为我们缺少了一部分。我们 helper 中的`callNext`函数需要立即自调用，否则没人会调用:

```
const helper = (gen) => {
  const g = gen();
  (function callNext(resolved) {
    const next = g.next(resolved);
    if(next.done) return next.value;
    return next.value.then(callNext);
  }()); // <-- self invoking
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了我们的帮助器函数，让我们在帮助器中抛出一个错误，以便我们稍后可以捕获它:

```
const helper = (gen) => {
  const g = gen();
  (function callNext(resolved) {
    const next = g.next(resolved);
    if(next.done) return next.value;
    return next.value.then(callNext)
    .catch(err => g.throw(err)); // <-- throw error
  }());
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果任何承诺出错，那么`catch`块将从生成器中抛出一个错误。我们可以简单地在传入的生成器函数中使用 try-catch 来处理错误。将所有这些放在一起，我们将有:

```
// [file]: code/generators/async-flow.js
const asynTask1 = () => new Promise((r, j) => setTimeout(() => r(1), 1000));
const asynTask2 = () => new Promise((r, j) => setTimeout(() => j(new Error('e')), 500));

const helper = (gen) => {
  const g = gen();
  (function callNext(resolved) {
    const next = g.next(resolved);
    if(next.done) return next.value;
    return next.value.then(callNext)
    .catch(err => g.throw(err));
  }());
};

helper(function* main() {
  try {
    const a = yield asynTask1();
    const b = yield asynTask2();
    console.log(a, b);
  } catch(e) {
    console.log('error happened', e);
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果你好奇，你可以看一看 [co](https://github.com/tj/co) 库，以获得更全面的实现。然而，我们将在下一章中关注`async-await`抽象，它是一个用于处理异步流的生成器的本地抽象。