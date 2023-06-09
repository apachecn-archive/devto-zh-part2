# code tip-Javascript:es6 函数中的伪强制参数

> 原文：<https://dev.to/avraammavridis/codetip---javascript-pseudomandatory-parameters-in-es6-functions-lb0>

在许多编程语言中，函数的参数在默认情况下是强制的，开发人员必须明确定义参数是可选的。在 Javascript 中，每个参数都是可选的，但是我们可以利用 [es6 的参数缺省值特性，在不干扰函数实际体的情况下实施这种行为。](http://exploringjs.com/es6/ch_parameter-handling.html#sec_parameter-default-values)

```
const _err = function(message) {
  throw new Error(message);
}

const getSum = (a = _err('a is not defined'), b = _err('b is not defined')) => a + b;

getSum(10); // throws Error b is not defined 
```

`_err`是一个立即抛出错误的函数。如果没有为其中一个参数传递值，将使用默认值，将调用`err`并抛出一个错误。你可以在 Mozilla 的开发者网络上看到更多关于[默认参数特性的例子。](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/default_parameters)