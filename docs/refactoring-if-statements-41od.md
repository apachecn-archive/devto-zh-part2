# 重构 if 语句

> 原文：<https://dev.to/funkysi1701/refactoring-if-statements-41od>

我正在做的代码库包含一个巨大的 if 块。我说的巨大是指 77 个 if 语句一个接一个，每个 if 语句检查你在哪个页面 id 上并加载不同的内容。这不容易维护，我想重构它。

一种选择是用 switch 块替换 if 语句。然而，这就像巨大的 if 块一样难以管理。让我们看看更好的选择。

多态是你创建一个基类，然后从它创建子类。在我的例子中，我用一个方法 CreateContent 创建了一个接口 IPage。

```
public interface IPage { string CreatePageContent(); } 
```

然后为实现这个方法的每个页面创建 77 个类。

现在有趣的部分来了，我如何从我的原始代码中调用正确的页面类？

我创建了一个字典，将页面 id 映射到类名。

```
public static Dictionary<PageIds, Type> PageIdToClass = new Dictionary<PageIds, Type>() { { PageIds.HomePage, typeof(HomePage) }, { PageIds.ContactPage, typeof(ContactPage) }, //etc } 
```

这是我不是 100%满意的一个步骤，因为我认为有可能删除或简化这个步骤。

现在我有了一个将 id 映射到类的方法，我可以写一个类来完成这个任务。

```
public class MyPage { IPage \_repo; public MyPage(int pageId) { PageIds p = (PageIds)pageId; Type t = PageIdToClass[p]; ConstructorInfo constructor = t.GetConstructor(new Type[] { }); \_repo = (IPage)constructor.Invoke(null); } public string Create() { return \_repo.CreatePageContent(); } } 
```

因此，在我的构造函数中，我获取 pageId 并将其传递给我的字典，以获取要加载的子类。然后我获取它的构造函数并调用它。

现在，我可以删除巨大的 if 块，用一行代码替换它。

```
var page = new MyPage(pageId); 
```

从表面上看，这种变化可能看起来像是没有太大收益的大量工作，因为我们从一个文件开始，现在我们有了原始文件、一个接口、77 个子类和 MyPage 类。然而，原始文件更易于管理，每个子类都可以独立地进行修改。

这是使代码更易维护的一大步，总是有更多的事情可以做，但可以等待另一天。