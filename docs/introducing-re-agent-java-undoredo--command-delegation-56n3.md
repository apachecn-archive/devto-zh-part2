# 介绍重新代理:Java 撤销/重做&命令委托

> 原文：<https://dev.to/jsjlewis96/introducing-re-agent-java-undoredo--command-delegation-56n3>

> 首先，这是我的第一个图书馆，也是有点尴尬的，我的第一篇博文😱。任何反馈都将不胜感激！一切都得从某个地方开始，对吗？

在拖延我的学位时，我意识到我的最终项目包含一些有用的命令委托代码，可以构成一个不错的小库。事不宜迟，让我来介绍一下...

# 重新代理

关于自述文件(也在下面)和代码，请查看:

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [ logdyn ](https://github.com/logdyn) / [再代理](https://github.com/logdyn/re-agent)

### 一个 Java 命令委托库，提供撤销/重做功能

<article class="markdown-body entry-content container-lg" itemprop="text">

# Logdyn 重新代理 [![Build Status](img/6872bf2ea26415f4bf5e59698b895e99.png)](https://travis-ci.org/logdyn/re-agent)

一个 Java 命令委托&撤销-重做库。

## 特征

*   退回和重做
*   松散耦合`Command Pattern`
*   听众支持

## 装置

当前的发布版本可以在 maven 中央存储库中找到。要使用它，请将以下依赖项添加到您的`pom.xml`:

```
<dependency&gt
  <groupId>com.logdyn</groupId&gt
  <artifactId>re-agent</artifactId&gt
  <version>1.2</version>
</dependency>
```

Enter fullscreen mode Exit fullscreen mode

该版本也可以在 [GitHub 版本](https://github.com/logdyn/re-agent/releases)上找到。

## 如何使用

### 命令

`Command`接口的实现用于触发订阅的`Executor`中的事件。`Command`不指定执行行为，而是用于触发事件和捕获该事件所需的任何信息。

```
class ExampleCommand implements UndoableCommand {

    @Override
    public String getName() {
        return "Example Undoable Command";
    }
}
```

Enter fullscreen mode Exit fullscreen mode

还有`UndoableCommand`的，可以…

</article>

[View on GitHub](https://github.com/logdyn/re-agent)

# 背景

这个库最初是作为另一个项目的撤销/重做控制器，并由我和我的编码员马特改编，作为我们在 GitHub 上合作的一部分。

该产品必须做三件事:

*   简化指挥委派
*   轻松撤销/重做
*   尽可能松散耦合

#### 那么我说的指挥授权是什么意思呢？

假设你有一个程序打开一个文件，并为你提供两个不同的编辑器窗口。很明显，每个编辑器窗口都有自己的控制器，但是文件操作可以在它们之间共享。在我们的例子中，编辑并不特别关心文件是如何打开的，只是不要忘记...会做得很好。

当你想打开一个文件时，A `FileMenuController`会知道，但是我们希望我们的编辑也知道，但是我们不一定希望控制器知道编辑。这就是我们的命令委派者的用武之地。

编辑将订阅命令委托者，说他们想要了解某些事件，在本例中是一个`FileOpenCommand`。当文件控制器打开一个文件时，它将向委托者发布一个`FileOpenCommand`,其中包含打开该文件所需的任何信息。命令委派者将把这个命令转发给编辑，他们将担心命令的执行。

哒哒！成功委派😄但是我们还没有完成...

#### 退回&准备

任何以前实现过撤销/重做系统的人都知道，可能最棘手的方面是回复状态。为了能够撤销一个动作，你需要以 RESTful 方式运行的方法(不管状态如何都表现相同)*或者*你需要存储你想要返回的状态。

作为宽宏大量的开发人员，我们决定我们不在乎，你应该能够做你想做的。耶，自由！在这种情况下，撤销/重做过程使用四个实体:委托者、发布者、命令者和执行者。命令可能包含状态，也可能不包含状态，但是它们总是与它们的执行器紧密耦合

*   委托者:处理执行者的订阅，并将对`DO`、`UNDO`和`REDO`的调用传递给命令的执行者
*   发布者:向委托者发送命令的任何东西
*   Command:基本上是一个数据包，可能有供执行者使用的实用方法
*   执行者:控制命令如何“完成”,可以选择实现撤销和重做

#### 那么我该怎么安装呢？

把它作为对 Maven 的依赖来添加就行了！(永远辉煌的[詹科夫教程](http://tutorials.jenkov.com/maven/maven-tutorial.html)你已经涵盖)

```
<dependency>
  <groupId>com.logdyn</groupId>
  <artifactId>re-agent</artifactId>
  <version>1.2</version>
</dependency> 
```

Enter fullscreen mode Exit fullscreen mode

#### 那么我该如何使用呢？

我很懒，所以这是直接来自自述文件，但我渴望得到改进它的建议👍

##### 命令

`Command`接口的实现用于触发订阅的`Executor`中的事件。`Command`不指定执行行为，而是用于触发事件和捕获该事件所需的任何信息。

```
class ExampleCommand implements UndoableCommand {

    @Override
    public String getName() {
        return "Example Undoable Command";
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

还有`UndoableCommand`，它可以被任何`Executor`执行，尽管如果你想使用撤销/重做，必须使用一个`UndoableExecutor`。默认情况下，`reexecute()`方法调用`execute()`，尽管这可以被覆盖。一个`UndoableCommand`必须提供撤销一个动作以及初始执行所必需的数据。

##### 执行者

当一个`Command`被发布时，`Executor`接口的一个实现被用来执行特定的行为。

```
class ExampleExecutor implements UndoableExecutor<ExampleCommand> {
    @Override
    public void execute(ExampleCommand command) {
        System.out.println("Hello, World!");
    }

    @Override
    public void unexecute(ExampleCommand command) {
        System.out.println("Goodbye, World!");
    }

    @Override
    public void reexecute(ExampleCommand command) {
        System.out.println("Hello again, World!");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

##### 订阅命令

订阅一个`Command`需要您指定一个`Executor`和它将执行的`Command`。一个`Executor`将执行它所订阅的`Command`的类型，或者那个`Command`的任何子类。因此，只有一个此类型的`Executor`或`Command`的子类型。

```
CommandDelegator.getINSTANCE().subscribe(new ExampleExecutor(), ExampleCommand.class); 
```

Enter fullscreen mode Exit fullscreen mode

##### 发布(`Doing`)一个命令

发布一个`Command`就像将它传递给`publish()`方法一样简单。这将调用相关的`Executor`类的`execute()`方法。对`execute()`的调用将和对`publish`的调用在同一个线程上，这意味着如果你想启动一个任务在后台运行，它必须从后台发布。

如果已发布的`Command`不可撤销，它将清除当前的撤销历史。同样，如果您撤消了一个`Command`并发布了一个新的，重做历史将被清除。

```
CommandDelegator.getINSTANCE().publish(new ExampleCommand()); 
```

Enter fullscreen mode Exit fullscreen mode

##### 退回&准备

撤销和重做是对`CommandDelegator`的方法调用。这与`publish()`方法的操作方式相同，它将调用相关`Executor`类的`unexecute()`或`reexecute()`方法。

```
CommandDelegator.getINSTANCE().undo();
CommandDelegator.getINSTANCE().redo(); 
```

Enter fullscreen mode Exit fullscreen mode

# 全部搞定！

任何问题或建议都很好，也可以在这里💬，我的推特，或发邮件给我，地址:[jake@logdyn.com](mailto:jake@logdyn.com)