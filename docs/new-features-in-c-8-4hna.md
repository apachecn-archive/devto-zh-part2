# C# 8 中的新特性

> 原文：<https://dev.to/mteheran/new-features-in-c-8-4hna>

在 2017 年令人惊叹的 7.x 版本之后，新的 C#正在构建中，并将包括新的有用功能。

但是，如果你不知道 C# 7.x 中的新特性，一定要看下面的帖子；您无法想象如何使用这些新特性来改进您的代码:

[https://mte heran . WordPress . com/2017/12/01/features-c-7-0-7-1-7-2/](https://mteheran.wordpress.com/2017/12/01/features-c-7-0-7-1-7-2/)
T3】https://blogs . msdn . Microsoft . com/dot net/2017/03/09/new-features-in-c-7-0/

C#的具体路线图还没有公布，但现在我们可以查看官方资源库中的新特性:[https://github.com/dotnet/csharplang/issues](https://github.com/dotnet/csharplang/issues)

可为空的引用类型:

C# 6 中引入了一个很棒的特性。空条件运算符有助于在访问内部值之前检查对象或变量是否为空。例如:

```
int? length = mynumbers?.Length; 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，我们不能对字符串类型使用这个特性。在这种情况下，特性在于允许开发者将字符串类型声明为可空:

```
String? strVar = null;
var length = strVar?.Length; 
```

Enter fullscreen mode Exit fullscreen mode

异步流:

基本上，异步流是 IEnumerable 的异步等价物。此功能允许开发人员在 foreach 中使用 async 关键字，如下所示:

foreach await(async stream 中的字符串 s)
为了使用该代码，您必须将集合定义为 IAsyncEnumerable:

async IAsyncEnumerable method name()
默认接口实现:

让我们开始检查下面的代码:

```
 Interface ICal 
{
    Void TurnOn();
}
Calculator : ICal
{
   void TurnOn()
   {
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用接口一切都更容易，正如我们所知，我们可以使用多种实现。但是如果我们想在接口中添加一个新方法，所有实现该接口的类都需要创建一个同名的本地方法并返回相同的值。

现在在 C# 8 中，我们可以像这样直接在接口中实现一个方法:

```
Interface ICar 
{
    Void TurnOn();
}
Car : ICar
{
   void TurnOn()
   {
   }
}
Interface ICal 
{
    Void TurnOn();
    double SumNumbers( double number1, double number2 ) 
    {
       return number1+ number2 
    }
}
Calculator : ICal
{
   void TurnOn()
   {
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

扩展所有内容:

该建议在 github 中被报告为一个问题，您可以在以下链接中看到摘要:

[https://github.com/dotnet/roslyn/issues/11159](https://github.com/dotnet/roslyn/issues/11159)

比如一类人的分机:

```
 extension MyPersonExtension extends Person 
{ 
   public int NumberOfFingers() 
   { 
      get { … } 
   } 
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以在以下链接中找到更多信息:

[https://www.infoq.com/news/2017/08/CSharp-8](https://www.infoq.com/news/2017/08/CSharp-8)

https://channel 9 . msdn . com/Blogs/Seth-Juarez/A-Preview-of-C-8-with-Mads-Torgersen # time = 32m 11s