# 带 EF 内核的 F#选项

> 原文：<https://dev.to/danieljsummers/f-options-with-ef-core-417a>

**更新:**这篇文章中描述的代码现在可以作为 [NuGet 包](https://www.nuget.org/packages/FSharp.EFCore.OptionConverter/)获得。

[实体框架核心](https://docs.microsoft.com/en-us/ef/core/)的 2.1 版本带来了做[值转换](https://docs.microsoft.com/en-us/ef/core/modeling/value-conversions)的能力。这是通过抽象类`ValueConverter`实现的，您可以实现该抽象类来转换数据类型。他们还提供了[几个内置的转换器](https://docs.microsoft.com/en-us/ef/core/modeling/value-conversions#built-in-converters)，你不用写，比如把`enum`存储成字符串。要使用值转换器，您需要提供它的一个新实例，并将其附加到模型的`OnModelCreating`事件中的一个属性上。

F#提供了一个`Option<'T>`类型来表示一个可能存在也可能不存在的值。将可选值定义为`'T option`比检查 null 有很多好处；如果你愿意，你可以[阅读全部内容](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/options)。

当我在做一个项目时，我已经使用了`Option.ofObj`将可能为空的结果从查询转换为选项；不过，在字段级别，我使用的是默认值。我可以使用这个新特性来处理`null`表列吗？事实证明，是的！

下面是值转换器的代码。

```
module Conversion =

  open Microsoft.FSharp.Linq.RuntimeHelpers
  open System
  open System.Linq.Expressions

  let toOption<'T> =
    <@ Func<'T, 'T option>(fun (x : 'T) -> match box x with null -> None | _ -> Some x) @>
    |> LeafExpressionConverter.QuotationToExpression
    |> unbox<Expression<Func<'T, 'T option>>>

  let fromOption<'T> =
    <@ Func<'T option, 'T>(fun (x : 'T option) -> match x with Some y -> y | None -> Unchecked.defaultof<'T>) @>
    |> LeafExpressionConverter.QuotationToExpression
    |> unbox<Expression<Func<'T option, 'T>>>

type OptionConverter<'T> () =
  inherit ValueConverter<'T option, 'T> (Conversion.fromOption, Conversion.toOption) 
```

Enter fullscreen mode Exit fullscreen mode

`Conversion`模块包含了我们需要在`ValueConverter`构造函数中提供的函数。*(按照类继承在 F#中编码的方式，以及`ValueConverter`希望其表达式在构造函数中的方式，这是一个必要的步骤。我希望看到一个无参数的构造函数和可重写的属性作为一个选项，但我没有抱怨；这是一个非常棒的功能。)*在这些函数中，我们使用[代码引用](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/code-quotations)，然后将引用表达式转换成 Linq 表达式。

另一个注意事项:在`toOption`函数中，如果我们使用`Option.ofObj`而不是`box x`，代码将不支持值类型。这意味着不支持像`int option`字段这样的东西。

现在我们有了我们的选项转换器，让我们把它连接到我们的模型中。在我的项目中，每个实体类型都有一个静态的`configureEF`函数，我从`OnModelCreating`调用那些。下面是我的一个实体类型的简略版本:

```
[<CLIMutable>]
[<NoEquality>]
[<NoComparison>]
type Member =
  { /// ...
    /// E-mail format
    format : string option
    /// ...
    }
  with
    /// ...
    static member configureEF (mb : ModelBuilder) =
    /// ... HasColumnName statements, etc.
    mb.Model.FindEntityType(typeof<Member>).FindProperty("format").SetValueConverter(OptionConverter<string> ())
    |> ignore 
```

Enter fullscreen mode Exit fullscreen mode

这一行代码查找模型中的类型、类型中的属性，并向它提供我们的选项转换器的新实例。在该实体中，此处的`None`表示该成员使用该组的默认电子邮件格式；`Some`表示他们已经指定了他们喜欢的格式。

这就是全部了！定义一次转换器，并将其插入所有可选字段；现在，EF Core 将可空字段转换为选项。[《神奇独角兽》](https://twitter.com/efmagicunicorns)果然！

*(鸣谢:非常感谢 jiří·钦古拉[精彩的价值转换博文](https://www.tabsoverspaces.com/233708-using-value-converter-for-custom-encryption-of-field-on-entity-framework-core-2-1)和托马斯·皮特里切克[关于将报价表达式转换为 Linq 表达式的栈溢出回答](https://stackoverflow.com/a/23146624)。)*