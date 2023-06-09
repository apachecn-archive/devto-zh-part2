# 如何利用命令模式实现更好的解耦

> 原文：<https://dev.to/kylegalbraith/how-to-leverage-the-command-pattern-for-better-decoupling-24i5>

当谈到编程模式时，命令模式可能会让你有点摸不着头脑。但是一旦你理解了其中的组件和实现它们的简单性，它就能真正改变你的编码。

总而言之，命令模式是一种编程模式，它允许我们将请求封装为对象。这种将请求煮沸成共享一个接口的对象的想法为我们提供了许多不同的好处。我认为最有价值的两点是:

*   一个请求处理器可以处理所有不同类型的请求。
*   每种类型的请求都可以单独处理。

这些都是我们在这篇文章中重点讨论的好处。让我们为命令模式何时值得您花时间来实现建立一个基线。

### 知道何时使用命令模式

因为命令模式非常适合处理一组请求，所以查看处理多种类型请求的代码是非常关键的。例如，假设我们有两种请求类型`ExtractPdf`和`ExtractWord`。

```
public class ExtractPdf
{
    private readonly string _documentPath;
    private readonly string _documentName;
    private readonly string _pdfVersion;

    public ExtractPdf(string documentPath, string documentName, string pdfVersion)
    {
        _documentPath = documentPath;
        _documentName = documentName;
        _pdfVersion = pdfVersion;
    }

    public string GetPdfContent()
    {
        // logic to extract the given pdf file.
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的`ExtractPdf`请求类有一个带三个参数的构造函数和一个方法`GetPdfContent()`，该方法有提取 PDF 文件内容的逻辑。

看一看`ExtractWord`请求类，我们开始发现一些相似之处。

```
public class ExtractWord
{
    private readonly string _documentPath;
    private readonly string _documentName;

    public ExtractWord(string documentPath, string documentName)
    {
        _documentPath = documentPath;
        _documentName = documentName;
    }

    public string GetWordContent()
    {
        // logic to extract the given word file.
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的`ExtractWord`类有一个构造函数，它有两个参数和自己的提取方法`GetWordContent()`。这里我们有一个两个请求类共享的公共接口，它们都从一个文件类型中提取内容。

让我们看看负责处理这些请求的`RequestProcessor`类。

```
public class RequestProcessor
{
    public RequestProcessor()
    {    
    }

    public ProcessPdfRequests(ExtractPdf[] pdfRequests)
    {
        foreach(req in pdfRequests)
        {
            var content = req.GetPdfContent();
            // do something with the content...
        }
    }

    public ProcessWordRequests(ExtractWord[] wordRequests)
    {
        foreach(req in wordRequests)
        {
            var content = req.GetWordContent();
            // do something with the content...
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

查看我们的`RequestProcessor`类，我们看到每种类型的请求都有一个处理函数。该函数接收一组请求，并对它们进行循环以提取内容。

这是命令模式派上用场的主要例子。

为什么？想象一下，如果您添加了另一种类型的请求，比如`ExtractImage`，那么需要做哪些改变来支持它呢？

1.  我们需要一个新的请求类`ExtractImage`来实现提取逻辑。
2.  我们需要更新请求处理器，使其具有新的功能来处理这些类型的请求。

第二点是这里的关键点，因为这意味着如果不触及处理逻辑，就不能添加额外的请求类型。把这当成你的嗅觉测试。

如果添加或修改现有请求需要更改消息处理的逻辑，我可能会用更通用的方式封装我的请求。

### 让我们利用命令模式

我们可以通过首先引入一个请求共享的公共接口来将命令模式引入到这个示例代码中。

我们来定义一下`IRequestExtraction`接口。

```
public interface IRequestExtraction
{
    string Extract();
} 
```

Enter fullscreen mode Exit fullscreen mode

有了为请求接口定义的接口，我们可以重构我们的`ExtractPdf`和`ExtractWord`请求类来实现它。

```
public class ExtractPdf : IRequestExtraction
{
    private readonly string _documentPath;
    private readonly string _documentName;
    private readonly string _pdfVersion;

    public ExtractPdf(string documentPath, string documentName, string pdfVersion)
    {
        _documentPath = documentPath;
        _documentName = documentName;
        _pdfVersion = pdfVersion;
    }

    public string Extract()
    {
        // logic to extract the given pdf file.
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
public class ExtractWord : IRequestExtraction
{
    private readonly string _documentPath;
    private readonly string _documentName;

    public ExtractWord(string documentPath, string documentName)
    {
        _documentPath = documentPath;
        _documentName = documentName;
    }

    public string Extract()
    {
        // logic to extract the given word file.
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这两种类型的请求的实现中，我们实际上只改变了两件事。我们更改类来实现`IRequestExtraction`接口，并将获取内容的方法更改为接口中定义的`Extract()`方法。

现在我们可以重构我们的`RequestProcessor`来利用这个公共接口。

```
public class RequestProcessor
{
    public RequestProcessor()
    {    
    }

    public ProcessRequests(IRequestExtraction[] requests)
    {
        foreach(req in requests)
        {
            var content = req.Extract();
            // do something with the content...
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们所做的是移除我们的请求处理器支持的*类型提取*的任何概念。取而代之的是，它现在有一个方法`ProcessRequests`，它接受一个`IRequestExtraction`对象的数组并执行它们的`Extract`方法。

我们不再需要处理器知道它正在处理什么类型的请求。这允许它接受满足请求接口的任何对象，并在其上执行命令。

这允许我们添加、删除和改变请求/命令的实现，而不必修改处理器。只要提取方法返回内容，就不需要进行任何更改。

在命令模式中，我们将这个请求处理器称为我们的**调用者**，因为它负责告诉命令执行。这个执行的结果可以被传递给一个**接收器**，它将对这个内容做一些事情。

### 包装完毕

命令模式对于我们希望对请求进行排队或批处理的系统非常有用。它还有一个额外的好处，就是将我们的请求处理从创建请求的业务逻辑中分离出来。这种模式假设您有不止一种类型的请求需要处理，因此如果不是这样，不必要的开销可能不值得您花费时间。

当我们引入这种由接口表示的常见请求队列时，我们可以灵活地添加和删除消息类型。这些请求保持隔离，以便每个请求都可以按照自己认为合适的方式处理提取。但是我们让处理器变得更加健壮，因为我们不必关心提取需要如何完成。

### 饿着肚子学亚马逊 Web 服务？

有很多人渴望学习亚马逊网络服务。受到这个事实的启发，我创建了一个课程，专注于通过使用它来学习 Amazon Web Services。关注静态网站的托管、保护和交付问题。通过构建问题的解决方案，您可以学习 S3、API Gateway、CloudFront、Lambda 和 WAF 等服务。

AWS 周围有大量的信息。很容易迷失方向，在学习上没有任何进步。通过解决这个问题，我们可以简化信息，加快你的学习。我用这本书和视频课程的目的是与你分享我所学到的。

听起来有趣吗？查看登录页面以了解更多信息，并选择一个适合您的软件包，[通过实际使用它来学习 AWS 基础知识](https://www.kylegalbraith.com/learn-aws/)。