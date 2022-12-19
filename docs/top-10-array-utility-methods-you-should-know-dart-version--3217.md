# 您应该知道的 10 大阵列实用程序方法(Dart)🎯

> 原文：<https://dev.to/graphicbeacon/top-10-array-utility-methods-you-should-know-dart-version--3217>

作为我使用 [Dart](https://dartlang.org) 进行客户端应用程序开发的一部分，我开始探索如何使用数组/列表类型。除了文档全面之外，我还能够在 [StackOverflow](https://stackoverflow.com/questions/tagged/dart) 社区上找到支持，并成功实现了我所需要的。

在今天的文章中，我们将关注 Dart 的“内置电池”概念，特别是用于处理列表的[内置实用程序](https://api.dartlang.org/dev/2.0.0-dev.65.0/dart-core/List-class.html)。我为你的下一个应用精选了 10 个你知道的最常见的。我还[准备了](https://dartpad.dartlang.org/aec9a0ecd000063f53630799bb24b05c)示例片段，这样你就可以自己玩了😁

那么，我们可以开始了吗？

* * *

## 1。forEach()

对列表中的每个元素运行一个函数

```
var fruits = ['banana', 'pineapple', 'watermelon'];
fruits.forEach((fruit) => print(fruit)); // => banana pineapple watermelon 
```

Enter fullscreen mode Exit fullscreen mode

## 2。地图()

转换给定列表中的每个元素后生成一个新列表

```
var mappedFruits = fruits.map((fruit) => 'I love $fruit').toList();
print(mappedFruits); // => ['I love banana', 'I love pineapple', 'I love watermelon'] 
```

Enter fullscreen mode Exit fullscreen mode

## 3。包含()

检查以确认给定元素在列表
中

```
var numbers = [1, 3, 2, 5, 4];
print(numbers.contains(2)); // => true 
```

Enter fullscreen mode Exit fullscreen mode

## 4。排序()

根据提供的*排序*函数
对列表中的元素进行排序

```
numbers.sort((num1, num2) => num1 - num2); // => [1, 2, 3, 4, 5] 
```

Enter fullscreen mode Exit fullscreen mode

## 5。减少()，折叠()

使用给定的函数
将列表中的元素压缩成一个值

```
var sum = numbers.reduce((current, next) => current + next);
print(sum); // => 15

// with fold() you provide an initial value
const initialValue = 10;
var sum2 = numbers.fold(initialValue, (current, next) => current + next);
print(sum2); // => 25 
```

Enter fullscreen mode Exit fullscreen mode

## 6。每隔()

确认每个元素都满足测试

```
List<Map<String, dynamic>> users = [
  { "name": 'John', "age": 18 },
  { "name": 'Jane', "age": 21 },
  { "name": 'Mary', "age": 23 },
];

var is18AndOver = users.every((user) => user["age"] >= 18);
print(is18AndOver); // => true

var hasNamesWithJ = users.every((user) => user["name"].startsWith('J'));
print(hasNamesWithJ); // => false 
```

Enter fullscreen mode Exit fullscreen mode

## 7。where()，firstWhere()，singleWhere()

返回满足测试
的元素集合

```
// See #6 for users list
var over21s = users.where((user) => user["age"] > 21);
print(over21s.length); // => 1

var nameJ = users.firstWhere((user) => user["name"].startsWith('J'), orElse: () => null);
print(nameJ); // => {name: John, age: 18}

var under18s = users.singleWhere((user) => user["age"] < 18, orElse: () => null);
print(under18s); // => null 
```

Enter fullscreen mode Exit fullscreen mode

`firstWhere()`返回列表中的第一个匹配，而`singleWhere()`返回第一个匹配，前提是**恰好有一个匹配**。

## 8。take()，skip()

包含或跳过元素时返回集合

```
var fiboNumbers = [1, 2, 3, 5, 8, 13, 21];
print(fiboNumbers.take(3).toList()); // => [1, 2, 3]
print(fiboNumbers.skip(5).toList()); // => [13, 21]
print(fiboNumbers.take(3).skip(2).take(1).toList()); // => [3] 
```

Enter fullscreen mode Exit fullscreen mode

## 9。List.from()

从给定的集合
创建一个新列表

```
var clonedFiboNumbers = List.from(fiboNumbers);
print('Cloned list: $clonedFiboNumbers'); 
```

Enter fullscreen mode Exit fullscreen mode

*从 Dart 2.0 开始，* `new` *关键字在实例化对象时是可选的。*

## 10。展开()

将每个元素扩展成零个或多个元素

```
var pairs = [[1, 2], [3, 4]];
var flattened = pairs.expand((pair) => pair).toList();
print('Flattened result: $flattened'); // => [1, 2, 3, 4]

var input = [1, 2, 3];
var duplicated = input.expand((i) => [i, i]).toList();
print(duplicated); // => [1, 1, 2, 2, 3, 3] 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我希望这是有见地的，如果这是你第一次接触 Dart，请阅读我的[第一步教程](https://dev.to/graphicbeacon/learn-dart-before-you-flutter-2997)以掌握基本知识。这篇文章的示例片段可以在[的标靶](https://dartpad.dartlang.org/aec9a0ecd000063f53630799bb24b05c)上找到。

**喜欢并关注我**😍有关 Dart 的更多内容。

本教程到此结束。感谢阅读。

## 接下来要检查什么

1.  [列表< E >类文档](https://api.dartlang.org/dev/2.0.0-dev.65.0/dart-core/List-class.html)
2.  **[飞镖截屏在 egghead . io](https://egghead.io/instructors/jermaine-oppong)T3】**

* * *

继续阅读:

[![graphicbeacon image](../Images/ebd69e31cdafde0c3cc551828feae27a.png)](/graphicbeacon) [## 你应该知道的十大贴图/对象工具方法(Dart)🎯

### 杰梅因 Oppong 7 月 23 日 182 分钟阅读

#dart #webdev #beginners #showdev](/graphicbeacon/top-10-mapobject-utility-methods-you-should-know-dart--1ido)

* * *