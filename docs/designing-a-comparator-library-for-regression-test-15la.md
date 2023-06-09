# 为回归测试设计比较器库

> 原文：<https://dev.to/namnguyen/designing-a-comparator-library-for-regression-test-15la>

# 回归测试

让我们引用维基百科对回归测试的定义:

> 回归测试是一种软件测试，它验证先前开发和测试的软件在被改变或与其他软件接口后仍然以相同的方式执行。变更可能包括软件增强、补丁、配置变更。

这种测试在数字图书馆中很普遍。在这样的项目中，开发人员希望确保程序的输出(数值)不会改变。好处是:

*   安全重构:只要测试通过，开发人员就不会害怕移动代码，重命名代码
*   Controle:开发者可以控制他们对库的修改。他们希望只看到库的某个部分的差异

# 回归测试工作流程

我们可以把我们的软件看作一个接受输入并产生输出的功能。回归测试并不保证输出的正确性，它只告诉输出自上次运行以来是否被修改过。

回归测试的常见实现包括:

1.  程序输入的集合。它通常以文本文件的形式存在。
2.  输出的集合，其中`output = f(input)`。输出保存在某个地方
3.  当测试运行时，它检查集合中的所有输入，应用函数并将结果与存储在文件系统中的输出进行比较。

如果测试通过，那么恭喜你，没什么可做的。然而，如果失败了呢？在这种情况下，您应该分析参考值和新值之间的差异。比较器的质量对于加速这一过程至关重要。让我们来看一个例子:

## 例 1:

您有两个对象，它们是由数千个元素组成的列表。如果你使用标准的 scala `==`，结果你只有`True`或者`False`。但是，有 1000 个元素，你想看看哪些元素被修改了。您需要这样的消息:`512 / 1.2 != 1.3`其中 512 是元素的位置

## 例二:

您有两个由
定义的 Toto 类型的对象

```
case class Toto(x: X, y: Y, z: Z) 
```

Enter fullscreen mode Exit fullscreen mode

比较器应该指出哪个字段包含两个对象之间的差异

`x / left != right`

比较两个案例类能力特别有用。如果计算代表重要的步骤，我们希望输出所有的中间步骤，这样我们就可以控制 bug 何时被引入系统。

当比较两个 doubles 时，比较器应该考虑一个可接受的误差，因为我们在数值计算中总是有数值误差:只有函数的一点点重排才能导致一些 1e-12 的差异。

现在，当回归测试失败时，有两种可能性:你要么发现你做的是错的并改正它，要么你认为新的值是更好的值。对于后一种情况，您必须将引用值更新到最新版本。

更新参考值有风险，您应该对新值有把握。CVS 应跟踪参考值。

# 设计比较器

比较器是集成测试的中心部分，也是库`comparator`的目的。

## API

在这一节中，我将一步一步地描述我在实现这个库时遵循的方法。

首先要问的问题当然是:如果 API？我们想要比较两个值，输出是这两个值之间的差异列表

```
Comparator.compare[A](left: A, right: A): List[Diff] 
```

Enter fullscreen mode Exit fullscreen mode

函数签名告诉我们，它接受两个相同类型的值，并返回这些值之间的差异列表。

`Diff`必须提供 2 个事实:差异的位置和差异。例如，当比较两个列表时，应该指定这些不同元素的位置

```
case class Diff(paths: List[String], diff: ComparatorDiff) 
```

Enter fullscreen mode Exit fullscreen mode

这个类可以输出如下内容:`data / 0 / x: 5 is not equal to 6`

`ComparatorDiff`可以是`String`,但是很难测试...这就是为什么我选择使用某种类型的系统。

```
sealed trait Side
case object Left extends Side
case object Right extends Side

sealed trait ComparatorDiff

case class SizeDiff(left: Int, right: Int) extends ComparatorDiff
case class DoubleDiff(left: Double, right: Double) extends ComparatorDiff
case class KeyNotExist(key: String, side: Side) extends ComparatorDiff
case object TypeDiff extends ComparatorDiff 
```

Enter fullscreen mode Exit fullscreen mode

既然我们必须指定`path`，我们应该有一个方法来添加新的路径到子路径

