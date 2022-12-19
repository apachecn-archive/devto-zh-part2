# JavaScript 或运算符 JavaScript:逻辑运算符和布尔值

> 原文：<https://dev.to/banesag/javascript-logical-operators-and-boolean-values-1l07>

[![alt text](img/5fc186c30577bb4445680f9d3f9a15d8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cF7T_xLB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zisuac1tw2xxxjv1o9sv.jpg)

### 逻辑运算符！(非)、&&(和)、||(或)

如果您正在学习编码或初学编码，您将使用逻辑运算符，这些运算符通常与布尔(逻辑)值一起使用。但是需要密切注意，因为&&和||操作符将返回指定操作数的值，所以如果操作数使用的是非布尔值，则返回的将是非布尔值。

逻辑运算符用于任何原始值或对象。其结果将基于该值是真还是假:

首先，我们来识别一下 JavaScript 中有三个逻辑运算符:！(非)、&&(和)、||(或)-用两个竖线符号表示。

**！**(逻辑非)

使用！运算符将把它转换为布尔值，并返回一个相反的值。这意味着真值将返回假，假值将返回真。Logical || (OR)仅用于处理布尔值。这种方法被称为否定:

***例如:***

```
!true; // negating true returns false
<<false

!0; // 0 is a false value, so negating it returns true
<<true 
```

使用双重否定(！！)可以帮助你发现一个值是真还是假，特别是当你不是一个有经验的程序员，你需要验证的时候，那么你可以先用这个方法测试一下，看看这个值是真还是假。

***例如:***

```
!! ‘ ’; // empty string value is false
<< false

!!“hello”;
<< true

!!3;
<< true

!!NaN;
<<False

!!“false”;
<< true

!!‘0’;  // Remember that a number 0 (zero) is false
<< true 
```

现在，让我们定义运算符&&和||它代表逻辑 and 和 OR 逻辑运算符。

***直观例子:***

```
// Logical AND operator
true  && true;  // true
true  && false; // false
false && true;  // false
false && false; // false

// Logical OR operator
true  || true;  // true
true  || false; // true
false || true;  // true
false || false; // false 
```

**& &(与)逻辑运算符**

逻辑运算符与两个或多个值(操作数)一起使用，并且仅当所有操作数都为真时才计算为真。如果它们都为真，返回的值将是最后一个真值，但是如果至少有一个值为假，它将返回第一个假值。

***例如:***

```
const x = 7;
const y = 4;

(x < 10 && y > 1); // true 
(x < 10 && y < 1); // false 
```

**||(或)逻辑运算符**

逻辑运算符|| (OR)也与两个或多个值一起使用，但如果操作数(值)中的任何一个*为真，则它的计算结果为真，因此只有当两个操作数都为假时，它的计算结果才为假。*

 ****例如***

```
const x = 7;
const y = 4;

(x == 5 || y == 5); // false 
(x == 7 || y == 0); // true
(x == 0 || y == 4); // true
(x == 7 || y == 4); // true 
```

**结论**

在 JavaScript 中，&&和||并不总是返回布尔值，但是运算符总是返回它们的一个操作数表达式的值。而且，用双重否定！！您的真值和假值可以转换为布尔值。*