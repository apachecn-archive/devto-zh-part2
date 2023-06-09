# Java vs Kotlin

> 原文：<https://dev.to/apium_hub/java-vs-kotlin-5a33>

在 [Apiumhub](/) 中，我们与[无控制器架构](https://dev.to/apium_hub/apiumhub-software-architecture-meetups-in-barcelona-31df)合作，这种架构被称为纯 MVP(我们将其命名为 MVPP)，它基于功能组合和依赖投资的概念。你可以在这些文章中找到更多关于它的信息:iOS 中的[MVPP](https://dev.to/apium_hub/b-wom-case-study-pure-mvp-application-3ll1)，Android 中的[MVPP](https://dev.to/apium_hub/pure-model-view-presenter-in-android-1736)。在今天的文章中，我们将讨论 java 与 kotlin，并比较在 Kotlin 出现之前我们是如何做事情的，以及我们是如何通过它们来提高代码的可读性的。

## Java vs Kotlin

### λ和函数作为参数

为了将视图的事件与服务的事件链接起来，我们不直接公开可观察对象以便另一方可以进行订阅，而是公开接收函数作为参数的方法；视图或服务中发生事件时将执行的功能，例如:

```
 //KOTLIN

interface View {
  fun requestData(func: (isPaginating: Boolean) -> Unit)
  fun onDataLoaded(newData: Data)
}

interface Service {
  fun getData(isPaginating: Boolean)
  fun onDataLoaded(func: (data: Data) -> Unit)
}

class Presenter(view: View, service: Service) {
  init {
    view.requestData {
      service.getData(it)
    }
    service.onDataLoaded {
      view.onDataLoaded(it)
    }
  }
} 
```

类似的 Java 实现如下所示:

```
 //JAVA

public interface View {
    void requestData(Consumer func);
    void onDataLoaded(Data newData);
}

public interface Service {
    void getData(boolean isPaginating);
    void onDataLoaded(Consumer func);
}

public class Presenter {
    Presenter(final View view, final Service service) {
        view.requestData(service::getData);
        service.onDataLoaded(view::onDataLoaded);
    }
} 
```

这些实现(java 与 kotlin)之间的主要区别在于，在 Java 中，我们需要使用 Consumer 类来传递函数作为参数(在本例中，函数接收布尔类型的输入参数，但不返回任何内容)。如果我们想让这个函数作为参数返回某个东西，我们必须将消费者改为函数，如果我们不想要输入参数，我们必须使用供应商。但事情并没有就此结束，如果我们不是有一个参数，而是有两个，我们需要使用双函数或双消费者。但是如果我们希望有三个而不是两个，Java 并没有像我们预期的那样为我们提供解决方案(TriFunction 或 TriConsumer)，或者我们自己构建或者我们使用 Function >或 Function，U >。与 Kotlin 提供的解决方案相比，任何解决方案的可读性都更差，定义和实现起来也更复杂，kot Lin 将这一选项集成到了语言本身中。

### 这个。应用运算符

当我们必须实例化一个片段时，Android 让我们通过一个静态方法来完成，在这个静态方法中，我们将构建一个包，在这个包中我们写下我们想要传递给片段的参数。这是因为这个包是由操作系统存储的，以便在它被破坏的情况下重建这个片段。

在 Java 中，我们需要这样做:

```
 static MyFragment newInstance(String arg1, String arg2) {
  MyFragment fragment = new MyFragment();
  Bundle arguments = new Bundle();
  arguments.putString(ARG_1_KEY, arg1);
  arguments.putString(ARG_2_KEY, arg2);
  fragment.setArguments(arguments);
  return fragment;
} 
```

而在科特林，使用。应用运算符，我们会这样做:

```
 companion object {
  fun newInstance(arg1: String, arg2: String): MyFragment {
    return MyFragment().apply {
      arguments = Bundle().apply {
        putString(ARG_1_KEY, arg1)
        putString(ARG_2_KEY, arg2)
      }
    }
  }
} 
```

虽然两个代码片段的行数没有区别，但在 Kotlin 中，没有必要保留对片段或包的引用，我们还可以缩小代码每个部分的范围，因为我们知道在每个*中。apply* 我们正在编写代码，其中 *this* 将引用我们在其上制作了*的对象。应用*

*   *内部修饰符*:关于 Java，Kotlin 的另一个优点是*内部*可见性修饰符，它给了我们一个类、方法、接口等等的可见性……绑定到它所在的模块。如果我们不想测试一个我们不想在模块外公开的功能，那么这就特别有趣了。
*   协程:Kotlin 给我们带来的另一个新好处(尽管在 1.1 版本中仍处于试验阶段)是协程。协程只不过是相对于调用它的线程异步执行的一段代码。我们可以认为线程已经存在，但有几个不同之处。
    *   对于一个线程，操作系统负责管理它的执行、暂停它、恢复它、改变上下文等。这些操作是繁重的，如果我们试图启动大量线程，例如一百万个，我们的处理器将崩溃，并且花费更多的时间从一个线程改变到另一个线程，而不是执行我们希望它执行的代码。这是因为我们在 Java(或 Kotlin)中实例化的线程对应于操作系统的一个线程(无论是物理的还是虚拟的)，因此是操作系统的调度程序负责确定每个时刻应该执行哪个线程的优先级。
    *   然而，协程与操作系统线程没有对应关系，而是语言本身(在这种情况下是 JVM)负责执行每个协程，并在必要时在它们之间进行切换。在 Kotlin 的[文档示例中，我们可以看到一百万个线程和一百万个协程的实例化进行了比较:](https://kotlinlang.org/docs/tutorials/coroutines-basic-jvm.html)

```
 val c = AtomicInteger()

for (i in 1..1_000_000)
    thread(start = true) {
        c.addAndGet(i)
    }

println(c.get())

val c = AtomicInteger()

for (i in 1..1_000_000)
    launch {
        c.addAndGet(i)
    }

println(c.get()) 
```

实验的结果是使用协程肯定比线程快，尽管这不是 Java 和 Kotlin 之间的比较，我们可以比较 Java 中广泛使用的功能(线程)和 Kotlin 中实现的功能(协程)

这些只是 Kotlin 给我们带来的关于 Java 的一些变化，这些变化使我们在为 JVM 开发应用程序时变得更加容易。

如果你有兴趣了解更多关于 Java vs Kotlin 的知识，我强烈推荐你点击[这里](http://eepurl.com/cC96MY)订阅我们的每月时事通讯。

## 如果你觉得这篇关于 Java vs Kotlin 的文章很有趣，你可能会喜欢…

[iOS Objective-C app:成功案例研究](https://dev.to/apium_hub/protected-ios-objective-c-app-cornerjob-successfull-case-study-89e)

[年度移动应用开发趋势](https://dev.to/apium_hub/mobile-app-development-trends-of-the-year)

[Banco Falabella 可穿戴设备案例研究](https://apiumhub.com/tech-blog-barcelona/banco-falabella-wearable-ios-android/)

[移动开发项目](https://apiumhub.com/software-projects-barcelona/)

[面向 iOS 应用的 Viper 架构优势](https://apiumhub.com/tech-blog-barcelona/viper-architecture/)

[为什么是科特林？](https://dev.to/apium_hub/why-kotlin-language-android-why-did-google-choose-kotlin--639)

[软件架构会议](https://dev.to/apium_hub/apiumhub-software-architecture-meetups-in-barcelona-31df)

[安卓纯 MVP](https://dev.to/apium_hub/pure-model-view-presenter-in-android-1736)

Java vs Kotlin 的帖子最先出现在 T2 的 Apiumhub 上。