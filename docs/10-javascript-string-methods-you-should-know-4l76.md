# 你应该知道的 10 种 JavaScript 字符串方法

> 原文：<https://dev.to/frugencefidel/10-javascript-string-methods-you-should-know-4l76>

这篇文章最初发表在我的博客上。

在这篇文章中，我将总结 10 个你至少应该知道的 javascript 字符串方法。

如果有兴趣看这里的 [10 个 javascript 数组方法你应该知道](http://frugencefidel.com/blog/10-javascript-array-methods-you-should-know) 。

字符串是单引号或双引号内的任何内容。

这里有 10 个你应该知道的 javascript 字符串方法。

*1。startsWith()*

检查字符串是否以指定字符开头。

```
 const str = 'JavaScript is amazing';

  console.log(str.startsWith('JavaScript')); // true
  console.log(str.startsWith('Java')); // true
  console.log(str.startsWith('javascript')); // false

  // position is optional
  // if not specified, the default value is 0
  console.log(str.startsWith('Script', 4)); // true
  console.log(str.startsWith('SCRIPT', 4)); // false 
```

Enter fullscreen mode Exit fullscreen mode

*2。endsWith()*

检查字符串是否以指定字符结尾。

```
 const str = 'JavaScript is amazing';

  // check if ends with 'amazing'
  console.log(str.endsWith('amazing')); // true
  console.log(str.endsWith('ing')); // true
  console.log(str.endsWith('Amazing')); // false

  // length is optional
  // if not specified, the default value is length of the string
  console.log(str.endsWith('is', 13)); // true
  console.log(str.endsWith('i', 13)); // false
  console.log(str.endsWith('s', 13)); // true 
```

Enter fullscreen mode Exit fullscreen mode

*3。包含()*

检查字符串是否包含指定的字符。

```
 const str = 'JavaScript is amazing';

  console.log(str.includes('Script')); // true
  console.log(str.includes('script')); // false
  console.log(str.includes('  ')); // true
  console.log(str.includes('array')); // false 
```

Enter fullscreen mode Exit fullscreen mode

*4。slice()*

复制字符串的一部分，而不修改原来的字符串。

```
 const str = 'JavaScript is amazing';

  // Default start index is 0
  console.log(str.slice()); // 'JavaScript is amazing'

  // start copy at index 4
  console.log(str.slice(4)); // 'Script is amazing'

  // end copy at index 10(character at this index will not be included)
  console.log(str.slice(0, 10)); // 'JavaScript' 
```

Enter fullscreen mode Exit fullscreen mode

*5。toUpperCase()*

将字符串转换成大写。

```
 const str = 'JavaScript is amazing';

  console.log(str.toUpperCase()); // 'JAVASCRIPT IS AMAZING' 
```

Enter fullscreen mode Exit fullscreen mode

*6。toLowerCase()*

将字符串转换成小写。

```
 const str = 'JavaScript is amazing';

  console.log(str.toLowerCase()); // 'javascript is amazing' 
```

Enter fullscreen mode Exit fullscreen mode

*7 .查尔特()*

在指定位置返回字符。

```
 const str = 'JavaScript is amazing';

  console.log(str.charAt()); // 'J'
  console.log(str.charAt(11)); // 'i'
  console.log(str.charAt(14)); // 'a'
  console.log(str.charAt(110)); // '' 
```

Enter fullscreen mode Exit fullscreen mode

*8。split()*

将字符串拆分成子字符串数组。

```
 const str = 'JavaScript is amazing';
  const strNew = 'JavaScript-is-amazing';

  console.log(str.split()); // ["JavaScript is amazing"]

  // Separator string used to determine where to make each split
  console.log(str.split('S')); // ["Java", "cript is amazing"]
  console.log(str.split('is')); // ["JavaScript ", " amazing"]
  console.log(str.split('  ')); // ["JavaScript", "is", "amazing"]
  console.log(strNew.split('-')); // ["JavaScript", "is", "amazing"] 
```

Enter fullscreen mode Exit fullscreen mode

*9。替换()*

用字符串中的另一个值替换指定的值。

```
 const str = 'JavaScript is amazing';

  console.log(str.replace('JavaScript', 'Node.js')); // 'Node.js is amazing'

  // replace() method is case sensitive
  // replace will not work
  console.log(str.replace('Javascript', 'Node.js')); // 'JavaScript is amazing'

  // use regular expression for case insensitive
  console.log(str.replace(/Javascript/i, 'Node.js')); // 'Node.js is amazing'

  // replace() method replaces only the first match
  console.log(str.replace('a', 'A')); // 'JAvaScript is amazing'

  // to replace all matches, use regular expression
  console.log(str.replace(/a/g, 'A')); // 'JAvAScript is AmAzing' 
```

Enter fullscreen mode Exit fullscreen mode

10。重复()

返回具有指定数量的现有字符串副本的新字符串。

```
 const str = 'JavaScript';

  console.log(str.repeat(3)); // 'JavaScriptJavaScriptJavaScript'
  console.log(str.repeat(1)); // 'JavaScript'
  console.log(str.repeat(0)); // '' 
```

Enter fullscreen mode Exit fullscreen mode