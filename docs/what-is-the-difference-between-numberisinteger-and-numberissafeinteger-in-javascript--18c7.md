# JavaScript 中 Number.isInteger()和 Number.isSafeInteger()有什么区别？

> 原文：<https://dev.to/moreonfew/what-is-the-difference-between-numberisinteger-and-numberissafeinteger-in-javascript--18c7>

作为 ES6(也称为 ECMAScript 2015 或 ECMAScript 6)的一部分，引入了一些新的 JavaScript 特性和方法。今天我们将只关注其中的两个，即`Number.isInteger()`和`Number.isSafeInteger()`。

## 什么是`Number.isInteger()`？

`Number.isInteger()`是作为 ES6 的一部分引入的 Number 对象的新方法。这是一个有趣而有用的特性，可以帮助你节省一些数学运算的时间。`Number.isInteger()`方法基本上告诉你作为参数传递给方法的数字是否是整数。比如说，如果我传递一个整数给这个方法，它会返回一个布尔值 true。但是，如果我向它传递任何东西，它将返回 false。让我们来看一个例子:

```
Number.isInteger(10);
//returns true

Number.isInteger(13.5);
//returns false 
```

Enter fullscreen mode Exit fullscreen mode

请注意，`Number.isInteger()`方法总是返回一个布尔值。

`Number.isInteger()`不应与`parseInt()`方法混淆。`parseInt()`方法将参数字符串解析为一个数字(假设参数‘number’的数据类型是 string，并且您希望 JavaScript 将其理解为一个整数)，并将值作为一个整数返回。如果它不能将其解析为数字，它将返回 NaN(不是数字)。`Number.isInteger()`另一方面，检查传递给它的参数号是否已经是一个整数，并相应地返回 true 或 false。

## 什么是`Number.isSafeInteger()`？

另一方面，`Number.isSafeInteger()`是一个与`Number.isInteger()`非常相似的方法，然而，它检查值是否是一个“安全整数”，安全整数是从-(2<sup>53</sup>–1)到+(2<sup>53</sup>–1)的所有整数，即-9007199254740991 到 9007199254740991。我们来看一个例子:

```
Number.isSafeInteger(10); // returns true

Number.isSafeInteger(12345678901234567890); // returns false

Number.isSafeInteger(9007199254740991); // returns true

Number.isSafeInteger(9007199254740992); // returns false 
```

Enter fullscreen mode Exit fullscreen mode

请注意`Number.isSafeInteger()`也总是返回一个布尔值(真或假)。

您可以使用 number 对象的两个新属性来找出安全整数的范围，这两个属性是`Number.MIN_SAFE_INTEGER`(返回-9007199254740991)和`Number.MAX_SAFE_INTEGER`(返回 9007199254740991)。

## 但是，究竟什么是安全的整数呢？

“安全整数”不是一个数学术语，因此如果你问你的数学天才朋友，他/她可能根本不知道。这里的“安全整数”指的是在 JavaScript 中安全稳定地使用的整数。JavaScript 数字的存储空间只够表示-(2<sup>53</sup>–1)到+(2<sup>53</sup>–1)范围内的整数，超过这个范围，JavaScript 整数就不安全或不稳定了。除了 2 <sup>53</sup> ，JavaScript 只能代表每秒一个的数学整数。例如:

```
console.log(Math.pow(2, 53));
//9007199254740992

console.log(9007199254740992);
//9007199254740992

console.log(9007199254740993);
// 9007199254740992 Surprising!!

console.log(9007199254740994);
//9007199254740994 fine !

console.log(9007199254740995);
// 9007199254740996 
```

Enter fullscreen mode Exit fullscreen mode

因此，你需要`Number.isSafeInteger()`找出这个整数在 JavaScript 的上下文中是否安全。

ES6 还引入了其他新的数学和数字相关的属性和方法，我建议你也和`Number.isInteger()`一起尝试一下；和`Number.isSafeInteger()`方法。如果你今天学到了什么新东西，一定要让我们知道。

帖子[JavaScript 中 Number.isInteger()和 Number.isSafeInteger()有什么区别？](https://www.moreonfew.com/difference-between-number-isinteger-and-number-issafeinteger-in-javascript/)最早出现在 [MoreOnFew](https://www.moreonfew.com) 上。