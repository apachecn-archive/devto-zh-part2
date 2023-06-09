# isNaN(')= false😅，怎么处理？

> 原文：<https://dev.to/3sanket3/isnan--false--how-to-handle-it-15d9>

我最近注意到在 JavaScript 中`isNaN('')`和`isNaN(null)`都返回了`false`。表示空字符串和`null`都是有效数字。所以如果你想通过使用`isNaN()`比较变量来执行任何数字特定的操作，这是行不通的。下面是一个例子:

```
function formattedAmount(x) {
  if (isNaN(x)) {
    return "Not a Number!";
  }
  return "$ " + x.toFixed(2);
}

console.log(formattedAmount(""));
// output: Error: x.toFixed is not a function

console.log(formattedAmount(null));
// output: Error: Cannot read property 'toFixed' of null 
```

Enter fullscreen mode Exit fullscreen mode

这可以使用`Number()`功能或`+`操作符来解决。它将创建变量`x`的`Number`对象。因此，空字符串和`null`都将产生数字`0`，相应地，语句的其余部分将被执行。

```
function formattedAmount(x) {
  if (isNaN(x)) {
    return "Not a Number!";
  }
  return "$ " + Number(x).toFixed(2);
  // OR
  // return '$ '+ (+x).toFixed(2);
}

console.log(formattedAmount(""));
// output: "$ 0.00"

console.log(formattedAmount(null));
// output: "$ 0.00"

console.log(formattedAmount(12.126));
// output: "$ 12.13" 
```

Enter fullscreen mode Exit fullscreen mode

希望你觉得有用。

我快速检查了一下，但是没有弄清楚为什么 JavaScript 中的`isNaN('')`是`false`。我很想知道你对此有什么要说的。感谢阅读！

校对: [@ron4ex](https://dev.to/ron4ex)