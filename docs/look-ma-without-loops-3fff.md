# 看，妈妈，没有圈圈！

> 原文：<https://dev.to/chrisvasqm/look-ma-without-loops-3fff>

<center>*Cover image by [@jordaneil](https://unsplash.com/@jordaneil) from [Unsplash](https://unsplash.com/)*</center>

* * *

## 读之前

读者先生/女士，我强烈建议您熟悉以下概念，以便全面理解本文:

1.  功能
2.  班级
3.  目标
4.  属性/字段/成员变量
5.  循环(`for`、`while`、`for-each`)

* * *

在我大学最初的几堂编程课上，老师向我介绍了一个练习/挑战，他说:

> 从存储在数组中的某一科目的所有学生成绩中，找出高于和低于平均值的人数

他给了我们如何开始的线索，因为他最近向我们展示了变量、函数和循环。

我首先想到的是:

> “我们可以不时地使用一些 for-each 循环...等等！不。让我们不要使用 for/while 循环！”

和任何认为唯一存在的东西是 JVM 的现代开发人员一样，我尝试在 Kotlin 中这样做🤓

(什么？你以为我会用 Java 吗？原谅你！)

## 计划

像任何千禧一代一样，我总是带着购物清单去杂货店，这样我就不会忘记任何东西。那么，让我们计划一下这个练习需要做些什么:

1.  我们需要一种在代码中表示`Student`的方法。简单来说，声明一个具有两个属性的`class`:`name`和`score`。
2.  稍后，我们将需要把它们中的每一个存储在我们将要操作的数组中。
3.  我们需要计算所有这些的`average`。
4.  然后我们可以用这个值与数组中的每一个`Student`进行比较，来计算有多少个在`average`之上和之下。

## 执行

### 制作`Student`类

为什么？因为我们可以！(一旦你[最终理解了什么是类](https://dev.to/chrisvasqm/how-i-finally-understood-what-a-class-is--24pl)，它实际上会使我们的代码更容易阅读)

现在，记住:我们需要有两个属性:`name`和`score`。他们的数据类型是什么？

是的，你完全正确！`name`将成为`String`，`score`将成为`Int`。

下面是代码的样子:

```
class Student(val name: String, val score: Int) 
```

Enter fullscreen mode Exit fullscreen mode

*漂亮！*

> *对于那些不熟悉`val`关键字的人来说，这意味着这个属性是只读的，所以它的值只能被读取，在它被设置之后，任何人都不能修改它。*

### 制造学生之阵

在这一步，我将使用科特林的“巫毒魔法”让我们的`Array<Student`发生，它看起来是这样的:

```
fun main(args: Array<String>) {
    val students = arrayOf(
            Student("Chris", 80),
            Student("Mark", 67),
            Student("Joseph", 98),
            Student("Carl", 74),
            Student("Katherine", 86)
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

*乌拉拉！*

这里我们使用了标准库提供的`arrayOf()`函数，它充当了一个[语法糖](https://dev.to/chrisvasqm/its-just-syntactic-sugar-apo)，在这里我们传入一堆我们将使用的`Student`对象(如果你愿意，可以随意修改任何`names`或`scores`)。

注意，我们不必为我们的`Array<T>`指定数据类型，因为编译器可以从我们传递给这个函数的每个参数中推断出它。

### 寻找平均值

这就是乐趣所在，因为我们不想使用任何`for` / `while` / `for-each`循环，我们将依赖一些高阶函数。

#### 介绍...`sumBy {...}`

该函数接受一个表达式(或者更具体地说是 Lambda 表达式),该表达式将用于基于该表达式计算单个值。

耶，耶，耶...我知道。听起来真的很无聊。

我们来看看:

```
students.sumBy { it.score } 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里所做的是迭代我们的`students`数组中的每个个体`Student`(我们称之为“它”)，然后只取它们的`score`，然后将它们加在一起得到**一个单一值**。

所以，我们可以用这个来计算`average`。

为了做到这一点，我们不仅需要将所有的分数加在一起，还要除以`students`的数量。

我们可以通过简单地调用`students.size`来获得该值，在本例中，这将为我们提供`5`。

如果我们把它们放在一起，我们会得到类似于:

```
 fun calculateAverageScore(students: Array<Student>) = students.sumBy { it.score } / students.size 
```

Enter fullscreen mode Exit fullscreen mode

这里我使用了一个函数，以便为我们将要做的操作添加更多的含义。

如果你想知道，如果我们的函数只有一行那么长，我们可以去掉花括号(`{`，`}`)和`return`关键字。

现在我们可以使用我们可靠的`calculateAverageScore()`函数来帮助我们，就像这样:

```
fun main(args: Array<String>) {
    val students = arrayOf(
            Student("Chris", 80),
            Student("Mark", 67),
            Student("Joseph", 98),
            Student("Carl", 74),
            Student("Katherine", 86)
    )

    val average = calculateAverageScore(students)
}

private fun calculateAverageScore(students: Array<Student>) = students.sumBy { it.score } / students.size 
```

Enter fullscreen mode Exit fullscreen mode

*牛逼！*

### 有多少是中上水平？

为此，我们可以使用`filter {...}`函数。

与`sumBy {...}`类似，这个函数接受一些人所谓的**谓词**，这是一个返回`Boolean` ( `true`或`false`)值的表达式。

在我们的例子中，它看起来像这样:

```
val aboveAverage = students.filter { it.score > average } 
```

Enter fullscreen mode Exit fullscreen mode

但是`filter`有一点特别，它不会返回单个值。它实际上会返回一个分数高于平均值的新数组`Students`。但我们只需要数一数有多少。

还记得我们如何使用`students.size`属性吗？

Oh yeeeeeaaaah!

我们也可以在`filter`块之后使用:

```
val aboveAverage = students.filter { it.score > average }.size 
```

Enter fullscreen mode Exit fullscreen mode

_ 太棒了！

### 低于平均水平的有多少？

我想你可能已经知道怎么做了...作为一名优秀的软件开发人员，我们可以相信自己的本能，直接复制/粘贴我们之前的代码

```
val belowAverage = students.filter { it.score < average }.size 
```

Enter fullscreen mode Exit fullscreen mode

我们必须改变什么？

*   `aboveAvereage`到`belowAverage`。
*   `<`操作符的`>`操作符。

就是这样！

## 最后的考验

现在我们只需要试一试。但是！为了显示结果，我们还没有做任何事情...

让我们通过在末尾添加这个来解决这个问题:

```
println("With an average of $average, there are $aboveAverage students above average and $belowAverage below it.") 
```

Enter fullscreen mode Exit fullscreen mode

*太好了！*

如果您密切关注每一步，您应该会看到如下内容:

```
fun main(args: Array<String>) {
    val students = arrayOf(
            Student("Chris", 80),
            Student("Mark", 67),
            Student("Joseph", 98),
            Student("Carl", 74),
            Student("Katherine", 86)
    )

    val average = calculateAverageScore(students)

    val aboveAverage = students.filter { it.score > average }.size

    val belowAverage = students.filter { it.score < average }.size

    println("With an average of $average, there are $aboveAverage students above average and $belowAverage below it.")
}

private fun calculateAverageScore(students: Array<Student>) = students.sumBy { it.score } / students.size 
```

Enter fullscreen mode Exit fullscreen mode

并且输出应该是:

```
With an average of 81, there are 2 students above average and 3 below it. 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 重述:

我们通过使用 Kotlin 标准库提供的`filter`和`sumBy`高阶函数，在我们定制的 Lambda 表达式的帮助下，避免了任何常规循环，使我们的代码非常干净简洁。

这些功能不仅仅是我们在 Kotlin 中可以使用的，许多其他语言都支持这种功能，你只需谷歌一下如何在其中的每个特定语言中编写它们，但它们确实是相似的，所以不要担心:)

<center>*You can checkout the full sample project [here](https://github.com/chrisvasqm/look-ma-without-loops)*</center>