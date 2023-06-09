# JavaScript:布尔基础

> 原文：<https://dev.to/kymiddleton/javascript-boolean-basics-1kf>

## 布尔

布尔作为一种原始数据类型，没有方法，也不是对象。布尔值主要与条件语句相关，当需要一个只能有两个值之一的数据类型(如 True / False、Yes / No 或 On / Off)时，它就会发挥作用。

```
const YES = true;
const NO = false; 
```

条件语句可用于根据特定条件确定不同的操作。If 语句和 if…else 语句构成了 JavaScript 中最常见的条件语句，通常只有在条件为真时才运行代码。

```
if (YES) {
  alert (“Block of code will execute”)
} 
if (NO) {
  alert (“This block of code will NOT execute”)
} 
```

如果需要检查一个以上的条件，可以在`if`和`else`之间使用`else if`语句。`condition`告诉 JavaScript 在继续之前要检查什么。如果条件评估为`true`，则执行`if`块中的代码。如果条件为`false`，则执行`else`块中的代码。

```
if (condition) {
  //Do Something
} else {
  //Do Something Else
}} 
```

## 比较运算符

比较运算符用于测试*真*或*假*，并在逻辑语句中用于确定值或变量之间的相等性。

#### 用于比较数字:

大于`>`或大于等于`>=`

```
6 > 4 // True
6 > 4 // False
6 >= 6 //True 
```

小于`<`或者小于等于`<=`

```
6 < 8 // True
6 < 6 // False
6 <=6 //True 
```

#### 检查项目是否相等或不相等:

在 JavaScript 中，三重等于`===`被称为严格相等，并评估两个数是否相等。double equals `==`用于查看两个值是否相同。如果两项不相等，不等运算符`!=`返回 true。如果项目不相等和/或不属于同一类型，则不相等或严格不相等`!==`返回 true。

等于`===`还是不等于`!=`

```
6 === 6 // True
6 != 6 // False 
```

严格不平等`!==`或不平等`!=`

```
1 != 2 // True
4 !== 3 // True 
```

严格等于`===`或等于`==`

```
‘6’ === 6 // False
‘6’ == 6 // True 
```

当将字符串“6”与数字 6 进行比较时，双倍等于`==`的结果为`true`，而三倍等于或严格等于`===`的结果为`false`。

然而，平等可能会引起混淆，所以要小心！在布尔值上下文中引用的值可以在 JavaScript 中自动强制。

以下评估为`True` :

```
console.log (‘’ == 0);
console.log (null == undefined);
console.log (false == ‘0’); 
```

这些怎么样？

```
if ( [ ] )  / / … if ( { } ) / / … 
```

对`True`的猜测是正确的，但是为什么呢？尽管是空的，`[ ]`和`{ }`，这两个都是对象。在 JavaScript 中，任何对象都将被强制为布尔值`true`。为了避免类型强制，使用`===`和`!==`来比较两个事物。