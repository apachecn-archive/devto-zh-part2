# JavaScript 中的短路分配

> 原文：<https://dev.to/flexdinesh/short-circuit-assignment-in-javascript--4k80>

[![JS short circuit assignment](img/064207f86d67a6006b3fc08faef98342.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_tqdbPet--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://image.ibb.co/c9duVn/short_circuit_alt.jpg)

**tldr；** *当未找到期望值时，使用短路评估将默认值分配给变量。*

JavaScript 很神奇。但是大多数时候，我们最终会为完成一个简单的任务而抓耳挠腮，比如，*当没有找到期望值时(可能是 null/undefined)，就给变量赋值一个默认值。*

就拿这个例子来说吧，

```
const person = {
    name: 'Jack'
};

const name = person.name;
const greetings = 'Hello' + ', ' + name + '!';
console.log(greetings) // => 'Hello, Jack!' 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果姓名键在人对象中不可用，

```
const person = {};

const name = person.name; // name is undefined here
const greetings = 'Hello' + ', ' + name + '!';
console.log(greetings) // => 'Hello, undefined!' 
```

Enter fullscreen mode Exit fullscreen mode

这个问题在 JavaScript 世界非常普遍。我们通常通过指定默认值来处理这种情况。

```
// we either use if case
let name = 'Sunshine'; // default value
if (person && person.name) {
    name = person.name;
}

// or we use ternary operator
const name = person && person.name ? person.name : 'Sunshine';

const greetings = 'Hello' + ', ' + name + '!'; // name will never be undefined now
console.log(greetings) // => 'Hello, Jack!' 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子是一个简单的场景，因为我们只需要检查一个变量。但是如果我们需要基于多个变量进行赋值，那么我们最终会写出混乱的*不易阅读的*代码。

```
let foo, bar, baz;

// if case mess
let name;
if (foo) name = foo;
else if (bar) name = bar;
else if (baz) name = baz;
else name = 'Sunshine';

// ternary operator nightmare
const name = foo ? foo : (bar ? bar : (baz ? baz : 'Sunshine')); 
```

Enter fullscreen mode Exit fullscreen mode

为了将世界从混乱的代码中拯救出来，**还有一个更好的*T2 技巧来分配默认值。***

## 短路赋值✨

这是在 JavaScript 中分配默认值的一种更短、更简洁的方式。这基于逻辑运算符的短路评估，其中第**个*真值*被返回**。

```
const name = person.name || 'Sunshine';

// if you're not sure if person object is defined
const name = (person && person.name) || 'Sunshine';

// can be used with as many variables as needed
let foo, bar, baz;
bar = 'Bar-da-Jack';
// first truthy value will be assigned to name
const name = foo || bar || baz || 'John'; // => name = 'Bar-da-Jack' 
```

Enter fullscreen mode Exit fullscreen mode

当发现真值时，评估电路断开，并返回到赋值。

任何不是`false`、`0`、`''`、`""`、`null`、`undefined`和`NaN`的值在 JavaScript 中都被认为是真值。

**注意:** *请记住，如果您期望`0`作为变量中的一个值，这个技巧可能不会像预期的那样工作，因为`0`在 JavaScript 中被认为是虚假的。*

所以，同志们，短路，把 JavaScript 世界从混乱的代码结构中拯救出来，一次一行！🎉