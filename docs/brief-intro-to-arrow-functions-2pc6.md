# 箭头功能简介

> 原文：<https://dev.to/veronicorn/brief-intro-to-arrow-functions-2pc6>

嘿德芙薇丝。第二篇技术博客文章来了。

显然，大约三年前对 JavaScript 最著名的添加之一(版本 ES6/ES2015)是对**箭头函数的引入。**(我也见过被称为**的胖箭头**功能。)

对 arrow 函数作用的简单解释是，它通过使用更短的语法，使您的函数代码更加简洁，看起来不那么杂乱。

用这个简单的函数将一个数翻倍:

```
const double = function (num) {
    return num * 2;
    }

double(3);
// output is 6

double (12);
// output is 24 
```

现在看看*箭头函数*能做什么:

```
const doubleArrow = num => num * 2;

doubleArrow(3);
// output is still 6!

doubleArrow(12);
// output is still 24! 
```

[![whaaaaaat?](../Images/81d2ca5b88f67a9dc7da0fb2fa45ac40.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ie2a73F6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://memeshappen.com/media/created/2016/12/Whaaaat-.jpg)

**doubleArrow** 函数做了和 **double** 函数完全一样的事情，但是只用了一行代码而不是三行！太神奇了！

这是一个非常简单的例子，只使用了一个参数。让我们做另一个非常简单的例子，但是这次有两个参数，只是为了好玩。

```
// regular function

const sum = function (num1, num2) {
    return num1 + num2;
    }

// arrow function version

const sumArrow = (num 1, num2) => num1 + num2;

sum(1, 10);
// output is 11

sumArrow (1, 10);
// output is still 11 
```

相当酷！double 和 sum 语法之间的唯一区别是，当有多个参数要应用时，我们重新添加了括号。如果只有一个参数，您仍然可以包含括号，但它们是可选的。

arrow 函数还有其他一些很酷的特性，与常规函数略有不同(最明显的是在使用‘this’关键字时)，但我们可以在以后的文章中深入探讨。:)