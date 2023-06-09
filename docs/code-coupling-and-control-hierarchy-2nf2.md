# 代码耦合和控制层级

> 原文：<https://dev.to/tzhechev/code-coupling-and-control-hierarchy-2nf2>

几年前，我承担了一项清理大量 Delphi 代码库的工作，目的是使之有可能只将部分代码编译成可执行文件。代码非常旧，没有遵循好的实践——因此，如果不创建一个 20mb 的 exe 来尝试启动数据库，您就无法编译它的最小部分。

导致这个问题的那种模式是我从那以后经常看到的，并试图建议不要这样做。这篇文章是我试图创建一个简短的，有点简洁的解释，为什么我个人认为某些事情应该以某种方式完成。

## 松耦合

松散代码耦合的常见思想倾向于表达为对严格的基于契约的需求，而不是对象之间基于类的交互。我倾向于认为建议的良好实践是，确保将依赖项定义为接口，而不是您将使用的实际类，并将这些接口限制为您实际使用的 API 的一部分。

```
public class Furnace implements BreadBaker {
    ...
    @Override
    public void breadIsBaked(Bread bread) {
        ...
    }
    public void addBread() {
        Bread newBread = new Bread(this);
        contents.add(newBread);
    }
    public void startBaking() {
        for (Bakeable bakeable : contents) {
            bakeable.startBaking();
        }
    }
}

public class Bread implements Bakeable {
    private BreadBaker baker;
    public Bread(BreadBaker baker) {
        this.baker = baker;
    }
    @Override
    public void startBaking() {
        ...
        baker.breadIsBaked(this);
    }
...
} 
```

虽然我认为这通常是一个好主意，但我认为不太经常提到的重要性是我将要(可能错误地)提到的控制层次。此外，我认为为每一个类依赖创建极其特殊的接口并不是一个天生的好策略。识别实际上可以提高代码重用的实例非常重要。

## 控制级别

让我们考虑一下，如果一个对象持有对另一个对象的引用，那么它就知道后者。(一般来说，仅通过有限的接口引用对象可能会模糊这种知识，但我不认为这是一个足够的障碍。)

我认为维护实际代码可重用性的唯一方法是确保如果我们将代码库视为这种对象引用的图形，那么它将是一个没有循环的有向图。

能够仅提取代码库的一部分的唯一方式是该部分不依赖于代码库的其余部分。我提到了如何通过契约(接口或其他任何东西)隔离这种依赖性，但根据我的经验，这往往不能正确地防止将引用的对象用作一种 oracle 或全局变量。一般来说，如果引用是必要的，因为它不能通过简单地将简单的数据传递给引用类来解决，这很可能违反了 Demeter 的[法则。](https://en.wikipedia.org/wiki/Law_of_Demeter)

遵循这一规则，代码执行可以被概念化为从图的“中心”的单个点开始。该节点有零条通向它的路径和所有离开它的路径。然后执行级联，覆盖图逻辑门指引它前进的任何路径。

这导致了代码结构中的分层，其中执行只能从更多“知识渊博”的对象“向下”流向更少的对象。这对于提高代码的可测试性有着巨大的好处——很容易从任何节点开始执行，而不涉及它上面的任何层。它在代码重用方面也非常有益——知识较少的对象可以被提取出来，并在不同的控制对象下安全地执行。另一个好处是，这大大有助于防止[副作用](https://en.wikipedia.org/wiki/Side_effect_(computer_science))错误，因为类不应该能够修改超出其范围的任何东西。

```
public class Furnace {
    ...

    public void breadIsBaked(Bread bread) {
        ...
    }
    public void addBread() {
        Bread newBread = new Bread();
        contents.add(newBread);
    }
    public void startBaking() {
        for (Bakeable bakeable : contents) {
            bakeable.startBaking();
            if (bakeable instanceof Bread) {
                breadIsBaked((Bread)bakeable);
            }
        }
    }
}

public class Bread implements Bakeable {

    public Bread() {

    }
    @Override
    public void startBaking() {
        ...
    }
...
} 
```

## 事件呢？

据我所知，到目前为止所有的问题是，如果你只考虑一个初始执行点，它工作得很好。但事实是，在任何响应性应用程序中，都有许多其他的执行点——事件。并且事件确实需要从末端节点向上流回(因为这是它们产生的地方),并在更高层执行代码。

在这方面，值得一问的是，为什么不简单地提供对更高层对象的引用，以便在发生事件时可以对其进行操作？您可以定义它的 API，以便处理程序逻辑保留在较高级别的对象中，并且只是作为带有事件数据的函数被较低级别的对象调用。

但是高级对象可以定义低级对象在事件发生时应该如何响应——这在 Java 中是通过将匿名类设置为监听器来实现的，在 JS 中是通过在“愚蠢”对象上设置由“聪明”对象定义的函数来实现的。

```
public class Furnace implements BreadBaker {
    ...
    @Override
    public void breadIsBaked(Bread bread) {
        ...
    }
    public void addBread() {
        Bread newBread = new Bread();
        newBread.setBreadBakedHandler(new BreadBakedHandler {
            @Override
            public void breadIsBaked(Bread bread) {
                breadIsBaked(bread);
            }
        }
        contents.add(newBread);
    }
    public void startBaking() {
        for (Bakeable bakeable : contents) {
            bakeable.startBaking();
        }
    }
}

public class Bread implements Bakeable {

    public Bread() {

    }
    @Override
    public void startBaking() {
        ...
        if (breadBakedHandler != null) {
            breadBakedHandler.breadIsBaked(this);
        }
    }
    public setBreadBakedHandler(BreadBakedHandler handler) {
        this.breadBakedHandler = handler;
    }
...
} 
```

从绝对意义上来说，这仍然可以被认为是创建了一个引用循环，因为这些函数经常会引用比执行它们的函数更高级别的对象，但至少它们是在它们适当的层次中定义的。在定义侦听器时，自然倾向于只公开处理事件所需的最小量。另一方面，当将引用分配给较高级别的对象时，自然倾向于向较低级别的对象公开更多不必要的内容——将 API 约束到绝对最小值是定义紧密接口的特定任务，可能会被忽略。

还有其他处理事件扩散的方法，比如使用事件总线，但至少对我来说，这似乎是一个更糟糕的解决方案，原因有二。一个是它倾向于充当全局对象，必须在对象图中传递(隐式或显式)。另一个原因是它会使调用堆栈更难调试，模糊了从事件执行到事件处理的路径。

## 其他解决方案？

我不确定。我很想听到更好的模式，所以请随意评论！