# 使用密封类以获得更好的域表示

> 原文：<https://dev.to/le0nidas/use-sealed-classes-for-better-domain-representation-4286>

让我们从业务逻辑开始。

> 我们有任务。
> 一个任务可以分配给一个用户**或**一个组**或**给任何人。

#### 第一:丑陋的方式

实现这一点的一种方法是将所有内容放入任务:

```
class UglyTask(
        val name: String,
        val assignedUser: User? = null,
        val assignedGroup: Group? = null
) {

    init {
        if (assignedUser != null && assignedGroup != null) {
            throw IllegalArgumentException("a task can be assigned to either a user OR a group.")
        }
    }
} 
```

这种实现不仅仅是丑陋的(所有这些空值和初始化检查),它还隐藏了业务逻辑。开发人员必须阅读该类的代码，以理解如何创建分配的任务。

此外，弄清楚任务是否被分配以及被分配到哪里的简单事情变得乏味，并且很容易导致错误。例如，编写一个函数来打印分配给任务的地方，必须进行空检查，并且不能利用 kotlin 的所有功能:

```
fun UglyTask.printAssignment() {
    when {
        assignedGroup == null && assignedUser == null -> println("\"$name\" is assigned to no one")
        assignedGroup != null -> println("\"$name\" is assigned to to group: ${assignedGroup.name}")
        assignedUser != null -> println("\"$name\" is assigned to to user: ${assignedUser.name}")
    }
} 
```

最后，如果你希望你的代码在所有情况下都是可读的，你必须更加努力一点:

```
 val le0nidas = User("le0nidas")
    val kotlinEnthusiasts = Group("kotlin enthusiasts")

    UglyTask("buy milk").printAssignment()
    UglyTask("write post", assignedUser = le0nidas).printAssignment()
    UglyTask("write kotlin", assignedGroup = kotlinEnthusiasts).printAssignment() 
```

这里我们按名称传递参数，以省略为组创建任务时的空用法:

```
UglyTask("write kotlin", null, kotlinEnthusiasts).printAssignment() 
```

#### 第二:不那么丑陋的方式

另一种方法是拥有多个构造函数，每个构造函数对应一个有效的赋值:

```
class LessUglyTask private constructor(
        val name: String,
        val assignedUser: User?,
        val assignedGroup: Group?
) {

    constructor(name: String) : this(name, null, null) // assigned to no one
    constructor(name: String, assignedUser: User) : this(name, assignedUser, null) // assigned to a user
    constructor(name: String, assignedGroup: Group) : this(name, null, assignedGroup) // assigned to a group
} 
```

这个实现稍微好一点，因为它帮助开发人员更快地理解任务分配背后的逻辑

[![less-ugly-task-creation.png](img/c6296977b73d6089994d81dad890a05a.png)T2】](https://postimg.cc/Hc7TC5gw)

但是它也有一些缺点，首先是当编写类似上面的函数时..您实际上以所有必要的空检查结束:

```
fun LessUglyTask.printAssignment() {
    when {
        assignedGroup == null && assignedUser == null -> println("\"$name\" is assigned to no one")
        assignedGroup != null -> println("\"$name\" is assigned to to group: ${assignedGroup.name}")
        assignedUser != null -> println("\"$name\" is assigned to to user: ${assignedUser.name}")
    }
} 
```

此外，它不容易扩展。如果我们想添加另一种赋值方式，我们需要改变主构造函数**和**为它添加一个新的第二个构造函数(第一个实现也有可伸缩性问题，但我不会去那里，因为..无效支票成倍增加！).

就可读性而言，它仍然需要一点科特林的爱，但它不会导致任何像上面这样的东西

```
 val le0nidas = User("le0nidas")
    val kotlinEnthusiasts = Group("kotlin enthusiasts")

    LessUglyTask("buy milk").printAssignment()
    LessUglyTask("write post", assignedUser = le0nidas).printAssignment()
    LessUglyTask("write kotlin", assignedGroup = kotlinEnthusiasts).printAssignment() 
```

#### 最后；封类道:)

实现业务逻辑的最佳方式是使用 kotlin 的密封类。这样，我们可以直接在代码中表示我们的业务逻辑，同时保持代码的整洁、可读性和可伸缩性:

```
sealed class AssignedTo
object AssignedToNoOne : AssignedTo()
data class AssignedToUser(val user: User) : AssignedTo()
data class AssignedToGroup(val group: Group) : AssignedTo()

class Task(val name: String, val assignedTo: AssignedTo = AssignedToNoOne) 
```

至于打印功能，你自己看看它有多容易被读取，以及它如何利用 kotlin 的所有功能(为胜利而聪明地施展):

```
fun Task.printAssignment() {
    when (assignedTo) {
        is AssignedToNoOne -> println("\"$name\" is assigned to no one")
        is AssignedToGroup -> println("\"$name\" is assigned to ${assignedTo.group.name}")
        is AssignedToUser -> println("\"$name\" is assigned to ${assignedTo.user.name}")
    }
} 
```

且不说我们的代码在创建任务时有多好看:

```
 val le0nidas = User("le0nidas")
    val kotlinEnthusiasts = Group("kotlin enthusiasts")

    Task("buy milk", AssignedToNoOne).printAssignment()
    Task("write post", AssignedToUser(le0nidas)).printAssignment()
    Task("write kotlin", AssignedToGroup(kotlinEnthusiasts)).printAssignment() 
```

如果我们想添加一种新的赋值方式，我们只需将*assign 扩展为*类！