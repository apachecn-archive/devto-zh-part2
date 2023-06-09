# 论科特林的不变性

> 原文：<https://dev.to/rapasoft/on-immutability-in-kotlin-251k>

Kotlin 直接支持不可变数据结构。它们被称为[数据类](https://kotlinlang.org/docs/reference/data-classes.html)，它们有一些非常好的特性。

首先，数据类的创建非常简单。例如，这个`User`数据类将用一个全参数构造函数创建类，生成`hashCode`、`equals`和`toString`方法:

```
data class User(
        val firstName: String,
        val lastName: String,
        val age: Int
) 
```

Enter fullscreen mode Exit fullscreen mode

这里还有一个额外的好处，通过使用`val`,对原始属性的引用是最终的——因此，不能被改变。创建新实例时，还需要定义所有的构造函数参数，例如`User("John", "Doe", 50)`。这是您自己构建不可变数据结构的良好开端！

对于不可变数据结构，最基本的操作是从现有的实例中创建新的实例，并改变它的(一些)参数。这在 Kotlin 中由`copy()`函数很好地支持。

让我们从简单地复制实例开始:

```
val user1 = User("John", "Doe", 50)
val user2 = user1.copy()

user1 === user2 // referential equality = false
user1 == user2  // structural equality = true 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到它像预期的那样工作——对象是相等的，但是实例是不同的。如果我们想改变一个属性呢？

```
val user1 = User("John", "Doe", 50)
val user2 = user1.copy(age = 20) 
```

Enter fullscreen mode Exit fullscreen mode

这将复制实例并将`age`参数更改为 20。与 Java 相比(在 Java 中，没有第三方库的克隆/复制几乎是不可能的),这真是太棒了！

让我们看一个稍微复杂一点的例子，在这个例子中，我们不仅仅使用原始类型，而是引用一个对象实例:

```
data class System(
        val name: String,
        val user: User
)

val user = User("Jane", "Doe", 50)
val system1 = System("Blog", user)

val system2 = system1.copy()

system1 === system2             // false
system1.user === system2.user   // true 
```

Enter fullscreen mode Exit fullscreen mode

所以你可以看到`copy()`没有*深*，这意味着引用实例的参数只会复制它们的引用。所以基本上，它是一个语法糖，通过它的构造函数创建新的实例，并传递原始实例的参数。

但这没关系，因为 user 是一个不可变的数据类，它的属性不能改变。只有当`User`的属性被定义为`var`时才会有问题。然后你就可以做这样的事情:

```
...
val system2 = system1.copy()
system1.user.age = 13     // not possible if user is defined as `val`

println(system2.user.age) // prints "13" 
```

Enter fullscreen mode Exit fullscreen mode

底线是，如果你坚持使用`val`定义，你应该在不变性方面是安全的！