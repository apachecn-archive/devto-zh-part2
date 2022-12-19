# Scala 泛型 III:通用类型约束

> 原文：<https://dev.to/apium_hub/scala-generics-iii-generalized-type-constraints-58km>

这是我们 Scala 泛型系列的第三篇文章(我们已经查看了[Scala 类型上限和下限](https://dev.to/apium_hub/scala-generics-i--scala-type-bounds-38)、[协方差和逆变](https://dev.to/apium_hub/scala-generics-ii-covariance-and-contravariance-in-generics-5dib))，今天我们将讨论约束，更具体地说是广义类型约束。

在 [Generics I](https://dev.to/apium_hub/scala-generics-i--scala-type-bounds-38) 中，我们讨论了类型界限和使用站点差异，我们还讨论了对抽象类型的控制，但是有些方法需要确保类的抽象类型只在该方法中满足某些限制。

今天我们将学习这一小组课程:

```
trait Thing
trait Vehicle extends Thing
class Car extends Vehicle
class Jeep extends Car
class Coupe extends Car

class Motorcycle extends Vehicle

class Vegetable 
```

我们将像往常一样处理停车问题。

```
class Parking[A <: Vehicle](val v: A){
  def park: A = v
} 
```

在这个例子中，parking 方法可以返回任何类型的车辆，正如 Parking 的类型上限所指定的那样，但是如果我们希望有特定的逻辑来停放汽车和摩托车，会发生什么呢？

```
class Parking[A <: Vehicle](val v: A) {
  def parkMoto(): A = {
    println("moto") // this could call some public method of Motorcycle
    v
  }
  def parkCar(): A = {
    println("car") // this could call some public method of Car
    v
  }
} 
```

f

在这些情况下，我们希望确保 A 对于 parkMoto 是摩托车类型，对于 parkCar 是汽车类型，对吗？

如果我们记得来自 [Generics I](http://hub.com/tech-blog-barcelona/scala-type-bounds/) 的一些东西，我们将会看到有两种方法来做类似的事情:方法上的类型界限和使用地点差异。

让我们试试类型界限！

让我们尝试为 parkMoto 和 parkCar 方法添加边界:

```
class Parking[A <: Vehicle](val v: A) {
  def parkMoto[A <: Motorcycle] = {
    println("moto") // this could call some public method of Motorcycle
    v
  }
  def parkCar[A <: Car] = {
    println("car") // this could call some public method of Car
    v
  }
} 
```

如果你把它放在 IDE 中，这会给你一些线索…可疑的阴影…但是没关系，它会编译，我们会试试！

```
val p1 = new Parking(new Motorcycle)
  p1: Parking[Motorcycle] = Parking@193f604a

p1.parkCar
  res5: Motorcycle = Motorcycle@5562c41e

p1.parkMoto
  res6: Motorcycle = Motorcycle@5562c41e 
```

似乎那些类型界限没有做任何事情。显然，IDE 给我们的线索是:类型参数的可疑隐藏意味着我们正在重新定义类型参数。

结果是我们没有给 A 增加界限，而是定义了一个新的类型 A …

如果我们输入这个方法的返回结果。

```
class Parking[A <: Vehicle](val v: A) {
  def parkMoto[B <: Motorcycle]: B = {
    println("moto") // this could call some public method of Motorcycle
    v
  }
}
  <console>:13: error: type mismatch;
      found : Parking.this.v.type (with underlying type A)
      required: B
                v
                ^ 
```

这还不够，因为我们增加了对返回类型的限制..但是我们想和 v: A 合作

换句话说，限制不应该在一个新的类型 B 上进行，而是在类中定义的类型 A 上，似乎类型绑定对我们不起作用…

让我们尝试使用使用位置差异，如果我们还记得的话，使用位置差异允许我们在定义泛型类型的时候定义它的约束:

```
class Parking[A](val v: A) {}

def parkMoto(parking: Parking[_ <: Motorcycle]) = {
  println("moto") // this could call some public method of Motorcycle
  parking.v
}
def parkCar(parking: Parking[_ <: Car]) = {
  println("car") // this could call some public method of Car
  parking.v
} 
```

看起来不错，让我们来看看:

```
parkCar(new Parking(new Car))
  res1: Car = Car@17baae6e

parkCar(new Parking(new Motorcycle))
  <console>:14: error: type mismatch;
    found : Motorcycle
    required: Car
    parkCar(new Parking(new Motorcycle))
                            ^ 
```

这似乎是一个解决方案，尽管我们不得不牺牲一些东西…我们使用的方法 parkMoto 和 parkCar 在停车之外，传递一个停车作为参数..另外，开闭原则已经被调用 parking.v(告诉不要问)打破了。

虽然它有效，但对于我们的问题来说，这是一个非常糟糕的解决方案。

这就是广义类型约束发挥作用的地方:

三个现有的通用类型约束是=: =、<: ev:="" t=":" b=""/>

这些隐式参数，通常称为 ev(“证据”)是测试，表明一个类型满足某些限制。

这些约束可以以不同的方式使用，但最有趣的是，它们允许我们以相同的方法限定类的类型参数:

```
class Parking[A <: Vehicle](val v: A) {
  def parkMoto(implicit ev: A =:= Motorcycle) { println("moto") }
  def parkCar(implicit ev: A =:= Car) { println("car")}
} 
```

通过使用=:=我们实现了一个抽象类型，比如 Parking，强制它的类型参数成为不同方法的特定参数。

如果我创造一辆停车车，并把它命名为 parkMoto，会发生什么？

```
val p1 = new Parking(new Car)
  p1: Parking[Car] = Parking@5669f5b9

p1.parkCar

p1.parkMoto
  <console>:14: error: Cannot prove that Car =:= Motorcycle.
    p1.parkMoto
      ^ 
```

事实上，我们已经设法让方法只在类型参数满足某些限制时才起作用。

广义类型约束主要用于确保特定的方法具有具体的约束，以便某些方法可以用于一种类型，而其他方法可以用于另一种类型。

然而，由于[式的擦除](https://en.wikipedia.org/wiki/Type_erasure)，我们无法重载一个方法:

```
class Parking[A <: Vehicle](val vehicle: A) {
  def park(implicit ev: A =:= Motorcycle) { println("moto") }
  def park(implicit ev: A =:= Car) { println("car") }
}
  method park:(implicit ev: =:=[A,Car])Unit and
  method park:(implicit ev: =:=[A,Motorcycle])Unit at line 12
  have same type after erasure: (ev: =:=)Unit
             def park(implicit ev: A =:= Car) {} 
```

另一个奇怪的用例可能是下一个:我想要一个为同类车辆停车的方法:

```
class Parking[A <: Vehicle](val vehicle: A) {
  def park2(vehicle1: A, vehicle2: A) {}
} 
```

正如您已经推断的那样，这是不够的，因为这两辆车可以是任何类型的车辆，如果我们创建的停车场是新停车场(新车)，我们可以一次停放一辆吉普车和一辆轿车。

解决方案是一个通用的类型约束:

```
class Parking[A <: Vehicle] {
  def park2[B, C](vehicle1: B, vehicle2: C)(implicit ev: B =:= C) {}
} 
```

现在让我们来试试:

```
val p2 = new Parking[Car]
  a: Parking[Car] = Parking@57a68215

p2.park2(new Jeep, new Jeep)

p2.park2(new Jeep, new Coupe)
  <console>:15: error: Cannot prove that Jeep =:= Coupe.
  a.park2(new Jeep, new Coupe)
              ^ 
```

现在，车辆 1 必须与车辆 2 的类型相同，但是…

```
p2.park2(new Vegetable, new Vegetable) 
```

哎呀…我们失去了车辆约束。让我们修理它！打出界来救援！

```
class Parking[A <: Vehicle] {
  def park2[B <: A, C](vehicle1: B, vehicle2: C)(implicit ev: B =:= C) {}
}

val p3 = new Parking[Car]
p3.park2(new Vegetable, new Vegetable)
  <console>:14: error: inferred type arguments [Vegetable,Vegetable] do not conform to method park2's type parameter bounds [B <: Car,C]
      p3.park2(new Vegetable, new Vegetable)
      ^
    <console>:14: error: type mismatch;
          found : Vegetable
          required: B
          p3.park2(new Vegetable, new Vegetable)
                    ^
    <console>:14: error: type mismatch;
          found : Vegetable
          required: C
          p3.park2(new Vegetable, new Vegetable)
                                   ^
    <console>:14: error: Cannot prove that B =:= C.
          p3.park2(new Vegetable, new Vegetable)*/

p3.park2(new Jeep, new Coupe)
  <console>:15: error: Cannot prove that Jeep =:= Coupe.
         p3.park2(new Jeep, new Coupe)
                     ^                            

p3.park2(new Jeep, new Jeep) 
```

现在 Parking2 方法只能接收两个相同的类型，它们也必须是 A 或 A 的子类型，已修复！

最后，让我们看看另外两个通用类型约束:

正如您可能猜到的，A <: b="" lt=""/>

它的用法和 with =:=完全一样，有一个隐含的“证据”。

在前面的 parkCar 和 parkMoto 的例子中，如果我们不仅要停放汽车和摩托车，还要停放汽车和摩托车的子类型，我们就不要使用 this =:=，而是使用 this:

```
class Parking[A <: Vehicle](val v: A) {
  def parkMoto(implicit ev: A <:< Motorcycle) { println("moto") }
  def parkCar(implicit ev: A <:< Car) { println("car")}
} 
```

当然，在接收两个类型参数的情况下，我们可以强制其中一个应该是另一个的子类型:

```
class Parking[A <: Vehicle] {
  def park2[B <: A, C](vehicle1: B, vehicle2: C)(implicit ev: B <:< C) {}
} 
```

最后一个通用类型约束是< %

对于那些想了解更多关于广义类型约束的信息，想知道它们内部是如何工作的，想和类型点做更多比较的人。，强烈推荐看这个帖子。

这三篇 Scala 泛型文章只是对泛型的介绍。我们看了一些复杂的作品，我应该说我们已经达到了相当深的层次，然而我们只是触及了表面。我们甚至还没有进入他们的实现是什么！！

我希望在不久的将来有时间写更多关于 Scala 的文章！

## 如果你觉得这篇关于泛型中协方差和逆变的文章很有趣，你可能会喜欢…

[Scala 泛型 I: Scala 类型界限](https://dev.to/apium_hub/scala-generics-i--scala-type-bounds-38)

[Scala generics II:协方差和逆变](https://dev.to/apium_hub/scala-generics-ii-covariance-and-contravariance-in-generics-5dib)

BDD:用户界面测试

[Scala 中泛型类型的 F-bound](https://apiumhub.com/tech-blog-barcelona/f-bound-scala-generics/)

[微服务 vs 整体架构](https://apiumhub.com/tech-blog-barcelona/microservices-vs-monolithic-architecture/)

[“几乎无限”的可扩展性](https://apiumhub.com/tech-blog-barcelona/almost-infinite-scalability/)

帖子 [Scala Generics III:通用类型约束](https://apiumhub.com/tech-blog-barcelona/scala-generics-generalized-type-constraints/)首先出现在 [Apiumhub](https://apiumhub.com) 上。