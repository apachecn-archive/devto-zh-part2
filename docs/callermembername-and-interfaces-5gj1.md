# CallerMemberName 和接口

> 原文：<https://dev.to/anandmukundan/callermembername-and-interfaces-5gj1>

[CallerMemberName]是 C# 5.0 中提供的一个属性。这是一个非常有用的功能，尤其是在处理日志、审计等常见的应用框架组件时。

在通用日志记录基础设施中，您通常会尝试做的一件事情是记录生成该日志的函数。

所以在 C# 5.0 之前，最常见的是询问堆栈来找到调用者，这可能是一个昂贵的操作。或者从源函数传递调用者信息，在调用中硬编码其名称。

大约

```
_logger.Debug("Message", "myFunctionName"); 
```

这变得很难维护，因此在大多数情况下，记录器实际上询问堆栈来获得这些信息，正如前面提到的，这带来了性能损失。

使用这个新属性，您可以为日志函数编写如下内容:

```
public void Debug(string Message, [CallerMemberName] string callerFunction = "NoCaller") 
```

这里的第二个参数不是由调用者传递的，而是由基础结构填充的(即 NET 框架)。参数必须是可选参数，您才能使用此属性。这不会有性能损失，因为编译器会在编译时填充这个参数，所以没有运行时开销。

这很好，除了当你使用接口的时候。同样，接口通常是一种模式，常用于日志之类的框架组件中。因此，您将有一个 ILogger 接口，它有一个类似的定义，由您的实际 Logger 类实现，调用者使用该接口与您的实现进行对话。

如果您这样做，问题是接口也需要定义属性，否则值不会自动填充。这是一件很容易被忽略的事情，而且在我能找到的资料中也没有很好的记录。因此，该函数的接口定义将具有如下定义的属性:

```
void Debug(string Message, [CallerMemberName] string callerFunction = "") 
```

以便调用者的值传递到您的实现中。

当你设计你的界面时，这很容易被忽略，并且要花很长时间才能找出原因。