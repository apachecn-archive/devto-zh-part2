# 你最喜欢/最有用的扩展方法？

> 原文：<https://dev.to/kritner/your-favoritemost-useful-extension-methods-51il>

扩展方法非常棒！它们是向代码添加功能的一种方式，而不需要实际接触原始源代码！

[微软文档](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/extension-methods)将扩展方法定义为:

> 扩展方法使您能够向现有类型“添加”方法，而无需创建新的派生类型、重新编译或修改原始类型。扩展方法是一种特殊的静态方法，但是调用它们时就好像它们是扩展类型上的实例方法一样。对于用 C#、F#和 Visual Basic 编写的客户端代码，调用扩展方法和在类型中实际定义的方法之间没有明显的区别。

以上听起来很不错，但似乎没有触及这样一个事实，即您可以在没有源代码的情况下对代码应用扩展方法！

扩展方法可能非常有用，但是正如文档所说，要谨慎使用。如果对你正在创建的底层对象的实现扩展方法进行修改，那可能会导致一些问题。

我喜欢使用扩展方法的一个例子:

```
public void DoStuff()
{
 List<IUser> users = new List<IUser>();

 var employee = GetEmployee(_somePredicate);
 if (employee != null)
 {
  users.Add(employee);
 }

 var manager = GetManager(_someOtherPredicate);
 if (manager != null)
 {
  users.Add(manager);
 }
} 
```

在上面的例子中，我们有相当多的代码嵌套，这是因为在对一个列表进行插入之前要检查是否有空值。扩展方法有助于使代码看起来更整洁！

扩展方法`AddIfNotNull`:

```
public static void AddIfNotNull<T>(this IList<T> obj, T item)
{
 if (obj == null)
 {
  throw new ArgumentNullException($"{nameof(obj)} is null");
 }

 if (item == null)
 {
  return;
 }

 obj.Add(item);
} 
```

上面给`IList<T>`和任何实现它的东西添加了一个新函数。在该函数中，我们检查要添加到`IList<T>`中的项目是否是`null`；如果是，我们什么都不做，如果不是，我们添加它。太好了，这对我们有什么帮助？

好吧，让我们看看前面的代码片段是什么样子的，使用扩展方法:

```
public void DoStuff()
{
 List<IUser> users = new List<IUser>();

 users.AddIfNotNull(GetEmployee(_somePredicate));
 users.AddIfNotNull(GetManager(_someOtherPredicate));
} 
```

这段代码要短得多，而且(我认为)更容易阅读！

我在一个知识库中添加了一些(底部附近的链接)。当前的扩展方法包括:

*   `IList.AddIfNotNull` —已经描述过
*   `IList.AddRangeIfNotNull` —与上面类似，只是在多个对象级别上
*   `IEnumerable<T>.TryFirst` —尝试从列表中获取一个项目，使用 bool 作为返回，实际对象在 out 参数中—类似于`int.TryParse`的工作方式。

你最喜欢的有用的扩展方法有哪些？欢迎在下面回复，或者提交一份 PR！

代码(截至编写时)可以在以下位置找到:

[krit ner-Blogs/extension methods](https://github.com/Kritner-Blogs/ExtensionMethods/releases/tag/v1.0.1)

或者作为一个 NuGet 包:

克里特纳。扩展方法 1.0.1