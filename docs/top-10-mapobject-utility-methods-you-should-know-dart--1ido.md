# 你应该知道的十大贴图/对象工具方法(Dart)🎯

> 原文：<https://dev.to/graphicbeacon/top-10-mapobject-utility-methods-you-should-know-dart--1ido>

在本文中，我们将看看关于 Dart 中的`Map`类型您应该知道的前 10 个实用方法。这是我之前写的关于数组/列表类型的“[前 10 名](https://dev.to/graphicbeacon/top-10-array-utility-methods-you-should-know-dart-version--3217)”的姊妹文章，所以让我们直接进入主题。

* * *

## 1。addAll()

这允许您将提供的映射的所有键/值对合并到当前的映射中。

```
var user = {
  "firstName": "Tom",
  "age": 25,
};

user.addAll({
  "lastName": "Smith",
  "age": 26,
});

print(user); // => {"firstName": "Tom", "age": 26, "lastName": "Smith"} 
```

Enter fullscreen mode Exit fullscreen mode

如果一个键已经存在，它的值将被替换。*这类似于 JavaScript 中的`Object.assign`。*

## 2。包含键()

检查给定的键是否存在

```
print(user.containsKey("age")); // => true
print(user.containsKey("accessToken")); // => false 
```

Enter fullscreen mode Exit fullscreen mode

## 3。包含 Value()

检查给定值是否存在

```
print(user.containsValue("Smith")); // => true
print(user.containsValue(40)); // => false 
```

Enter fullscreen mode Exit fullscreen mode

## 4。forEach()

对每个键/值对运行给定的函数

```
user.forEach((key, value) => print('Key: $key, Value: $value')); // => "Key: firstName, Value: Tom" "Key: age, Value: 26" "Key: lastName, Value: Smith" 
```

Enter fullscreen mode Exit fullscreen mode

## 5。putIfAbsent()

如果不存在，则添加一个键/值对。如果键已经存在，如果没有，将设置一个值。

```
user.putIfAbsent("accessToken", () => "abf329jklr90rnlk2..."); // => {"firstName": "Tom", "age": 26, "lastName": "Smith", "accessToken": "abf329jklr90rnlk2..."} 
```

Enter fullscreen mode Exit fullscreen mode

## 6。移除()

删除提供的键及其相关值

```
user.remove("accessToken"); // => abf329jklr90rnlk2... 
```

Enter fullscreen mode Exit fullscreen mode

这将返回被删除的值。

## 7。removeWhere()

如果给定条件为真，则删除键/值对

```
user.removeWhere((key, value) => key == "lastName"); 
```

Enter fullscreen mode Exit fullscreen mode

## 8。清除()

删除映射中的每个键/值对

```
user.clear();
print(user); // => {} 
```

Enter fullscreen mode Exit fullscreen mode

## 9。更新()

更新给定键
的值

```
user["age"] = 25;
user.update("age", (dynamic val) => ++val); // => 26 
```

Enter fullscreen mode Exit fullscreen mode

这也将返回新值。为了防止键不存在时抛出错误，还有第三个参数:

```
user.update("name", (dynamic val) => "Jim", ifAbsent: () => "Jane");
print(user); // => {"age": 26, "name": "Jane"}; 
```

Enter fullscreen mode Exit fullscreen mode

在大多数情况下，你可以使用数组括号符号来更新:

```
user["name"] = "Mary"; 
```

Enter fullscreen mode Exit fullscreen mode

## 10。Map.from()

从技术上讲，这是一个构造函数，但作为一个实用程序仍然很有用。它创建并返回所提供地图的副本:

```
var userCopy = Map.from(user); 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我希望这是有见地的，如果这是你第一次接触 Dart，请阅读我的[第一步教程](https://dev.to/graphicbeacon/learn-dart-before-you-flutter-2997)以掌握基本知识。这篇文章的代码片段可以在[的标靶](https://dartpad.dartlang.org/b738e69b9985a5c11a841c94de1a1261)上找到。

**喜欢并关注我**😍更多关于 Dart 的文章。非常感谢。

## 快速链接

1.  [地图< K，V >类文档](https://api.dartlang.org/dev/2.0.0-dev.65.0/dart-core/Map-class.html)
2.  **[免费飞镖截屏在 Egghead.io](https://egghead.io/instructors/jermaine-oppong)**

* * *

继续阅读:

[![graphicbeacon image](../Images/ebd69e31cdafde0c3cc551828feae27a.png)](/graphicbeacon) [## 您应该知道的十大字符串实用程序方法(Dart)🎯

### 杰梅因奥彭 8 月 9 日 182 分钟阅读

#dart #webdev #beginners #showdev](/graphicbeacon/top-10-string-utility-methods-you-should-know-dart--4df0)

* * *