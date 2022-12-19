# 编码概念-反射

> 原文：<https://dev.to/chris_bertrand/coding-concepts---reflection-4d2c>

# 反思-你需要知道的。

> 在计算机科学中，反射是计算机程序在运行时检查、反省和修改自身结构和行为的能力。[1]

这意味着什么呢？反射是一个不时被提起的术语，但是你真的知道它的意思吗？为什么以及何时应该使用它，它的主要优点是什么？这是一个很难理解的概念，但是非常值得努力学习，因为它可以使某些看似不可能的任务成为可能。

通常我在 JavaScript 中浏览例子，以使大多数人能够理解，然而 JavaScript 中的反射与预编译的面向对象语言(如 Java 和 C#)中的反射并不完全相同，因为它本身并不包含类，所以我们今天将使用这些例子。的。Net Framework 和公共语言运行时(CLR)在 Visual Studio 中的 Intellisense 和其他 IDE 功能以及序列化中大量使用反射，因此您可能会在不知道的情况下使用反射。

让我们来看看 C#中的这个例子。

```
// Using GetType to obtain type information: 
int i = 42;  
System.Type type = i.GetType();  
System.Console.WriteLine(type); 
```

Enter fullscreen mode Exit fullscreen mode

您可能已经猜到控制台中的输出是:

```
System.Int32
```

所以这个静态方法 **GetType** 使用反射来获取变量的类型！显然，在编写代码时，我们知道“I”是一个整数，但运行时手头没有这方面的知识，所以它必须在自身内部进行搜索来找出它！大多数人过去都用过 **GetType** ，但是有些人可能不知道它是如何获得类型的！

让我们看看反射的主要用途，并回答上面提出的一些问题，我会尽量让它容易理解。

## 什么时候使用反射，为什么！

*   传统上用于在运行时从程序集中加载模块/类并创建它们的实例。
*   用于获取对象的公共属性。
*   在测试期间，在运行时初始化期间创建模拟对象。
*   创建通用库来处理不同的格式，而不需要重新部署(有时称为重新部署)或使用隐式后期绑定。
*   在运行时生成新类型时。
*   用于检查和实例化程序集中的类型。
*   能够更改第三方库中标记为私有的字段的值。

> 检查系统中的代码和查看对象类型的能力不是反射，而是类型自省。反射是在运行时利用内省进行修改的能力。这里的区别是必要的，因为有些语言支持内省，但不支持反射。一个这样的例子是 C++

如你所见，这主要是关于类的，查看它们，改变它们，实例化它们，但是都是在运行时而不是编译时！

[![Compile Time Vs Runtime](../Images/e974544d9c7a76a79580beb51f1ffec5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jLHp2cJ7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.designpuddle.com/wp-content/uploads/2018/07/CompileVsRun-1024x414.png)

我能解释这两者区别的最好方法就是看你的跑鞋！

编译时间就像在你出去跑步之前系好鞋带并检查一下。你对鞋子做的任何事情都会被保存下来。这将让您了解它们是否有问题，以及它们包含什么和能做什么。

**运行时间**是指你已经上路的时间！你的鞋子已经为你准备好了，除非你停下来，否则你通常不能更换这些鞋子或重新系鞋带，但是你可以看看这些鞋子有什么功能，并使用这些鞋子现有的任何功能！你可以换一双新鞋，但这需要你已经做好了新鞋，并且可以在跑步时穿。

> 在面向对象的**编程**语言中，**反射**允许在**运行时**检查类、接口、字段和方法，而无需知道编译时接口、字段、方法的名称。它还允许新对象的实例化和方法的调用。

上面我们有一个在对象上使用反射并获取对象属性的例子，现在让我们看看如何使用反射从一个类型中获取信息，以及我们如何在运行时找到并加载新的程序集。

```
 // Using Reflection to get information from an Assembly: 
        System.Reflection.Assembly integerTypeAssembly = typeof(System.Int32).Assembly;  
        System.Type integerType = typeof(System.Int32);

        System.Console.WriteLine(integerTypeAssembly); // mscorlib, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089
        System.Console.WriteLine(integerType.ToString()); // System.Int32

        //Use this typeString to refind the type
        Type calcType = integerTypeAssembly.GetType(integerType.ToString());
        System.Console.WriteLine(calcType); // System.Int32

        // We can then Create a new Instance of whatever this type may be, and call it's methods.
        object integerInstance = Activator.CreateInstance(calcType); 
        // In this case it's just a boring Integer, and we can see it's been initialised to it's default of 0..
        System.Console.WriteLine(integerInstance.ToString()); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到在编译期间查看设置的变量和查询库本身的细微差别，以及我们可以使用这两种类型的反射在运行时对系统进行更改的不同方式。

## 真实世界的例子

使用反射的一个有用的实际例子是基于存储在数据库或其他静态资源中的值在程序集之间切换。例如:您的系统中配置了“GoogleSearchService”和“BingSearchService”。**通常**你会硬编码/注入这些服务中的一个作为要使用的服务。

但是比方说，您有一个“LoadSearchServiceAssemblyFromResource()”方法，它从数据库中检索服务，然后在运行时使用。

如果此服务停止运行、变得过时或您的合同将被终止，您不必使用“BingSearchService”重新部署您的系统，只需更新指向您的另一个程序集(“BingSearchServiceAssembly”)的数据库/存储字段。

下次调用您的搜索服务时，它会在运行时动态切换到您的新服务。对我来说，这是反射的主要好处，也是为什么可以或者应该使用它。因为每个服务都是一个 dll，所以你可以使用反射使用这样的格式直接调用它们。

```
//This will load the dll from a static resource such as a DB or File
Assembly searchAssembly = LoadSearchServiceAssemblyFromResource();
// Now we need to find the class in the assembly, this in this instance is simply called "Search"
Type searchType = searchAssembly.GetType(searchAssembly.GetName().Name + "Search");

// If we have found the class
if (searchType != null)
{
    object searchResult = null;
    dynamic classInstance = Activator.CreateInstance(searchType);
    // DoSearch is a method that is declared on both SearchService Classes!
    var searchResult = instance.DoSearch();
} 
```

Enter fullscreen mode Exit fullscreen mode

您可能不总是有使用它的场景，但是知道何时以及为什么应该使用它是无价的。

这里还没有涉及到更多关于反射的内容。所以如果你想了解更多关于这个话题的内容，可以看看附加阅读材料。

* * *

## 附加阅读

[微软概念文档-反思](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/reflection)

[类型自省和反思](https://thesocietea.org/2016/02/programming-concepts-type-introspection-and-reflection/)

[微软-反射和 CodeDom](https://docs.microsoft.com/en-us/dotnet/framework/reflection-and-codedom/reflection)

[微软反射-动态加载和使用类型](https://docs.microsoft.com/en-us/dotnet/framework/reflection-and-codedom/dynamically-loading-and-using-types)

[StackOverflow -什么是反射，为什么有用](https://stackoverflow.com/questions/37628/what-is-reflection-and-why-is-it-useful)