# scala 的第一步——第 2 部分:对象

> 原文：<https://dev.to/bettyes/first-steps-in-scala---part-2-2llh>

这是我的一个非常基本的尝试，来帮助 scala 初学者开始他们的第一个项目，包括对象。这个也已经发表在我的[个人网页](https://schirrmeister.wordpress.com/2018/11/08/first-steps-in-scala-part-2/)上了。在我之前的[博客](https://dev.to/bettyes/first-steps-in-scala---part-i-43j1)中，我的目标是让每个人开始使用智能智能中不同的“hello world”方法。这包括简单的打印语句、函数和变量。在下面的帖子中，这是关于:

## **介绍对象**

有许多关于面向对象编程的博客，以及潜在的优点和缺点。让我试着总结一下我对其中一些的看法。与[过程化编程](https://en.wikipedia.org/wiki/Procedural_programming)(来自 20 世纪 60 年代:Fortran、C、Pascal)的焦点围绕**动作**(函数)相反，面向对象编程(来自 1995 年:Java)完全是关于存储在**对象**中的数据。这些对象的可重用性使得面向对象编程(OOP)成为大型项目的一个好方法。就我个人而言，我喜欢 OOP 项目的清晰结构，但请在评论部分随意同意或不同意；-) .每个对象都是使用一个**类**(对象的模板)构建的，并带有自己的变量(数据存储)，称为**‘字段’**和自己的函数，称为**‘方法’**(对象的行为)。这个术语也很容易区分(I)对象链接变量(字段)和函数(方法)以及(ii)独立变量和函数。

简单来说:对象封装数据，定义可以应用于这些数据的函数。这太棒了，因为你不必担心在哪里应用一个函数，因为它们显然与特定的对象/数据相关联。让我们看一个非常简单的例子:

### **我的第一个对象:一辆车**

我的汽车对象将有字段:**颜色，品牌和位置**和方法:**移动()**
下面看一些简单的代码来构建你的第一个汽车对象。我把这个项目命名为“停车场”。它包含一个 main 方法，我们程序的入口点总是以`main(args:Array[String])`开始。通常，没有 main-method，独立的应用程序就无法运行，任何逻辑都将从这里开始。在 main 方法中，我们将创建一个 car 对象(基于 Car 类中定义的模板)，名为“fiat ”,并在 print 语句中检查它的字段、颜色、品牌和位置(print 语句嵌入了一个变量引用(`s"$variable"`)。我在我的[上一篇文章](https://dev.to/bettyes/first-steps-in-scala---part-i-43j1)中已经提到过[字符串插值](https://docs.scala-lang.org/overviews/core/string-interpolation.html)。).Car-class 为我们的 Car 对象定义了特性，使我们能够构建尽可能多的汽车，这些汽车将共享字段:**颜色**(用户定义的，不可变的)**制造**(用户定义的，不可变的)和**位置**。

```
object carPark {
  def main(args: Array[String]) {
    val fiat = new Car("red","Fiat");
    println(s"a ${fiat.color} car of make ${fiat.make} 
is now standing at position ${fiat.position.mkString(",")}.")
  }
}

class Car(col: String, mk: String) {
    val color: String = col
    val make: String = mk
    var position = new Array[Int](2);
    position(0) = 0
    position(1) = 0
} 
```

添加函数 **move()** 实际上将使我们能够访问 car 类中 car.position 中存储的可变数据，并转换这些数据。如果你想象一个网格(x，y ),我们现在可以在这个网格内从位置(0，0)移动我们的汽车到位置(2，2)。

```
object carPark {
  def main(args: Array[String]) {
    val fiat = new car("red","Fiat")

    // Move to a new location
    fiat.move(2, 2)
    println(s"a ${fiat.color} car of make ${fiat.make} 
is now standing at position ${fiat.position.mkString(",")}.")
  }
}

  class Car(col: String, mk: String) {
    val color: String = col
    val make: String = mk
    val position = new Array[Int](2)
    position(0) = 0
    position(1) = 1

    def move(dx: Int, dy: Int) {
      position(0) = position(0) + dx
      position(1) = position(0) + dy
    }
  } 
```

* * *

*** ***随意运动*** ***

如果你想尝试建造你自己的物品。您可以尝试添加第二辆汽车，逐步增加复杂性:
*(1)汽车对象具有与第一辆
相同的变量和方法(2)汽车不应该能够移动到相同的位置
如果发生这种情况，将第一辆汽车移动到前一辆汽车旁边的 y 位置，并发送一条消息，表明所选位置已被占用，它已停在右侧
(3)想象您只有 5*5 个位置可用，可以使用以下位置 x in (0，4)和 y in (0，4)。确保第二辆车不会掉出网格。*

当然这只是一个关于如何开始的建议。有很多方法可以增加这个例子的复杂性。
添加视觉输出

* * *

对于汽车位置的简单可视化解决方案，您可以添加一个网格类来显示它的位置(我用“X”标记了汽车的位置):

```
// main class with instructions what to do:
// instantiate car, names fiat and move it one position 
// towards x and y
// create a grid and display

object carPark {
  def main(args: Array[String]) {
    val fiat = new Car("red","Fiat")

    // Move to a new location
    fiat.move(2, 2);
// at the moment show method works best for to 5*5
    val streetMap = new Grid(5,5) 
    streetMap.updatePosition(fiat.position(0),fiat.position(1))

    streetMap.show()
  }
}

// build a grid class to visually display position of the car
// field of this class: row, col, streetMap
// methods: updatePosition, show

import Array._
class Grid(r:Int, c: Int) {
  val row: Int = r
  val col: Int = c
  var streetMap = ofDim[String](row, col)
  for (i <- 0 to row-1) {
    for (j <- 0 to col-1) {
      streetMap(i)(j) = " "
    }
  }

  def updatePosition(posX: Int, posY: Int): Unit = {
    streetMap(posX)(posY) = "X"
  }

  def show(): Unit = {
    println("=========================")
    for (i <- 0 to row-1) {
      print("|")
      for (j <- 0 to col-1) {
        print(" " + streetMap(i)(j)+" | ")
      }
      println()
      println("=========================")
    }
  }
}

// car class as shown earlier 
class Car(col: String, mk: String) {      
    val color: String = col     
    val make: String = mk         
    val position = new Array[Int](2);  
    position(0) = 0
    position(1) = 0   

    def move(dx: Int, dy: Int) {       
    position(0) = position(0) + dx
    position(1) = position(1) + dy 
    }   
} 
```

在网格类中，我使用了一个[多维数组](https://alvinalexander.com/scala/how-to-create-multidimensional-arrays-in-scala-cookbook)。多维[数组](https://docs.scala-lang.org/overviews/collections/arrays.html)将数据存储在一个矩阵中。首先看一下数组可能是值得的。在上面的例子中，car.position 是一个数组。scala 中的数组是数据的集合，就像 java 中的数组一样。不同数组的示例:(I) `var numbers: Array[Int] = Array(1,2,3,4)` (II) `var strings: Array[String] = Array("hello","goodbye", "servus")`

运行整个程序时，您应该会看到类似这样的内容:

[![output1](img/541a3e8a1f589c3286621db0dda3f5d5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--50vw6IJE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/80q8vucj7h4x7nnurzmg.png)

### **让它对用户友好——从命令行添加标准输入**

最后，如果你想让它更具交互性，我们可以让用户输入他喜欢的坐标，方法是在 car 类中添加一个允许用户输入的方法。而不是在主方法中调用`fiat.readCoordinates(); fiat.move(fiat.position(0),fiat.position(1))`

```
def readCoordinates(carName: String){
  println(s"where do you want to move your $carName?\n Please enter X     position:")
  val dx  = scala.io.StdIn.readInt()
  println("Please enter Y position")
  val dy = scala.io.StdIn.readInt()
  coord = Array(dx,dy)
} 
```

`scala.io.readInt()` [https://www.scala-lang.org/api/2.12.2/scala/io/StdIn$.html](https://www.scala-lang.org/api/2.12.2/scala/io/StdIn%24.html)命令允许用户输入整数。使用`scala.io.readLine()`读入一个字符串。

现在，如果你想从命令行运行你的程序，只需转到`yourProject/target/scala*/classes/`并键入`scala yourProject`，你应该被要求输入 x 和 y 坐标。请记住，目前网格设置为 5*5 字段，所以您的坐标应该在[0，4]之间。

[![commandline](img/48d3cc29c86a5f55e1d5121f4f364386.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nFAiUPAN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ue2raljwotg5cur0fon9.png)

你可以访问整个项目，包括在 [github](https://github.com/BettyES/carPark) 上的第二辆车的解决方案。

### **面向对象编程概述**

既然我们已经看到了数据和相关的方法是如何存储在对象中的，那么封装的概念应该更清楚了。一般来说，面向对象编程遵循[四大原则](https://medium.freecodecamp.org/object-oriented-programming-concepts-21bb035f7260)，包括**封装**、**抽象**、**继承**和**多态**。我将简短地描述这些概念。看上面的例子:(I)我们有**封装的**数据和与类 car 相关的方法，比如颜色、品牌、位置和移动。(二)**抽象**表示实现细节被隐藏。例如，move 方法接受两个数字并返回一个位置。我们看不到实际的动作(尽管在这种情况下非常简单)是如何发生的。(iii)我们没有在停车场项目中使用**继承**和**多态性**的概念。继承简而言之，如果我们有一个包含颜色、轮子和方法移动等字段的 Vehicles 类，我们可以将其细分为 Cars、Bicycles 和三轮车。例如，汽车将继承颜色、车轮和移动字段，但可能会添加燃料、发动机和方法等字段，如 getFuel。如果你热衷于了解这些概念，看看这个不错的博客。

最后一句话:尽管我将这篇博客的重点放在了对象上，scala 也是**函数式的**！...但这将是另一个博客的话题。

请继续关注更多 scala 初学者博客，包括测试、数据结构等。