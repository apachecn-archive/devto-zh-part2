# 你应该知道的 7 大约会方法(Dart)

> 原文：<https://dev.to/graphicbeacon/top-7-date-methods-you-should-know-dart-2ap0>

在本教程中，我们将看看让你在处理日期时更有效率的 7 大方法。使用日期以`DateTime`类的形式出现，这在实现日历、日程和约会等界面特性时证明是有效的。

下面是一个示例片段，展示了如何在 Dart:
中创建一个`DateTime`对象

```
var now = DateTime.now();
var berlinWallFell = DateTime.utc(1989, 11, 9);
var moonLanding = DateTime.parse("1969-07-20 20:18:04Z"); 
```

Enter fullscreen mode Exit fullscreen mode

你可以这样检索有用的信息:

```
berlinWallFell.year; // => 1989
berlinWallFell.month; // => 11 (November) numbering starts from 1 (January)
berlinWallFell.day; // => 9
berlinWallFell.weekday; // => 4 (Thursday) numbering starts from 1 (Monday) 
```

Enter fullscreen mode Exit fullscreen mode

`DateTime`提供了一些有用的日期和月份常量值:

```
moonLanding.weekday == DateTime.sunday; // => true
moonLanding.month == DateTime.july; // => true
DateTime.daysPerWeek; // => 7 
```

Enter fullscreen mode Exit fullscreen mode

如果你喜欢你目前所看到的，那就继续读下去吧！

* * *

## 1。添加()

这将添加所提供的持续时间，并返回一个新的`DateTime`实例:

```
var berlinWallAdd10 = berlinWallFell.add(Duration(days: 10, hours: 5))); // 19th of November at 05:00 hrs

print(berlinWallAdd10.day); // => 19
print(berlinWallAdd10.hour); // => 5 
```

Enter fullscreen mode Exit fullscreen mode

## 2。差异()

它接受另一个`DateTime`对象，将差异作为一个`Duration`对象返回。然后，您可以提取日、小时、分钟等等。

```
var diff = berlinWallFell.difference(moonLanding);

print(diff.inDays); // => 7416
print(diff.inHours); // => 177987
print(diff.inMinutes); // => 10679261 
```

Enter fullscreen mode Exit fullscreen mode

## 3。isAfter

这将检查给定日期是否在`other` :
之后

```
moonLanding.isAfter(berlinWallFell); // => false 
```

Enter fullscreen mode Exit fullscreen mode

## 4。isBefore( `DateTime`其他)

这将检查给定日期是否在`other` :
之前

```
moonLanding.isBefore(berlinWallFell); // => true 
```

Enter fullscreen mode Exit fullscreen mode

## 5。比较(其他)

检查日期值是否相等。如果它们相等，则返回`0`。

```
berlinWallFell.compareTo(berlinWallFell); // => 0 (equal)
moonLanding.compareTo(berlinWallFell); // => -1 (not equal) 
```

Enter fullscreen mode Exit fullscreen mode

## ⑥。减去(`Duration`持续时间)

从日期中减去给定的持续时间。

```
berlinWallFell.subtract(
  Duration(days: 7416, hours: 3, minutes: 41, seconds: 56));
// => 1969-07-20 20:18:04.000Z (about the day and time of the moon landing) 
```

Enter fullscreen mode Exit fullscreen mode

## 7。toLocal()

返回本地时区的日期。对 [i18n](https://en.wikipedia.org/wiki/Internationalization_and_localization) 有用。

```
moonLanding.toLocal(); // => 1969-07-20 21:18:04.000 
```

Enter fullscreen mode Exit fullscreen mode

## 还有奖金...🥁

## 8。toUtc()

返回 UTC 时间的日期

```
moonLanding.toUtc(); // => 1969-07-20 20:18:04.000Z
moonLanding.timeZoneName; // => UTC 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 结论

我希望这是有见地的，如果这是你第一次接触 Dart，请阅读我的[第一步教程](https://dev.to/graphicbeacon/learn-dart-before-you-flutter-2997)以掌握基本知识。这篇文章的代码片段可以在[的标靶](https://dartpad.dartlang.org/e37bd9e9b9181fb9fb50b317d7ddf6af)上找到。

**喜欢并关注我**😍更多关于 Dart 的文章。

## 快速链接

1.  [日期时间类文档](https://api.dartlang.org/stable/2.0.0/dart-core/DateTime-class.html)
2.  [持续时间类文档](https://api.dartlang.org/stable/2.0.0/dart-core/Duration-class.html)
3.  **[免费飞镖截屏在 Egghead.io](https://egghead.io/instructors/jermaine-oppong)**