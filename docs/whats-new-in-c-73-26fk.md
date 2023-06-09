# C# 7.3 有什么新特性？

> 原文：<https://dev.to/borrrden/whats-new-in-c-73-26fk>

Visual Studio 15.7 预览版 3 已经提供了对一些 C# 7.3 功能的初始支持。让我们看看他们是什么！

### `System.Enum``System.Delegate``unmanaged`约束。

现在，使用泛型函数，您可以对传入的类型进行更多的控制。更具体地说，您可以指定它们必须是`enum`类型、`delegate`类型或“blittable”类型。最后一个有点复杂，但它意味着一个类型只包含某些预定义的原始类型(如`int`或`UIntPtr`)，或者这些类型的数组。“Blittable”意味着它能够通过托管-非托管边界按原样发送到本机代码，因为它没有对托管堆的引用。这意味着你有能力做这样的事情:

```
void Hash<T>(T value) where T : unmanaged
{
    fixed (T* p = &value) { 
        // Do stuff...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我对这一点特别兴奋，因为我必须使用许多变通方法才能使助手方法与“指针类型”一起工作。

### 参考本地重新分配

这只是一个小小的改进，允许你像对待普通变量一样将`ref`类型变量/参数分配给其他变量。我认为下面的代码就是一个例子(马上想到)

```
void DoStuff(ref int parameter)
{
    // Now otherRef is also a reference, modifications will 
    // propagate back
    var otherRef = ref parameter;

    // This is just its value, modifying it has no effect on 
    // the original
    var otherVal = parameter;
} 
```

Enter fullscreen mode Exit fullscreen mode

### Stackalloc 初始化器

这增加了初始化堆栈分配数组的能力(你知道这是 C#中的一个东西吗？我做了:D)为你将一堆分配一:

```
Span<int> x = stackalloc[] { 1, 2, 3 };. 
```

Enter fullscreen mode Exit fullscreen mode

### 分度可移动固定缓冲器

我真的无法理解这个问题，所以看看你[是否能理解它](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-7.3/indexing-movable-fixed-fields.md)

### 自定义固定报表

这是我第一次看到这个，对我来说很刺激！基本上，如果你实现了一个隐式接口(一个方法)，你可以在一个`fixed`语句中使用你自己的类型来传递 P/Invoke。我不确定确切的方法是什么(`DangerousGetPinnableReference()`或`GetPinnableReference()`)，因为提案和发布说明不一致，但是如果这个方法返回一个合适的类型，那么你可以消除一些样板文件。

### 强化过载候选者

有一些新的方法解析规则来优化将方法解析为正确方法的方式。见[提案](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-7.3/improved-overload-candidates.md)中的列表。

### 表达式变量在初始化器中

这里的总结是“像 out var 和 pattern 变量这样的表达式变量在字段初始值设定项、构造函数初始值设定项和 LINQ 查询中是允许的。”但是我不确定这能让我们做什么...

### 元组比较

元组现在可以和`==`和`!=`比了！

### 支持字段上的属性

您是否曾经想要将一个属性(例如`NonSerializable`)放在一个属性的支持字段上，然后意识到您必须手动创建一个属性和支持字段才能做到这一点？

```
[Serializable]
public class Foo {
    [NonSerialized]
    private string MySecret_backingField;

    public string MySecret {
        get { return MySecret_backingField; }
        set { MySecret_backingField = value; }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

不再是了！

```
[Serializable]
public class Foo {
    [field: NonSerialized]
    public string MySecret { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode