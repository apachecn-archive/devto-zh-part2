# 科特林列表操作符有效率吗？

> 原文：<https://dev.to/lalunamel/are-kotlin-list-operators-efficient-5aen>

*本帖原本为[blog.codysehl.net](//blog.codysehl.net)T3】准备*

嗯，那要看你说的*高效*是什么意思了！

让我们从探索列表操作符(map、filter、reduce 等)可能的工作方式开始。

一个例子

```
val numbers = listOf(1, 2, 3, 4, 5, 6)
val evenNumbers = list.filter { num -> num % 2 == 0 } // 2, 4, 6
val evenNumbersDoubled = evenNumbers.map { num -> num * 2 } // 4, 8, 12
val sum = evenNumbersDoubled.reduce { sum, num -> sum + num } // 24 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们得到了一个数字列表，找到偶数，加倍，然后求和。每一步都被分解成一个变量，这样每一个操作都有一个名字。

这里有一个更简洁的版本，更接近于在生产中可能是如何编写的:

```
listOf(1, 2, 3, 4, 5, 6)
  .filter { num -> num % 2 == 0 } // 2, 4, 6
  .map { num -> num * 2 } // 4, 8, 12
  .reduce { sum, num -> sum + num } // 24 
```

Enter fullscreen mode Exit fullscreen mode

鉴于以上所述，我可以设想这段代码的两种合理的方式:
(为了简单起见，用伪 Java 表示)

### 为每个操作者的一个循环

```
list = [1, 2, 3, 4, 5, 6]

// Filter
evenNumbers = []
for(i=0; i<list.length, i++) {
  num = list[i]
  if(num % 2 == 0) {
    evenNumbers.add(num)
  }
}

// Map
evenNumbersDoubled = []
for(i=0; i<evenNumbers.length, i++) {
  num = evenNumbers[i]
  transformedResult = num * 2
  evenNumbersDoubled.add(transformedResult)
}

// Reduce
sum = 0
for(i=0; i<evenNumbersDoubled.length, i++) {
  num = evenNumbersDoubled[i]
  sum = sum + num
} 
```

Enter fullscreen mode Exit fullscreen mode

使用这种方法，列表会迭代三次。

### 多个操作符，一个循环

```
list = [1, 2, 3, 4, 5, 6]

sum = 0
evenNumbers = []
for(i=0; i<list.length, i++) {
  num = list[i]

  // Filter
  if(num % 2 == 0) {

    // Map
    num = num * 2

    // Reduce
    sum = sum + num
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用这种方法，列表被迭代一次。

## 科特林是用哪种方式做的？

现在我们已经建立了列表操作符的两种可能的工作方式，Kotlin 选择哪种方式呢？

让我们来看看贴图、过滤和减少的来源！

(你可能不想跟着链接走——这些文件长达数千行。)

### 地图

[源](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map.html)带我们到 [_Arrays.kt:8255](https://github.com/JetBrains/kotlin/blob/1.2.60/libraries/stdlib/common/src/generated/_Arrays.kt#L8225) ，它使用在 [8542](https://github.com/JetBrains/kotlin/blob/1.2.60/libraries/stdlib/common/src/generated/_Arrays.kt#L8542) 行定义的`mapTo`。

下面是`mapTo` :
的实现

```
public inline fun <T, R, C : MutableCollection<in R>> Array<out T>.mapTo(destination: C, transform: (T) -> R): C {
  for (item in this)
    destination.add(transform(item))
  return destination
} 
```

Enter fullscreen mode Exit fullscreen mode

### 滤镜

[源](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter.html)带我们到 [_Arrays.kt:3000](https://github.com/JetBrains/kotlin/blob/1.2.60/libraries/stdlib/common/src/generated/_Arrays.kt#L3000) ，它使用在 [3417](https://github.com/JetBrains/kotlin/blob/1.2.60/libraries/stdlib/common/src/generated/_Arrays.kt#L3417) 行定义的`filterTo`。

下面是`filterTo` :
的实现

```
public inline fun <T, C : MutableCollection<in T>> Array<out T>.filterTo(destination: C, predicate: (T) -> Boolean): C {
  for (element in this) if (predicate(element)) destination.add(element)
  return destination
} 
```

Enter fullscreen mode Exit fullscreen mode

### 减少

[源](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce.html)带我们去 [_Arrays.kt:11384](https://github.com/JetBrains/kotlin/blob/1.2.60/libraries/stdlib/common/src/generated/_Arrays.kt#L11384) 。

*注意:除了累加器的初始值是给定列表中的第一个元素*之外，Reduce 与 Fold 做同样的事情

下面是`reduce` :
的实现

```
public inline fun <S, T : S> Array<out T>.reduce(operation: (acc: S, T) -> S): S {
  if (isEmpty())
    throw UnsupportedOperationException("Empty array can't be reduced.")
  var accumulator: S = this[0]
  for (index in 1..lastIndex) {
    accumulator = operation(accumulator, this[index])
  }
  return accumulator
} 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所见，映射、过滤和归约都使用`A loop for every operator`方法，而不是将操作排队并使用`Many operators, one loop`方法。

## 科特林列表算子是否高效？

所以我们又回到了我们的主要问题——这些操作符有效率吗？
我将在上下文中定义单词`efficient`的意思是使用`Many operators, one loop`方法。

那么很明显，没有！Kotlin 列表操作符效率不高。

## 有何不可？

语言实现者往往是非常精明的人，他们不可能从未想到过我上面描述的场景。

让我们参与一些[辩护](https://www.google.com/search?q=apologetics&oq=apologetics&aqs=chrome..69i57j0l5.1969j0j7&sourceid=chrome&ie=UTF-8)并思考为什么 Kotlin 的列表操作符是这样实现的——可能有一个原因！

### 不纯函数

有一天，我和我的朋友克里斯·博林谈论这个问题，他提到了一个令人困惑的因素:全球变量。

如果您的 map 和 filter 函数先写入一个在操作符范围之外定义的变量，然后再从中读取，会怎么样呢？给定相同的接口或契约，操作符的结果会根据它们的实现细节而改变。绝对不是你想发生的事情！

(也描述了为什么函数可以根据世界的状态给出不同的答案，也就是不纯的函数，是一个坏主意)

### 他们不是为了效率而建造的

也许 Kotlin 的列表操作符效率不高的另一个原因是它们本来就不应该如此。就像所有与性能相关的事情一样，在它成为问题之前，它都不是问题。也就是说，在效率很重要的情况下，谁会在乎你运行了多少次 for 循环呢？

*也许* Kotlin 对于少量数据有一个效率较低的列表类型，而对于大量数据有一个不同的列表类型？*也许*语言实现者有意识地决定将高效操作分离到他们自己的特殊概念中？

下一次，我们将看看[序列](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/-sequence/index.html)的内部——一个列表类型就是这样做的！