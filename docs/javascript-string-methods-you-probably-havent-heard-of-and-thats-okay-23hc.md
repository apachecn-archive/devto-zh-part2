# JavaScript 字符串方法你可能没听说过，这没关系

> 原文：<https://dev.to/mahmoudelmahdi/javascript-string-methods-you-probably-havent-heard-of-and-thats-okay-23hc>

[![OCTOPAL](../Images/be7cc7044ffa74a19b7fec634ba48530.png "OCTOPAL")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ipOpvi0P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gw77jwde8kgh8kiraapy.png)

# 概述

字符串是 JavaScript 的数据类型，用于表示可以包含字母、数字、符号、标点符号甚至表情符号的文本。它们由零个或多个(16 位值)字符组成，用单引号`'`或双引号`"`括起来。

这不是火箭科学！之前的介绍没什么特别的，但是刷新一下总是好的。信不信由你，有大量的开发人员对每一个 JavaScript 预定义方法都一无所知，我们大多数人都觉得这些信息在脑子里“没问题”。例如，几乎我们所有人都知道，要获取一个字符串的第一个字符，我们可以像`str[0]`这样做，这完全没问题。在这篇文章中，我将通过例子介绍你可能听说过或者没听说过的 5 种 JavaScript 方法。

## 字符串方法&属性

因为字符串是基元值之一，例如“Mask Off”不能有属性或方法。幸运的是，JavaScript 定义了几个在字符串上调用的属性和方法。这些属性和方法是使用点符号来访问的。

> 字符串在 JavaScript 中是不可变的；并且可以像只读数组一样对待。所有字符串方法都返回一个新值，并且它们不会修改调用它们的字符串。

* * *

## 示例设置

在我们深入了解更多细节之前，让我们先设置一个可以在所有示例中使用的块:

```
const content = "Forem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronics typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem IpsumL";

const { length }  = content;    // 500 characters
const firstChar   = 0;          // F
const lastChar    = length - 1; // L
const midContent  = length / 2; // e
const outOfRange  = length * 2; // "" 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## charAt()方法

`charAt()`方法返回指定索引处的字符，如果索引超出范围，则返回空字符串。如果未提供 index-param，则默认值为 0。

```
/**
 * @param  {number} ranges from 0 to the length of the string -1
 * @return {string}
 */
string.charAt(index) 
```

Enter fullscreen mode Exit fullscreen mode

### charAt()示例

```
content.charAt() // "F"
content.charAt(firstChar)  // "F"
content.charAt(lastChar)   // "L"
content.charAt(midContent) // "e"
content.charAt(outOfRange) // "" 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## [T1】starts with()方法](#startswith-method)

方法确定一个字符串是否以指定字符串的字符开始。

```
/**
 * @param  {string} string to search for
 * @param  {number} optional - index, defaults to 0
 * @return {boolean}
 */
string.startsWith(string, position) 
```

Enter fullscreen mode Exit fullscreen mode

> `startsWith()`方法是**区分大小写的**。

### 以()为例开始

```
content.startsWith('F') // true
content.startsWith('f') // false
content.startsWith('e', midContent) // true 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## endsWith()方法

方法确定一个字符串是否以一个指定字符串的字符结束。否则返回`false`

### endsWith()示例

```
content.endsWith('L', lastChar) // true
content.endsWith('l', lastChar) // false 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 包括()方法

方法`includes()`让你决定一个字符串是否包含另一个字符串，返回`Boolean`

```
/**
 * @param  {string} string to search for
 * @param  {number} optional - index, defaults to 0
 * @return {boolean}
 */
string.includes(string, position) 
```

Enter fullscreen mode Exit fullscreen mode

### 包括()例子

```
content.includes('Ipsum') // true
content.includes('1960s') // true
content.includes('Hello from outside') // false 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 重复()方法

`repeat()`方法构造并返回一个新的字符串，该字符串带有被调用字符串的指定数量的副本，*将*连接在一起。

```
/**
 * @param  {number} - indicating the number of times to repeat
 * @return {string}
 */
string.repeat(count) 
```

Enter fullscreen mode Exit fullscreen mode

> 重复计数必须为:非负、小于无穷大且不溢出最大字符串大小。

### 重复()例子

```
"**".repeat(3)  // "******"
"😺".repeat(3)  // "😺😺😺" 
```

Enter fullscreen mode Exit fullscreen mode

* * *

**总结**；上面提到的方法可以用另一种方法实现，它们可能对性能有害，或者它们是最快的选择！最终结果将取决于你的需求。

要更详细地查看所有可用的属性和方法
，我强烈推荐阅读完整的 [JavaScript 字符串参考](https://www.w3schools.com/jsref/jsref_obj_string.asp)。