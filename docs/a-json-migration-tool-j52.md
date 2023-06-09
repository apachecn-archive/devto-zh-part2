# 一个 json 迁移工具

> 原文：<https://dev.to/namnguyen/a-json-migration-tool-j52>

# JSON-迁移

这个[项目](http://hibou107.github.io/developing-a-json-migration-tool.html)试图以与数据库迁移工具相同的方式帮助构建一个 json 迁移框架。

本例中使用的`json`库是`play-json`

这个项目的目的是让没有多少函数式编程经验的人也能轻松编写迁移脚本。即使是有经验的程序员也能坚持使用[海岸到海岸的设计](https://www.playframework.com/documentation/2.6.x/ScalaJsonTransformers)

这是有代价的，因为它不是类型安全的:如果用户想要更新一个字符串字段，但它实际上是一个对象
，那么就会抛出一个`Exception`。记住，异常可以在迁移脚本中的任何地方抛出。

在实际项目中，用户应该在应用任何迁移之前备份他们的数据库

目前，我没有让它可以从`sbt`导入。如果您想集成到您的项目中，只需复制
文件并添加依赖项。

```
 libraryDependencies += "org.scalaz" %% "scalaz-core" % "7.2.17"
libraryDependencies += "com.typesafe.play" %% "play-json" % "2.6.7"
libraryDependencies += "org.scalatest" %% "scalatest" % "3.0.4" % "test" 
```

Enter fullscreen mode Exit fullscreen mode

# 如何使用

假设我们想要一个 json 值:

```
 {"field1"  :  {  "field11":  100  },  "field2":  [  {  "sField":  "Do it"  },  {  "sField":  "good",  "s1":  "fine"  }  ],  "field3":  {  "sField":  "nice"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

我们希望对这个 json 值应用一系列转换:

1.  移除`field1` / `field11`
2.  添加值为`myNewField`的字段`field1` /【字段 12】
3.  对于每个字段`sField`，用`hahaha`替换所有值。字段`sField`在多个地方:在一个数组`field2`里面和在`field3`里面

我们想看到的结果是:

```
{  "field1"  :  {  "field12":  "myNewField"  },  "field2":  [  {  "sField":  "hahaha"  },  {  "sField":  "hahaha",  "s1":  "fine"  }  ],  "field3":  {  "sField":  "hahaha"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 创建迁移者列表

这个库提供了一种很好的方式来简单地描述:

扩展特征`JsValueWrapperImplicits`,在 Json 和可变包装器之间自动转换

创建一个新的定义函数`migrate`的`JsonMigrator`。这个函数接受一个`JsValueWrapper`，它是一个可变值

### 第一次迁移

```
private val migrator1 = new JsonMigrator() {
    def migrate(x: JsValueWrapper): Unit = {
      x("field1").map.remove("field11")
      ()
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

*   `x("field1")`表示我知道 x 是一个 JsObject，我访问了字段`field1`
*   `y.map.remove("field11")`意味着我知道 y 是一个 JsObject，我删除了字段`field11`

### 第二次迁移

```
 // add new field field1/field12
private val migrator2 = new JsonMigrator {
def migrate(input: JsValueWrapper): Unit =
  input("field1").map.update("field12", "myNewField")
} 
```

Enter fullscreen mode Exit fullscreen mode

### 第三次迁移

```
//  Change all sFields to "hahaha"
private val migrator3 = new JsonMigrator {
def migrate(input: JsValueWrapper): Unit =
  PathResolver.migrate(input, List(RecurFieldCond(HasField("sField")))) { w =>
    w.map.update("sField", JsStringWrapper("hahaha"))
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这种迁移有点复杂。在函数`migrate`中，我们使用了一个助手`PathResolver.migrate`，它将第一个输入作为`JsValueWrapper`和一个列表`FieldCond`。`RecurFieldCond(HasField("sField"))`的意思是找到所有的
即`JsObject`且有字段`sField`的`Jsvalue`。第二个参数是一个使
值发生变化的函数。请注意，`w`对象是找到的对象，而不是字段`sField`的值

一旦我们有了迁移器的列表，我们就可以将它们组合成一个全局 json 迁移器

```
import scalaz.syntax.foldable._
import scalaz.std.list._
val globalMigrator = List(migrator1, migrator2, migrator3).suml 
```

Enter fullscreen mode Exit fullscreen mode

注意，为了使用`suml`实用程序，您需要导入`scalaz`语法

## 迁移

现在当全局`migrator`被创建时，唯一要做的就是迁移原来的 json:

```
val x = JsValueWrapper.create(json) // first we need to create a mutable version of the original json
allMigrator.migrate(x) // then mutate it by applying the global migration
JsValueWrapper.toJson(x) shouldBe jsonResult // the result must be identical to the desired result 
```

Enter fullscreen mode Exit fullscreen mode

## 将其与 JsonTransformer 进行比较

我将[教程([[https://www . play framework . com/documentation/2.6 . x/ScalaJsonTransformers](https://www.playframework.com/documentation/2.6.x/ScalaJsonTransformers))中的`JsonTransformer`与这个可变版本进行对比

原点 json 是

```
val gizmo = Json.obj(
  "name" -> "gizmo",
  "description" -> Json.obj(
    "features" -> Json.arr( "hairy", "cute", "gentle"),
    "size" -> 10,
    "sex" -> "undefined",
    "life_expectancy" -> "very old",
    "danger" -> Json.obj(
      "wet" -> "multiplies",
      "feed after midnight" -> "becomes gremlin"
    )
  ),
  "loves" -> "all"
) 
```

Enter fullscreen mode Exit fullscreen mode

转换后的 json 是

```
val gremlin = Json.obj(
  "name" -> "gremlin",
  "description" -> Json.obj(
    "features" -> Json.arr("skinny", "ugly", "evil"),
    "size" -> 30,
    "sex" -> "undefined",
    "life_expectancy" -> "very old",
    "danger" -> "always"
  ),
  "hates" -> "all"
) 
```

Enter fullscreen mode Exit fullscreen mode

纯变压器解决方案:

```
import play.api.libs.json._
import play.api.libs.json.Reads._
import play.api.libs.functional.syntax._

val gizmo2gremlin = (
  (__ \ 'name).json.put(JsString("gremlin")) and
  (__ \ 'description).json.pickBranch(
    (__ \ 'size).json.update( of[JsNumber].map{ case JsNumber(size) => JsNumber(size * 3) } ) and
    (__ \ 'features).json.put( Json.arr("skinny", "ugly", "evil") ) and
    (__ \ 'danger).json.put(JsString("always"))
    reduce
  ) and
  (__ \ 'hates).json.copyFrom( (__ \ 'loves).json.pick )
) reduce 
```

Enter fullscreen mode Exit fullscreen mode

哇，好吓人！能给一个数据科学家开发者解释一下吗？

这个库的解决方案是:

```
val migrator = new JsonMigrator {
  def migrate(input: JsValueWrapper): Unit =
    input.map.update("name", "gremlin")
    val description = input.map("description").map
    description.update("features", JsArrayWrapper("skinny", "ugly", "evil"))
    val currentSize = description("size").number
    description.update("size", currentSize * 3) 
    description.update("danger", "always")
    input.map.remove("danger")
    input.map.update("hates", "all")
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我觉得这种方法比纯粹的功能性风格更容易。

Play 网站上的教程不包含任何递归转换。如果我错了，请纠正我，但是我认为不使用[拉链数据结构](https://en.wikipedia.org/wiki/Zipper)是不可能做到

# 更进一步

这段代码摘自一个真实的项目。如果您想将迁移集成到您的工作流中，您必须做更多的事情:

创建转换列表:

```
val l = List(
  (0, migration1),
  (1, migration2),
  (2, migration3)
) 
```

Enter fullscreen mode Exit fullscreen mode

如果您的 json 位于数据库中，那么您必须将版本存储在某个地方。要构建一个全局转换器，你必须知道起始版本并创建从起始版本到最新版本的全局转换。

好了

如果你觉得它有用，请告诉我