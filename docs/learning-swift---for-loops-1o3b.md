# 学习快速循环

> 原文：<https://dev.to/trozware/learning-swift---for-loops-1o3b>

*从[https://troz.net/post/2016/learning-swift-for-loops/](https://troz.net/post/2016/learning-swift-for-loops/)交叉发布，作为我学习 Swift 系列的一部分。*

循环是任何程序的基本构件。快速准确地做重复性的工作是计算机真正擅长的，而我们人类做这些会感到很无聊。Swift 提供了几种不同的执行循环的方法，但是今天我们将集中讨论 for 循环。

最基本的循环形式是`for-in`循环。有两种方法可以使用它:循环一个范围内的数字或循环一个数组或字典中的元素。

首先，范围:

```
for x in 0 ..< 5 { printWithSpace(x) }
// prints: 0 1 2 3 4 
```

为了方便起见，我使用了自定义打印功能，可以在一行中打印结果。

这个`for-in`循环使用半开范围，它实际上是在说:

```
start with x at 0
print x with a space
add 1 to x
is x still less than 5?
    yes - go back to the print stage
    no - stop 
```

如果我们想一直走到 5，而不是在 5 之前停止，我们将使用一个封闭的范围，用`...`
代替`..<`

```
for x in 0 ... 5 { printWithSpace(x) }
// prints: 0 1 2 3 4 5 
```

* * *

如果我们想一次遍历一系列数字中的一个，这些方法很好，但是如果我们想要不同的增量呢？有几种方法可以改变步长。

*   `stride(from:to:by:)`
*   `stride(from:through:by:)`
*   `where`

```
for x in stride(from: 0, to: 10, by: 2) {
  printWithSpace(x)
}
// prints: 0 2 4 6 8 
```

```
for x in stride(from: 0, through: 10, by: 2) {
  printWithSpace(x)
}
// prints: 0 2 4 6 8 10 
```

`stride`的两种变化类似于在`for-in`循环中表达范围的两种方式。

`stride(from:to:by:)`从`from`参数中的数字开始，每次循环时，该数字增加`by`参数的数量。如果增加的数字小于`to`参数，循环继续。

`stride(from:through:by:)`以同样的方式工作，但会继续，直到增加的数字大于或等于`through`参数。

使用负值的`by`允许倒计数:

```
for x in stride(from: 10, through: 0, by: -2) {
  printWithSpace(x)
}
// prints: 10 8 6 4 2 0 
```

Stride 也可用于浮点循环，尽管结果可能包含一些意想不到的精度变化，例如 0.70000000000001，而不是预期的 0.7

```
let startingNumber = 0.1
for x in stride(from: startingNumber, through: 1.0, by: 0.2) {
  printWithSpace(x)
}
// prints: 0.1 0.3 0.5 0.7000000000000001 0.9 
```

注意，结果不包含`through`参数，因为下一次迭代将产生大于它的 1.1。此外,`startingNumber`不必是变量，因为它实际上从未改变过。

使用`where`可以在符合某些条件的情况下单步执行循环:

```
for x in 0 ... 10 where x % 3 == 0 {
  printWithSpace(x)
}
// prints: 0 3 6 9 
```

* * *

`for-in`循环的另一个主要用途是遍历数组或字典的元素:

```
let arrayOfInts = [ 1, 2, 3, 4, 5, 6 ]
for x in arrayOfInts {
  printWithSpace(x * 2)
}
// prints: 2 4 6 8 10 12 
```

```
let myDict = [ 1: "one", 2: "two", 3: "three" ]
for (key, value) in myDict {
  print("\(key) = \(value)")
}
// prints:
// 2 = two
// 3 = three
// 1 = one 
```

由于字典是未排序的，所以条目的顺序可能会有所不同。

如果你想遍历一个数组，同时监控每个元素的索引，你可以使用`enumerated()`

```
let arrayOfNames = [ "Andy", "Buzz", "Woody" ]
for (index, element) in arrayOfNames.enumerated() {
  print("The name at index \(index) is \(element)")
}
// prints:
// The name at index 0 is Andy
// The name at index 1 is Buzz
// The name at index 2 is Woody 
```

如果您的数组包含可选值，您可以使用`for case let`来测试每个值:

```
let arrayWithOptionals: [String?] = [ "Woody", nil, "Buzz", nil, "Rex" ]
for case let name? in arrayWithOptionals {
  printWithSpace(name)
}
// prints: Woody Buzz Rex 
```

`name`变量在每次循环时都被解包，因此可以安全使用。

这些循环类型中的任何一种都可以与`where`结合起来，在每次迭代中执行检查:

```
let arrayWithOptionals: [String?] = [ "Woody", nil, "Buzz", nil, "Rex" ]
for case let name? in arrayWithOptionals where name.count < 5 {
  printWithSpace(name)
}
// prints: Buzz Rex 
```

* * *

你们中的一些人可能想知道像这样的旧 C 风格循环发生了什么:

```
for var x = 0; x < 5; x++ { print(x, terminator: " ") } 
```

这在 Swift 3+中不起作用:语言中已经删除了`++`和`--`操作符以及这种风格的循环声明。有关更多详情，请查看 Swift Evolution 的相关提案:

*   [删除带有条件和增量的 C 型 for 循环](https://github.com/apple/swift-evolution/blob/master/proposals/0007-remove-c-style-for-loops.md)
*   [删除++和-运算符](https://github.com/apple/swift-evolution/blob/master/proposals/0004-remove-pre-post-inc-decrement.md)

* * *

本文中的所有示例都可以在 Swift playground 中找到，现在
更新为 Swift 4 语法。

## ![GitHub logo](../Images/a73f630113876d78cff79f59c2125b24.png)[troz ware](https://github.com/trozware)/[for-loops](https://github.com/trozware/for-loops)

### 学习快速循环的游乐场

<article class="markdown-body entry-content container-lg" itemprop="text">

# for 循环

学习快速循环的游乐场

[https://troz.net/post/2016/learning-swift-for-loops/](https://troz.net/post/2016/learning-swift-for-loops/)

现已更新至 Swift 4 语法。

</article>

[View on GitHub](https://github.com/trozware/for-loops)