```
def compareWithPath(path: String, left: A, right: A)(implicit err: AcceptanceError): List[Diff] = {
    val temp = compare(left, right)
    temp.map { diff => diff.copy(paths = path :: diff.paths)
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

既然我们选择了`List`来存储路径，那么`prepend`操作符就是`O(1)`

## 一些有用类型的比较器

该库提供了一些基本的类型类

```
implicit def doubleComparator(implicit err: AcceptanceError): Comparator[Double] = (left: Double, right: Double) => {
    val abs = Math.abs(left - right)
    val diff = Diff(Nil, DoubleDiff(left, right)) :: Nil
    if (left == 0.0 || right == 0.0)
      if (abs > err.absolute) diff else Nil
    else {
      val rel = abs / left
      if (rel > err.relative) diff else Nil
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

请注意，`doubleComparator`不是一个值，而是一个隐式函数，因为它需要一个`AcceptenceError`

另一个例子是`List`的比较器

```
implicit def listComparator[A](implicit c: Comparator[A]): Comparator[List[A]] = (left: List[A], right: List[A]) => {
    if (left.lengthCompare(right.size) != 0)
      Diff(Nil, SizeDiff(left.size, right.size)) :: Nil
    else {
      left.zip(right).zipWithIndex.flatMap {
        case ((l, r), index) =>
          c.compareWithPath(index.toString, l, r)
      }
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

隐函数`listComparator`的签名告诉我们，如果我们可以提供一个类型为`A`的比较器，那么编译器可以自动创建一个类型为`Comparator[List[A]]`的实例。隐式系统有利于减少大量样板代码

我建议你看看源代码中的比较器实例是`Option`和`Map`。

# 使用无形的力量

在最后一部分，我们定义了一些有用的类型类，它们可以由编译器以多种方式自动组合。然而，我们仍然有许多样板代码要写。例如，如果我们想比较元组呢？没有办法告诉编译器在不使用宏的情况下自动构造 tuple2，tuple3 的比较器。幸运的是，这就是强大的库 shapeless 的目的。

解释库`shapeless`超出了本文的范围。然而，我展示了如何使用`shapeless`来创建自动派生类型的类实例

强烈推荐[免费指南](https://github.com/underscoreio/shapeless-guide/blob/develop/dist/shapeless-guide.pdf)全面了解 shapeless。

shapeless 背后的理念是，它可以将任何对象(case 类)转换成通用表示。通用表示用于组成子类型类以创建父类型类。用户只需编写组合函数，让隐式机制与编译器一起自动创建所需的类型类。

无形状提供 2 个中间表示。

第一个是代表异构列表的`HList`，它将一个`case class`表示为一个元组。在这个表示中，我们没有关于场的信息。需要字段的名称，因为比较器应该显示差异的位置

第二个是`LablledGeneric`与`HList`类似，但带有字段名称的信息。例如，`case class Person(name: String, age: Int)`的通用表示是

```
String with KeyTag[Symbol with Tagged["name"], String] ::
Int with KeyTag[Symbol with Tagged["numCherries"], Int] ::
Boolean with KeyTag[Symbol with Tagged["inCone"], Boolean] ::
HNil 
```

Enter fullscreen mode Exit fullscreen mode

## 进入点

隐式搜索的入口点:

```
implicit def genericComparator[A, H](implicit
    generic: LabelledGeneric.Aux[A, H],
    hEncoder: Lazy[Comparator[H]]): Comparator[A] =
    (left: A, right: A) => {
      hEncoder.value.compare(generic.to(left), generic.to(right))
    } 
```

Enter fullscreen mode Exit fullscreen mode

函数的签名告诉我们，如果我们能够提供一种方法，将类型为`A`的对象转换为类型为`H`的中间表示和类型为`H`的比较器，那么它就可以创建类型为`A`的比较器

创建`A`类型的比较器。我们必须编写一个函数，它接受两个类型为`A`的元素，并返回它们的差。因为我们有了代表`A`的比较器，我们可以将两个元素都转换成`H`并使用这个比较器

## `HList`的比较器

构造包括`Tuple`在内的任意`case class`的`Comparator`实例。我们必须定义两个函数

第一个是`HNil` :
的比较器

```
implicit val hNilComparator: Comparator[HNil] = (left: HNil, right: HNil) => Nil 
```

Enter fullscreen mode Exit fullscreen mode

第二个是:

```
 implicit def hlistFieldComparator[K <: Symbol, H, T <: HList](implicit
    witness: Witness.Aux[K],
    hEncoder: Lazy[Comparator[H]],
    tEncoder: Comparator[T]): Comparator[FieldType[K, H] :: T]) = {
    val fieldName: String = witness.value.name

    } 
```

Enter fullscreen mode Exit fullscreen mode

像往常一样，函数的签名告诉我们，如果我们有头部的比较器、尾部和访问头部名称的方式，那么我们可以创建组合`head :: tail`的比较器。
我们可以使用由隐式值`witness`提供的字段名称。
完整的功能是:

```
 implicit def hlistFieldComparator[K <: Symbol, H, T <: HList](implicit
    witness: Witness.Aux[K],
    hEncoder: Lazy[Comparator[H]],
    tEncoder: Comparator[T]): Comparator[FieldType[K, H] :: T] = {
    val fieldName: String = witness.value.name
    (left: FieldType[K, H] :: T, right: FieldType[K, H] :: T) => {
      val headDiffs = hEncoder.value.compare(left.head, right.head)
      val tailDiffs = tEncoder.compare(left.tail, right.tail)
      val headWithPath = headDiffs.map(d => d.copy(paths = fieldName :: d.paths))
      headWithPath ++ tailDiffs
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

逻辑是一样的:我们有头部的比较器，所以我们用它来计算头部的差异。因为我们有了头部字段的名称，所以我们需要在结果前面加上字段名。我们有尾部的比较器，我们用它来计算尾部的差异。最后，我们将两个列表附加在一起，形成最终结果

## `Product`的比较器

同样，如果我们能提供具体 case 类的所有类型类，让我们构造一个 trait 的任何类型类。

模式与`HList`相同，`shapeless`可以构造一个被称为`CoProduct`的`trait`的中间表示。例如

```
sealed trait People

case class Man(name: String) extends People
case class Woman(name: String) extends People 
```

Enter fullscreen mode Exit fullscreen mode

`People`的中间表示是`Man :+: Woman :+: CNil`，与标准的`Either` :
`Either[Man, Either[Woman, CNil]]`非常相似

如果我们承认`shapeless`的力量可以为我们自动创建这个中间类型，那么我们可以定义 2 个函数(或值)

第一个是`CNil`。我们从来不需要评估类型类`CNil`，所以我们可以抛出异常或者返回一个`Nil`

```
 implicit val cNilComparator: Comparator[CNil] = (left: CNil, right: CNil) => Nil 
```

Enter fullscreen mode Exit fullscreen mode

第二个函数结合了`Comparator[Head]`和`Comparator[Tail]`

```
implicit def coProductComparator[H, T <: Coproduct](implicit hComparator: Lazy[Comparator[H]], tComparator: Comparator[T]): Comparator[H :+: T] =
    (left: :+:[H, T], right: :+:[H, T]) => {
      (left, right) match {
        case (Inl(h1), Inl(h2)) => hComparator.value.compare(h1, h2)
        case (Inr(h1), Inr(h2)) => tComparator.compare(h1, h2)
        case (x, y)             => Diff(Nil, TypeDiff(x.toString, y.toString)) :: Nil
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

功能比较简单。它说，如果 2 个元素是左类型，使用头部的比较器。如果这两个元素是正确的类型，使用尾部的比较器。否则，这是一个错误，因为这两个元素不是同一类型

# 例

本例使用了`shapeless`
提供的所有功能

```
sealed trait Tree 
case class Branch(left: Tree, right: Tree) extends Tree 
case class Leaf(value: Option[Double]) extends Tree 
```

Enter fullscreen mode Exit fullscreen mode

为了使用比较器

```
Comparator.compare[Tree](left, right) 
```

Enter fullscreen mode Exit fullscreen mode

本例使用了`shapeless`提供的所有电源

*   `LabelledGeneric`、`HList`创建任何带有字段名的 case 类的类型类
*   `CoProduct`创建任何特征的类型类
*   使用`Lazy`类型类有助于递归隐式搜索

#结论

是这项工作的完美工具，最终产品有助于提高开发人员的绩效。他们可以准确地看到输入中发生了什么变化，并能够快速纠正错误