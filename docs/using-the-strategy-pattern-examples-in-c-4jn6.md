# 使用策略模式(C#中的示例)

> 原文：<https://dev.to/sam_ferree/using-the-strategy-pattern-examples-in-c-4jn6>

# 先决条件

为了从这篇文章中得到最大的收获，如果你对面向对象编程和继承有一个基本的了解，以及像 C#或 Java 这样的面向对象编程语言，这将会很有帮助。尽管我希望即使你不是 C#语法专家，你也能理解策略模式背后的主要思想。

# 例题

我们正在开发一个保持文件同步的应用程序，最初的要求是，如果一个文件存在于目标目录中，但不存在于源目录中，那么这个文件应该被删除。我们可能会这样写

```
 public void Sync(Directory source, Directory destination)
{
    CopyFiles(source, destination)
    CleanupFiles(source, destination)
}

private void CleanupFiles(Directory source, Directory destination)
{
    foreach(var destinationSubDirectory in destination.SubDirectories)
    {
        var sourceSubDirectory = source.GetEquivalent(destinationSubDirectory);
        if(sourceSubDirectory != null)
        {
            CleanupFiles(sourceSubDirectory, destinationSubDirectory);
        }
        else
        {
            // The source sub directory doesn't exist
            // So we delete the destination sub directory
            destinationSubDirectory.Delete();
        }
    }

    // Delete top level files in this directory
    foreach(var file in destination.Files)
    {
        if(source.Contains(file) == false)
        {
            file.Delete();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

嘿，他们看起来不错！我们使用了递归，它非常易读。但是，就像所有的软件项目一样，需求会发生变化。我们发现有时候，我们不想删除多余的文件。这是一个非常快速的修复。我们可以通过检查一个标志来实现。

```
public void Sync(Directory source, Directory destination)
{
    CopyFiles(source, destination)
    if(_shouldDeleteExtraFiles)
    {
        CleanupFiles(source, destination)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因为我们将删除逻辑分离到它自己的方法中，所以一个简单的 If 语句就可以完成这项工作。直到需求再次发生变化。现在，我们希望应用程序给用户保留文件的选项。usf 扩展。这要求我们对 CleanupFiles 方法进行更改。

```
private void CleanupFiles(Directory source, Directory destination)
{
    foreach(var DestinationSubDirectory in destination.SubDirectories)
    {
        var sourceSubDirectory = source.GetEquivalent(DestinationSubDirectory);
        if(sourceSubDirectory != null)
        {
            CleanupFiles(sourceSubDirectory, DestinationSubDirectory);
        }
        else
        {
            // The source sub directory doesn't exist
            // So we delete the destination sub directory
            destinationSubDirectory.Delete();
        }
    }

    // Delete top level files in this directory
    foreach(var file in destination.Files)
    {
        if(_keepUSF && file.HasExtension("usf"))
        {
            continue;
        }

        if(source.Contains(file) == false)
        { 
            file.Delete();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

嗯，现在我们已经添加了几个全局标志，我们有了依赖于它们分布在多个方法中的逻辑。(在这篇文章的范围之外，我们可能还需要用。usf 扩展。)我们真的可以从清理这些代码中受益。我们应该如何将我们的逻辑分离出来，以便关于使用哪个删除逻辑的决策和逻辑本身不那么纠缠在一起？

# 进入策略模式。

来自 w3sDesign.com:

> 策略模式(也称为策略模式)是一种行为软件设计模式，支持在运行时选择算法。代码不是直接实现单个算法，而是接收运行时指令，以决定使用哪一组算法。

现在我知道我一直在谈论清理代码的需要，而不是在运行时选择算法。我们已经有能力在运行时改变算法，因为我们使用了标志，但我们将看到清理的好处。

首先，让我们编写一个接口。

```
public interface IDeleteStrategy
{
    void DeleteExtraFiles(Directory source, Directory Destination);
} 
```

Enter fullscreen mode Exit fullscreen mode

为了简化，假设客户端将把它传递给我们的方法，那么我们更新它的签名和内容如下:

```
public void Sync(
    Directory source,
    Directory destination,
    IDeleteStrategy deleteStrategy) //Expect to recieve a delete strategy
{
    CopyFiles(source, destination)

    //Call our delete strategy and let it handle the clean up
    deleteStrategy.DeleteExtraFiles(source, destination)
} 
```

Enter fullscreen mode Exit fullscreen mode

好吧，那肯定会把事情弄清楚的。现在让我们看一些实现。首先是琐碎的选项，不要删除任何东西。如果这个策略被传入，方法调用将不做任何事情，目标目录中的任何额外文件都将保留。

```
public class NoDelete : IDeleteStrategy
{
    public void DeleteExtraFiles(Directory source, Directory Destination)
    {
        //Do nothing!
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以编写两个不同的策略来删除不在源文件中的文件，并保留 USF 文件，但是保留 USF 文件的策略是删除不在源文件中的文件的策略的扩展。所以我们可以用继承来保存一些代码。

如果文件不在源目录中，我们的策略是删除目标目录中的文件。注意，我们保留了之前的递归来清理子目录，但是我们打破了删除顶层文件的逻辑，并决定我们是否应该删除一个文件。

```
public class DeleteIfNotInSource : IDeleteStrategy
{
    public void DeleteExtraFiles(Directory source, Directory Destination)
    {
        foreach(var destinationSubDirectory in destination.SubDirectories)
        {
            var sourceSubDirectory = source.GetEquivalent(destinationSubDirectory);
            if(sourceSubDirectory != null)
            {
                // use recursion to pick up sub directories
                DeleteExtraFiles(sourceSubDirectory, destinationSubDirectory);
            }
            else
            {
                // The source sub directory doesn't exist
                // So we delete the destination sub directory
                destinationSubDirectory.Delete();
            }
        }

        DeleteTopLevelFiles(source, destination);
    }

    private void DeleteTopLevelFiles(Directory source, Directory destination)
    {
        foreach(var file in destination.Files)
        {
            if(ShouldDelete(file, source))
            { 
                file.Delete();
            }
        }
    }

    protected bool ShouldDelete(File file, Directory source)
    {
        return source.Contains(file) == false;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我们实际上只需要实现我们的策略，通过从 DeleteIfNotInSource 继承并覆盖 ShouldDelete 方法来保存 usf 文件！

```
public class KeepUSF : DeleteIfNotInSource
{
    public override bool ShouldDelete(File file, Directory source)
    {
        if(file.HasExtension("usf"))
        {
            return false;
        }

        //Defer to our base class
        return base.ShouldDelete(file, source);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以现在，如果我们需要添加一个新的策略，我们不需要修改任何其他的代码。我们可以创建一个新的类来实现 IDeleteStrategy 接口，并添加选择它的逻辑。

例如，我们可以使用像配置设置这样的东西来选择一个策略，然后将它传递给我们的 sync 方法。下面是一个可能的例子(使用工厂模式，如果你想进一步了解的话)

```
// The application configuration tells us what delete strategy we are using
var deleteStrategyFactory = DeleteStrategyFactory.CreateFactory(configSettings);
...

// We don't know exactly what strategy we're getting, 
// better yet we don't care!
var deleteStrategy = deleteStrategyFactory.getDeleteStrategy();
SyncProcess.Sync(source, destination, deleteStrategy); 
```

Enter fullscreen mode Exit fullscreen mode

# 猛踩刹车。

一些熟悉设计模式的人可能会说“嘿，Sam，你为什么不用装饰模式来实现保持 USF 的功能呢！?"(如果你愿意，可以进一步阅读。)

有时候，最好不要仅仅因为可以就应用设计模式。事实上，出于教育目的，这个例子很简单。有人可能会说，这里的策略模式是矫枉过正。

一定要权衡应用设计模式的收益和成本。在这里应用策略模式为我们的项目添加了一个接口和三个新类。换句话说:“确保果汁值得压榨。”