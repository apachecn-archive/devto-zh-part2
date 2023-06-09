# JavaScript 在 JavaScript 中省略三个点(…)

> 原文：<https://dev.to/sagar/three-dots---in-javascript-26ci>

> 嘿！我是萨加尔。我喜欢写教程和文章来帮助开发者更好地理解 JavaScript 的魔力。如果你对这篇文章有任何问题，请留下评论，我会尽快回复你，或者在 twitter 上找到我。

在本文中，我们将讨论 ES6 中引入的一个特性，即 spread 操作符和 rest 操作符。🔥 🔥 🔥

我非常喜欢这三点，它们可能会改变你在 JavaScript 中解决问题的方式。我们可以用三个点……以两种不同的方式作为扩展操作符和剩余操作符。

## 休息参数😴

使用 rest 参数，我们可以将任意数量的参数聚集到一个数组中，并对它们做我们想做的事情。引入 Rest 参数是为了减少由参数引起的样板代码。🙌

```
function myFunc(a, b, ...args) {
 console.log(a); // 22
 console.log(b); // 98
 console.log(args); // [43, 3, 26]
};
myFunc(22, 98, 43, 3, 26); 
```

Enter fullscreen mode Exit fullscreen mode

在 myFunc 的最后一个带…前缀的参数中，这将导致所有剩余的参数都放在 javascript 数组中。

rest 参数收集所有剩余的参数，因此没有意义😕在最后一个参数之前添加 rest 参数。Rest 参数必须是最后一个形参。

```
function myFunc(arg1, ...rest, arg2) {
  // arg2 ?
} 
```

Enter fullscreen mode Exit fullscreen mode

Rest 参数可以被析构(仅数组)，这意味着它们的数据可以被解包到不同的变量中。

```
function myFunc(...[x, y, z]) {
  return x * y* z;
}

myFunc(1)          // NaN
myFunc(1, 2, 3)    // 6
myFunc(1, 2, 3, 4) // 6 (fourth parameter is not destructured) 
```

Enter fullscreen mode Exit fullscreen mode

## 传播算子✨

spread 运算符用于将 iterable(如数组)的元素扩展到可以容纳多个元素的地方。

```
function myFunc(x, y, ...params) { // used rest operator here
  console.log(x);
  console.log(y);
  console.log(params);
}

var inputs = ["a", "b", "c", "d", "e", "f"];
myFunc(...inputs); // used spread operator here
// "a"
// "b"
// ["c", "d", "e", "f"] 
```

Enter fullscreen mode Exit fullscreen mode

组合数组的方式一直有很多种，但是 spread 运算符给出了一种组合数组的新方法:

```
const featured = ['Deep Dish', 'Pepperoni', 'Hawaiian'];
const specialty = ['Meatzza', 'Spicy Mama', 'Margherita'];

const pizzas = [...featured, 'veg pizza', ...specialty];

console.log(pizzas); // 'Deep Dish', 'Pepperoni', 'Hawaiian', 'veg pizza', 'Meatzza', 'Spicy Mama', 'Margherita' 
```

Enter fullscreen mode Exit fullscreen mode

有了 spread 运算符，现在可以使用比 Object.assign()更短的语法进行浅层克隆(不包括原型)或合并对象。

```
var obj1 = { foo: 'bar', x: 42 };
var obj2 = { foo: 'baz', y: 13 };

var clonedObj = { ...obj1 };
// Object { foo: "bar", x: 42 }

var mergedObj = { ...obj1, ...obj2 };
// Object { foo: "baz", x: 42, y: 13 } 
```

Enter fullscreen mode Exit fullscreen mode

## 👉结论

当我们在代码中看到三个点(…)时，它要么是 rest 参数，要么是 spread 操作符。

有一种简单的方法来区分它们:

1.  当三个点(…)位于函数参数的末尾时，它是“rest parameters ”,并将参数列表的其余部分聚集到一个数组中。

2.  当三个点(…)出现在一个函数调用或类似的调用中时，它被称为“扩展操作符”并将一个数组扩展为一个列表。

感谢阅读。我希望你喜欢这篇文章，请随意喜欢、评论或与你的朋友分享这篇文章。

😄快乐编码…