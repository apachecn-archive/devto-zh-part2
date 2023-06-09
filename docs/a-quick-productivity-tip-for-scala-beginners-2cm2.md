# Scala 初学者的快速生产力技巧

> 原文：<https://dev.to/mrmurphy/a-quick-productivity-tip-for-scala-beginners-2cm2>

当学习一门新的语言时，我可能想学的第一件事就是如何快速运行一个独立的代码块，这样我就可以轻松地进行小实验，并确保我理解语言语义或其库。然而，要弄清楚如何做到这一点并不总是那么简单。

如果我们使用 Node.js / Javascript，我们可以简单地编写一个小脚本并执行它。大概如下。

我们编写一个名为`foo.js`的小文件，看看我们是否理解了 Javascript 中字符串和数字是如何交互的:

```
function foo() {
    var addingNumbersToStrings = 1 + "2"
    console.log(addingNumbersToStrings)
}

foo() 
```

Enter fullscreen mode Exit fullscreen mode

我们这样运行它:

```
node foo.js 
```

Enter fullscreen mode Exit fullscreen mode

它将运行，并打印出“12”，现在我们确信我们理解了 JS 是如何工作的😉。

但是现在，假设我们的工作描述发生了一些变化，我们被转移到了一个编写 Scala 的团队。我们如何像上面的例子一样快速测试我们的假设？

我们可能会接触到`sbt`中的 Scala REPL，这在某种程度上是可行的，但是迭代起来很麻烦，并且如果我们以后想引用它们的话，对于保留那些小测试来说不是很好。

但是，如果我们使用 IntelliJ 和 Scala 插件作为我们的编辑器(我认为大多数 Scala 开发人员都是这样)，这里有一个方便的小技巧。在任何一个`.scala`文件中，我们可以添加一个扩展`App`的对象，这个对象将变成可执行的:

```
object UnderstandAddition extends App {
    println(1 + 2)
} 
```

Enter fullscreen mode Exit fullscreen mode

IntelliJ 甚至会在该对象旁边的空白处放一个绿色的小三角形，告诉我们可以只运行这一部分，而不必运行我们正在处理的整个大型服务器。

如果我们看不到那个绿色的小三角形，我们总是可以右键单击该对象的名称，并选择从上下文菜单中运行它。

在这种情况下，我们将看到控制台打印出数字“3”。

但是，让我们稍微升级一下，把这些实验变成特别的测试:

```
object TestAddition extends App {
    assert(1 + 2 == 3, "Oops, math broke")
} 
```

Enter fullscreen mode Exit fullscreen mode

哇！我们刚刚在主代码库中编写了一个小测试，而无需配置测试运行程序、加载断言库或创建新文件！任何时候我们运行这个类，它都会告诉我们，我们的假设是对还是错。

显然，对于一个真正的测试套件，我们会想去配置一个测试运行程序，等等...并把一些东西挂在`sbt test`上，这样我们就可以放心，我们代码库中的所有测试都已经运行并通过了。这个小技巧只是在这里帮助我们测试我们的逻辑，并迭代我们的方式成为精通生态系统。