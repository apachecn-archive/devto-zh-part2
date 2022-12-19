# 如何用 Dart 将一个字符串转换成一个数字(反之亦然)

> 原文：<https://dev.to/wangonya/how-you-turn-a-string-into-a-number-or-vice-versa-with-dart-392h>

当你习惯了一种语言，并试图学习一种新的语言时，你可能会发现自己总是回头看你已经知道的东西，以确定新语言是否匹配。我知道我有。如果我找到相似之处，我很高兴🙂。如果我发现更多的功能，我会很兴奋😄。如果缺少新的语言，我会想，为什么会缺少如此重要的东西(不管是多么微不足道的东西)🤨我不能带着这个工作。

假设我需要用 Javascript 将一个字符串转换成一个数字，有很多方法可以做到:

*   使用 [`parseInt()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/parseInt)
*   使用 [`parseFloat()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/parseFloat)
*   使用 [`Number()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)

Dart 具有几乎相似的功能来实现相同的结果。

### 用镖将字符串变成数字(反之亦然)

你可以在[标靶](https://dartpad.dartlang.org/)上尝试代码片段

#### 字符串到整数

```
// String -> int
main () {
    var one = int.parse('1');
    print(one == 1); // prints true
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 字符串为双精度

```
// String -> double
main () {
    var onePointOne = double.parse('1.1');
    print(onePointOne == 1.1); // prints true
} 
```

Enter fullscreen mode Exit fullscreen mode

#### int 到 String

```
// int -> String
main () {
    String oneAsString = 1.toString();
    print(oneAsString == '1'); // prints true
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 双到串

```
// double -> String
main () {
    String piAsString = 3.14159.toStringAsFixed(2);
    print(piAsString == '3.14'); // prints true
} 
```

Enter fullscreen mode Exit fullscreen mode

我一直想用 flutter 进行移动应用程序开发，所以我想在进入 flutter 之前先熟悉一下 dart。到目前为止，我很开心。Dart 来自 Javascript，容易掌握，学习起来也很有趣。