# 设计 json 迁移工具

> 原文：<https://dev.to/namnguyen/designing-a-json-migration-tool-40j3>

# 出现了问题

Json 可能是当今最流行的数据格式。它既用于数据交换(例如前端和后端之间)。它也用来存储像`MongoDB`这样的数据库内部，甚至 [`postgresql`](https://www.postgresql.org/docs/9.3/static/functions-json.html) 都支持 json 数据类型。

使用`json`意味着定义 2 个功能:

*   将对象转换成`json`格式的编写器
*   读取一个`json`并产生一个对象的读取器

这个用例的一个例子是:

```
case class Model(x: String, y: String) 
```

Enter fullscreen mode Exit fullscreen mode

在 [`play-json`](https://github.com/playframework/play-json) 中，我们可以定义一个`Format`并使用宏助手自动定义一个`Reader`和一个`Model`
的`Writer`

```
implicit val modelFormat: Format[Model] = Json.format[Model] 
```

Enter fullscreen mode Exit fullscreen mode

这是一个示例值:

```
{  “x”:  “toto”,  “y”:  “tata”  } 
```

Enter fullscreen mode Exit fullscreen mode

一切都运行良好，现在 json 数据保存在数据库和测试文件夹中。

有一天客户想在模型中增加一个字段，新的模型应该是:

```
case class Model(x: String, y: String, z: Int) 
```

Enter fullscreen mode Exit fullscreen mode

我们不需要修改`Format`，因为宏会自动处理它。

但是旧的 json 格式呢？当前格式无法读取旧的 json 数据，因为它缺少`z`

现在我们应该找到一个解决方案，通过将`z`设置为默认值来读取旧的 json 格式

# 先解

我们找到了解决这个问题的快捷方法。例如，我们可以定义一个可以读取当前和旧版本的`Reader`。

我们首先要做的是将`Format`拆分成`Reader`和`Writer` :

```
implicit val modelReads: Reads[Model] = (
  (JsPath \ "x").read[String] and
  (JsPath \ "y").read[String]
)(Model.apply _)

implicit val modelWrites: Writes[Model] = (
  (JsPath \ "x").write[Double] and
  (JsPath \ "y").write[Double]
)(unlift(Model.unapply)) 
```

Enter fullscreen mode Exit fullscreen mode

然后我们定义两个版本的`Reader` :

```
val modelReadsV0: Reads[Model] = (
  (JsPath \ "x").read[String] and
  (JsPath \ "y").read[String] and
  Reads.pure(0)
)(Model.apply _)

implicit val modelReadsV1: Reads[Model] = (
  (JsPath \ "x").read[String] and
  (JsPath \ "y").read[String] and
  (JsPath \ "z").read[Int]
)(Model.apply _) orElse modelReadsV0 
```

Enter fullscreen mode Exit fullscreen mode

系统是这样工作的:隐式读者总是在最后一个版本中。如果`ReaderV1`不能读取`json`，则使用`ReaderV0`来定义字段`z`的默认值

但是这种设计的问题是:

*   如果我们添加或删除一个字段，我们需要更新所有的阅读器
*   不要在更复杂的情况下工作:移动一个字段到另一个地方，重命名一个字段，改变一个字段的值
*   将`Format`拆分为`Reader`和`Writer`会导致读取器/写入器不匹配。我们需要增加很多测试

# 秒解

## 祈使句 vs 功能句

解决方案是克隆 SQL 迁移设计:每个迁移都由一个脚本定义。

问题是我们如何定义这个迁移脚本。`JsValue`是不可变的，从旧的创造新的价值是乏味的。我是函数式编程的粉丝，但这是命令式编程更容易的地方。例如

| 命令方式 | 功能方式 |
| --- | --- |
| `x.y.z += 1` | `x.copy(y = x.y.copy(z = x.y.z + 1))` |
| `value[“key25”] = {“key251”: value[“key2”][“key21”]}` | `(__ \ 'key25 \ 'key251).json.copyFrom( (__ \ 'key2 \ 'key21).json.pick )` |

我为团队中的功能性和非功能性程序员编写这个库。编写迁移脚本应该很容易，而函数式方法很难完成这项任务。此外，对于高级任务，这几乎是不可能的。例如，假设我们想要修改所有具有字段`toto`的`JsObject`，并将字段值更改为 0。这个 json 值的一个例子是:

```
{  “X”:  {  “toto”:  0  },  “Y”:  [{“toto”:  1},  {“tata”:  2}  ],  “Z”  :  {  “zz”:  {“toto”:  2}}  } 
```

Enter fullscreen mode Exit fullscreen mode

我们想要修改的值可以在一个字段中，嵌套在两级字段中，甚至在一个数组中。以函数的方式更新这些值是很难的，我发现的唯一方法是使用先进的概念，如[拉链](https://en.wikipedia.org/wiki/Zipper_(data_structure))

所有的困难导致了显而易见的解决方案:将临时不可变值转换成可变版本，应用更改并转换回原始值

## 可变版本的包装器

让我们为这个新的数据结构定义一个`trait`:

```
sealed trait JsValueWrapper
case class JsObjectWrapper(value: collection.mutable.Map[String, JsValueWrapper]) extends JsValueWrapper
case class JsStringWrapper(value: String) extends JsValueWrapper
case class JsArrayWrapper(value: ArrayBuffer[JsValueWrapper]) extends JsValueWrapper
case class JsBooleanWrapper(value: Boolean) extends JsValueWrapper
case class JsNumberWrapper(value: BigDecimal) extends JsValueWrapper
case object JsNUllWrapper extends JsValueWrapper 
```

Enter fullscreen mode Exit fullscreen mode

这就建立了一个`JsValue`所有可能值的等价物。唯一的区别是`JsObjectWrapper`和`JsArrayWrapper`在内部使用可变集合

## 转换功能

让我们定义两个在`JsValue`和`JsValueWrapper`之间转换的方法。当然在处理`JsObject`和`JsArray`
的时候有一些递归

```
implicit def create(input: JsValue): JsValueWrapper =
   input match {
     case x: JsObject    => JsObjectWrapper(collection.mutable.Map(x.value.mapValues(create).toSeq: _*))
     case x: JsArray     => JsArrayWrapper(ArrayBuffer(x.value: _*).map(create))
     case x: JsString    => JsStringWrapper(x.value)
     case x: JsBoolean   => JsBooleanWrapper(x.value)
     case x: JsNumber    => JsNumberWrapper(x.value)
     case JsNull         => JsNUllWrapper
   }

implicit def toJson(input: JsValueWrapper): JsValue = {
input match {
    case x: JsObjectWrapper    => JsObject(x.value.map { case (name, value) => (name, toJson(value)) }.toSeq)
    case x: JsArrayWrapper     => JsArray(x.value.map(toJson))
    case x: JsStringWrapper    => JsString(x.value)
    case x: JsBooleanWrapper   => JsBoolean(x.value)
    case x: JsNumberWrapper    => JsNumber(x.value)
    case JsNUllWrapper         => JsNull
}
} 
```

Enter fullscreen mode Exit fullscreen mode

## 脚本

让我们定义一个`trait`，它定义一个方法`migrate`，这个方法进行适当的修改

```
trait JsonMigrator {
 def migrate(input: JsValueWrapper): Unit
 def transform(input: JsValueWrapper): JsValueWrapper = {
   migrate(input)
   input
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们应该定义一种方法来组合多个脚本以形成一个全局脚本。这个操作叫做`append`，如果我们可以定义一个中立的脚本(一个什么都不做的脚本)，那么我们可以定义一个`Monoid`实例

```
implicit val monoid: Monoid[JsonMigrator] = new Monoid[JsonMigrator] {
   def zero: JsonMigrator = (_: JsValueWrapper) => ()
   def append(f1: JsonMigrator, f2: => JsonMigrator): JsonMigrator = {
     (input: JsValueWrapper) => {
       f1.migrate(input)
       f2.migrate(input)
     }
   }
 } 
```

Enter fullscreen mode Exit fullscreen mode

## 一些帮手

我们应该创建一些隐式转换来帮助我们的用户编写更简洁的迁移代码。但是这不是一个安全的操作，因为`asInstanceOf`会抛出异常

```
implicit class JsObjectWrapperConverter(input: JsValueWrapper) {
   def apply(field: String): JsValueWrapper = input.asInstanceOf[JsObjectWrapper].value(field)
   def number: BigDecimal = input.asInstanceOf[JsNumberWrapper].value
   def map: mutable.Map[String, JsValueWrapper] = input.asInstanceOf[JsObjectWrapper].value
   def setDefault(field: String, value: JsValueWrapper): Unit = {
     if (!has(field))
       input.asInstanceOf[JsObjectWrapper].map.update(field, value)
   }
   def remove(field: String): Option[JsValueWrapper] = {
     input.asInstanceOf[JsObjectWrapper].map.remove(field)
   } 
```

Enter fullscreen mode Exit fullscreen mode

## 例子

假设我们有 3 个迁移:

```
private val migrator1 = new JsonMigrator() {
    def migrate(x: JsValueWrapper): Unit = {
      x("field1").map.remove("field11")
      ()
    }
  }
private val migrator2 = new JsonMigrator { // add new field field1/field12
def migrate(input: JsValueWrapper): Unit =
  input("field1").map.update("field12", "myNewField")

private val migrator3 = new JsonMigrator { //  Change all sFields to "hahaha"
def migrate(input: JsValueWrapper): Unit =
  PathResolver.migrate(input, List(RecurFieldCond(HasField("sField")))) { w =>
    w.map.update("sField","hahaha")
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以创建一个全局迁移器，因为我们已经为它定义了
的`Monoid`

```
import scalaz.syntax.foldable._
import scalaz.std.list._
val globalMigrator = List(migrator1, migrator2, migrator3).suml 
```

Enter fullscreen mode Exit fullscreen mode

现在有了全局脚本，我们将不可变的 json 值转换成可变的版本，转换它并转换回不可变的版本

```
val x = JsValueWrapper.create(json) // first we need to create a mutable version of the original json
allMigrator.migrate(x) // then mutate it by applying the global migration
val result = JsValueWrapper.toJson(x) 
```

Enter fullscreen mode Exit fullscreen mode

## 集成到部署系统中

这只是一个例子，说明我们如何自动迁移包含大量 json 的数据库:数据库将当前版本存储在某个地方。当执行迁移时，proram 获取当前版本并找到所有迁移脚本，将它们组合成一个全局脚本，迭代数据库中的所有 json 值，转换成可变版本，用全局脚本转换，最后转换回不可变版本

用户需要维护一个定义版本和脚本映射的文件:

```
val l = List(
  (0, migration1),
  (1, migration2),
  (2, migration3)
) 
```

Enter fullscreen mode Exit fullscreen mode

如果两个用户同时修改文件，他们必须在合并期间解决冲突

# 结论

这个库很成功，因为我的团队每天都在使用它。有一些非常复杂的脚本可能需要 100 多行代码。一个很大的优势是我们可以使用`Format`类型的类来代替`Reader/Writer`,这样读取器和写入器之间就没有不匹配的问题了