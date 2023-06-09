# C# 7 中 F#联合类型的模式匹配

> 原文：<https://dev.to/stuartblang/pattern-matching-f-union-types-in-c-7-5cnm>

从 C# 7.0 开始，有了基本的模式匹配支持。我想看看如何使用它来与 F#有区别的联合进行互操作，并看看从 C#中使用 F#代码最好是什么样子。

下面是一个 F#区分并集(以下简称 DU)
的例子

```
type Record = { Name: string; Age: int }

type Abc =
    | A
    | B of double
    | C of Record 
```

对于那些不熟悉 DUs 的人来说，上面定义的`Abc`类型可以是`A`、`B`或`C`(没有其他的)，在`A`的情况下没有“有效载荷”，而`B`和`C`有一个有效载荷。

因此，假设这编译成一个密封的类，并且用例变成类，我们可以使用 C#中的新类型模式来匹配:

```
void HandleAbc(Abc abc)
{
    switch (abc)
    {
        case Abc.B _:
            Console.WriteLine("B");
            break;
        case Abc.C c:
            Console.WriteLine(c.Item.Name);
            break;
    }
} 
```

这里有一些需要注意的地方:

### 1。真的很好

我认为这里的代码是干净的，我们在类型上进行匹配，可以使用`_`忽略转换的结果，也可以将它作为一个命名变量(就像本例中的`c`)。

### 2。我们得不到 F 中的安全感

在 F#中，当处理数据时，编译器确保我们已经处理了所有的情况，在 C#中，这些安全检查是不强制的。

### 3。案例`A`呢！

是啊，这就是不完美的地方。F#编译器确实为 case `A`生成了一个类型，但是它被标记为内部类型，因此我们无法对其进行匹配，但是，有几个选项，我会让您选择您最喜欢的:

```
void HandleAbc(Abc abc)
{
    switch (abc)
    {
        // Option 1:
        case var x when x.IsA:
            Console.WriteLine("A");
            break;
        // Option 2:
        case var x when x == Abc.A:
            Console.WriteLine("A");
            break;
    }

    switch (abc.Tag)
    {
        // Option 3:
        case abc.Tags.A:
            Console.WriteLine("A");
            break;
    }
} 
```

让我在评论中知道你的偏好是什么，或者有更好的选择吗？

感谢阅读！