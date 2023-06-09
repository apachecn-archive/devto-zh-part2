# scala 的第一步——第一部分:hello world

> 原文：<https://dev.to/bettyes/first-steps-in-scala---part-i-43j1>

# 从一开始

这一部分是为渴望迈出 Scala 第一步，但不太确定如何开始的新手准备的。我将从一些简单的“hello world”示例开始介绍变量和方法。在下一部分中，我将更多地探索对象。我一直在使用 IntelliJ IDE，并且在基于 Unix 的系统上工作。

要为 Scala 设置 IntelliJ，请看这里的。这里涉及的许多概念可以在[教程点](https://www.tutorialspoint.com/scala/index.htm)找到。最初这篇指导性文章已经发表在我的[私人网站](https://schirrmeister.wordpress.com/2018/10/27/first-steps-in-scala-part-i/)上

### **基础知识**

*   Scala 是面向对象和函数式的
*   每个值都是一个对象
*   每个函数都是一个值
*   每个函数都是一个对象(困惑？函数用“val”创建，方法用“def”创建，是类/对象的一部分。暂时不要太担心它。如果你有，请阅读这里的
*   不需要冗余的类型信息
*   Java 和 Scala 最大的区别在于“；”线端是可选的
*   CamelCase 命名约定(每个新单词以大写字母开头)函数名以小写字母开头:getValue 类名以大写字母开头:Value Class

对 Java 有所了解对 Scala 入门绝对有帮助。这里使用的 Scala 组件简而言之

*   **对象** -有状态和行为，例如一辆汽车可能有状态:颜色、品牌和行为:移动
*   **类** -用于创建对象的模板
*   **方法**——基本上是行为。它们可以作为函数独立于对象出现。
*   **字段** -对象的实例变量

要运行 scala，你可以选择交互模式(在终端中)或者脚本模式(在 IDE 中)。

要运行交互模式:(1)打开你的终端，(2)安装 scala: `brew install scala`(如果需要安装 brew 点击[这里](https://brew.sh/))，(3)运行 scala: `scala`，(4)用 ctrl+c 停止 scala

### **一句经典:“你好，世界！”**

让我们看看我们的第一个 hello world 示例。每个应用程序都需要一个入口点，即 main 方法。在 scala 中，如果你手动创建这个方法，它将在一个对象中(关于启动你的项目的其他方法，请看这里的)。在脚本模式下创建一个新的文件 helloworld.scala，它应该包含一个带有 main 方法的对象。在那里你放置你的印刷论点。(使用`println()`以新行结束，否则使用`print()`)。

helloworld.scala 的内容

```
object HelloWorld { 
def main(args: Array[String]) {
println("Hello, world!") }
} 
```

你可以直接在 IntellyJ 的控制台中运行这个文件，或者在终端中通过`cd ~/path/FolderContainingTheFile/`并键入`scala helloworld.scala`来运行。

与 Java 相反，你可以去掉“；”在一行的末尾。但是，您可以将其作为语句之间的换行符。将字符串赋给变量也不一定需要声明数据类型。变量本身可以是**可变的** (var)和**不可变的** (val)。

让我们再次运行“hello world ”,但这次包括上面提到的概念:

```
object HelloWorld {
def main(args: Array[String]) {
val s: String = ”Hello, world!"; println(s)
  }
} 
```

最后，我们还可以将 print 语句移到一个函数中，然后在我们的 main 方法中调用它。

```
object HelloWorld {
  def main(args: Array[String]) {
    val name: String = "Joe"
    hello(name)
  }

  def hello(s: String ): Unit ={
    println(s"Hello, $s!")
  }
} 
```

请注意，在上面的 print-statement 中，通过在"前面添加' s '，我们能够在一个字符串中调用一个变量(${variablereference})，称为[字符串插值](https://docs.scala-lang.org/overviews/core/string-interpolation.html)。

在 IntelliJ IDE 中，它应该是这样的:

[![hello world in IntelliJ](img/089f88c2fb85f070350b634332ae0ec6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aKfZvYL2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wyzf2tm8g9mw316j1kzi.png)

你不用在终端中输入 scala helloWorld.scala，只需在 IntelliJ 中点击 main 方法旁边的绿色“play”按钮就可以运行你的 helloWorld。

### **数据类型**

最后，下面是 scala 中使用的一些数据类型。关于数字，你大部分时间都在处理 int(整数)和 double(分数):

*   **字节:**从-128 到+127 的 8 位有符号值
*   **短:** 16 位有符号值。范围从-32768 到+32767
*   **Int:** 32 位有符号值。范围从-2147483648 到+2147483647
*   **Long:** 64 位有符号值。-9 223 372 036 854 775 808 至+9 223 372 036 854 775 807
*   **浮点:** 32 位 IEEE 754 单精度浮点
*   **Double:** 64 位 IEEE 754 双精度浮点
*   **Char:** 16 位无符号 Unicode 字符。范围从 U+0000 到 U+FFFF
*   **字符串:**一系列字符
*   **布尔:**文字真或文字假

### **下一部分:**

在下一篇博客中，将会有更多面向对象的编程(包括优缺点)，创建一个 car 类，一个用于简单视觉输出的 grid 类，并探索一些标准输入。