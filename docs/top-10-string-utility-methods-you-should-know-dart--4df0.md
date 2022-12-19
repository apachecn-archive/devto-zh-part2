# 您应该知道的十大字符串实用程序方法(Dart)🎯

> 原文：<https://dev.to/graphicbeacon/top-10-string-utility-methods-you-should-know-dart--4df0>

在今天关于 10 大方法的系列文章中，我们将看看`String`类型附带的一些有用的实用方法。

您可以将这些方法与模板文字的内置支持结合使用，从而允许有效地操作字符串:

```
var str1 = 'Lorem';
var str2 = '$str1 ipsum'; // String interpolation
var str3 = '''Multi
Line $str1  $str2'''; // Multi-line strings 
```

Enter fullscreen mode Exit fullscreen mode

我们先来看看:

## 1。包含()

这允许你检查指定的字符串是否存在:

```
str1.contains('rem'); // true 
```

Enter fullscreen mode Exit fullscreen mode

## 2。开始于()

这允许您检查字符串是否以指定的字符开头:

```
str2.startsWith('Lorem'); // true
str3.startsWith('Noorem'); // false 
```

Enter fullscreen mode Exit fullscreen mode

## 3。端点()

检查字符串是否以指定字符结尾:

```
str3.endsWith('ipsum'); // true
str3.endsWith('oopsum'); // false 
```

Enter fullscreen mode Exit fullscreen mode

## 4。toLowerCase()，toUpperCase()

将字符串转换为小写和大写格式:

```
str1.toLowerCase(); // lorem
str1.toUpperCase(); // LOREM 
```

Enter fullscreen mode Exit fullscreen mode

## 5。拆分()

按照匹配模式分割字符串，返回子字符串列表:

```
str3.split('\n'); // ['Multi', 'Line', 'Lorem Lorem ipsum']; 
```

Enter fullscreen mode Exit fullscreen mode

## 6。splitMapJoin()

拆分字符串，转换每个列表项，并将它们组合成一个新字符串:

```
str3.splitMapJoin(RegExp(r'^', multiLine: true), // Matches the beginning of each line
  onMatch: (m) => '**${m.group(0)} ', // Adds asterisk to the line beginning
  onNonMatch: (n) => n); // Leaves non matches as is
/*
Output: 

** Multi
** Line
** Lorem Lorem ipsum

*/ 
```

Enter fullscreen mode Exit fullscreen mode

## 7\. indexOf(), lastIndexOf()

返回给定模式的第一个和最后一个匹配的位置:

```
str3.indexOf('rem'); // 13
str3.lastIndexOf('rem'); // 19 
```

Enter fullscreen mode Exit fullscreen mode

这两种方法都有一个可选参数，指定从哪个索引开始搜索:

```
str3.lastIndexOf('rem', 18); // 13 
```

Enter fullscreen mode Exit fullscreen mode

## 8。修剪()

删除前导和尾随空格:

```
" $str2 ".trim(); // 'Lorem ipsum' 
```

Enter fullscreen mode Exit fullscreen mode

## 9。padLeft()，padRight()

如果字符串小于指定的长度:
，则用给定的填充在左右两边填充字符串

```
str1.padLeft(8, 'x'); // xxLorem
str1.padRight(8, 'x'); // Loremxx 
```

Enter fullscreen mode Exit fullscreen mode

## 10。replaceAll()

用替换字符串:
替换与指定模式匹配的所有子字符串

```
str2.replaceAll('e', 'é'); // Lorém 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 结论

我希望这是有见地的，如果这是你第一次接触 Dart，请阅读我的[第一步教程](https://dev.to/graphicbeacon/learn-dart-before-you-flutter-2997)以掌握基本知识。这篇文章的代码片段可以在[的标靶](https://dartpad.dartlang.org/abea509cc44de22b5c714c686b17c2ce)上找到。

**喜欢并关注我**😍更多关于 Dart 的文章。非常感谢。

## 快速链接

1.  [字符串类文档](https://api.dartlang.org/stable/2.0.0/dart-core/String-class.html)
2.  **[免费飞镖截屏在 Egghead.io](https://egghead.io/instructors/jermaine-oppong)**

* * *

继续阅读:

[![graphicbeacon image](img/ebd69e31cdafde0c3cc551828feae27a.png)](/graphicbeacon) [## 你应该知道的 7 大约会方法(Dart)

### 杰梅因·奥蓬 9 月 14 日 182 分钟阅读

#dart #webdev #beginners #showdev](/graphicbeacon/top-7-date-methods-you-should-know-dart-2ap0)

* * *