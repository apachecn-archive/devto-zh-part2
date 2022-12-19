# 不要喝太多活性清凉饮料

> 原文：<https://dev.to/stealthmusic/dont-drink-too-much-reactive-cool-aid-20lk>

*交叉发布[我的博客文章](https://return.co.de/blog/articles/dont-drink-too-much-reactive-cool-aid/)* 。

* * *

在过去的几年中，“反应式服务器应用程序”的概念得到了大力推广，尤其是通过新的框架。在 Java 世界里，已经有了 [RxJava](https://github.com/ReactiveX/RxJava) 、 [vert.x](//Eclipse%20Vert.x) 以及最近的 [Spring WebFlux](https://docs.spring.io/spring/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html) 。

虽然总体来说我喜欢这个潜在的想法，但是我发现作者和用户推动的方式是有问题的。在我与 Josh Long 在 twitter 上的对话之后，我想解释一下为什么。

围绕它有几个神话，最主要的是...

# 同步码慢/无功码快

这是真的吗？

那么首先，同步是什么意思？在早期，代码是在计算机上按顺序执行的。一条指令接一条指令，直到代码结束。出现了同时运行多个程序的需求。这就产生了对流程的需求。现在，您可以同时运行多个程序，而不一定是并行的。处理器将简单地在程序执行和保持内存分离之间来回切换。那时，需要在一个程序中同时做一些事情。创建了允许同时运行程序的多个部分但允许共享内存的线程。

同步代码将简单地运行在一个进程和一个专用线程中，直到它完成。

反应式编程模型假设对进出程序(I/O)的所有事物进行集中处理。一个线程通常会做多件事情，循环处理可能发生的事情。有新的 TCP 连接吗？我的字节被写入流了吗？文件是从磁盘加载的吗？一旦完成，它会通知一些注册的代码。这将是一个回调函数。

那么，这种同步代码慢于反应式代码吗？事实上，同步代码更快，因为线程不是用来做不同的事情。就像当你正在写一个 TCP 流时，没有什么会中断发送字节，因为程序的其他部分想要从文件中读取。所以假设一个理想的管道(比如无限的 I/O 吞吐量)，同步代码会更快。但大多数时候，I/O 比计算慢得多，这意味着在等待 I/O 完成的同时，有足够的时间做其他事情。

# 螺纹贵

现在，你有可能同时使用单线程做其他事情。为什么这是一个问题？创建一个线程需要几千字节的内存，并且在多个线程之间切换上下文也很昂贵。

见[此处](https://stackoverflow.com/questions/763579/how-many-threads-can-a-java-vm-support)举例。在某些时候，它会耗尽你所有的内存和 CPU。

# 无功秤佳

在传统的 web 服务器中，对于每个传入的 web 请求，要么启动一个新线程，要么从池中重用，然后是所有的处理、数据库调用等。是在那个线程中完成的。这仍然没有问题，直到你得到更高的负荷。由于 RAM 的使用和一些操作系统的限制，您不能拥有无限数量的线程。极限大概是几千个线程。因此，要么您的应用程序内存不足(没有实施线程限制)，要么它的处理响应速度较慢(线程池有限)，因为一些请求必须等到一个已用线程被释放。

事实上，要达到这个极限，你必须是一个幸运的人。例如，即使是一个 Spring Boot 应用程序的单个实例也能以极慢的响应时间提供相当高的负载。

当达到这个极限时，您可以开始生成应用程序的多个实例(如果可以的话)并使用负载平衡器。

换句话说:一个反应式应用程序在低负载下可能会有较慢的响应时间，但它能够在大量传入请求时保持这个响应时间不变。它能够更有效地使用服务器的现有资源。

这在几篇文章中有说明，你可以在这里找到和在这里找到。

# 编程模式

虽然使用异步 I/O 处理看起来是个好主意，尽管事实上并不是在所有场景中都“更好”,但是有一个严重的缺陷:它完全改变了您需要编写代码的方式。在大多数流行的编程语言中，你不能只是“打开”异步，就万事大吉了。

正如我之前提到的，有回调的概念。所以从概念上来说，不用做

```
 someFunction() {
        value = getValue()
        print(value)
    } 
```

Enter fullscreen mode Exit fullscreen mode

你需要这样做:

```
 someFunction() {
        getAsyncValue(callback)
    }

    callback(myValue) {
        print(myValue);
    } 
```

Enter fullscreen mode Exit fullscreen mode

但是，这只是最基本的情况。如果有嵌套调用，情况会变得更糟..

```
 someFunction() {
        getAsyncValue(valueCallback)
    }

    valueCallback(myValue) {
        getUserFromDbAsync(dbCallBack)
    }

    dbCallback(user) {
        print(user + myValue);
    } 
```

Enter fullscreen mode Exit fullscreen mode

这是两个调用的组合，哎呀，我如何将`myValue`传递给`dbCallback`函数？

现在我可以使用 lambdas 和闭包来冻结代码中的浮点值:

```
 someFunction() {
        getAsyncValue(myValue -> {
            getUserFromDbAsync(user -> {
                print(user + myValue)
            })
        })
    } 
```

Enter fullscreen mode Exit fullscreen mode

这样好些了吗？那么错误处理呢？简单:

```
 someFunction() {
        getAsyncValue(myValue -> {
            getUserFromDbAsync(user -> {
                print(user + myValue)
            }).onError(() -> {
                print("can't get user")
            }
        }).onError(() -> {
            print("can't get value")
        })
    } 
```

Enter fullscreen mode Exit fullscreen mode

如果我需要在我的`getAsyncValue`回调中处理 DB 用户错误怎么办？看看[我的 vert.x 2 评测文章](https://return.co.de/blog/articles/vertx-brings-awsomeness-javascript-jvm/)。这是旧的，但它涵盖了这篇文章的一些想法。

有了 WebFlux 这样的反应式堆栈，你可以使用链式伪函数调用，比如:

```
 someFunction() {
        getAsyncValue()
            .map(myValue -> print);
    } 
```

Enter fullscreen mode Exit fullscreen mode

然而，当您想要组合上面的调用并添加错误处理时，这就变得棘手了。你有一个很长的`.map`、`flatMap`和`zip`链，与异步版本相比不会有很大的改善，与同步版本相比也很糟糕。请看这里的例子[。实际上，一个方法调用链跨越 12 行，包括伪 if-else 调用。](https://dzone.com/articles/doing-stuff-with-spring-webflux)

在上述所有情况下，使用函数引用、lambdas 或反应式扩展，代码会遇到以下问题:

*   **可读性**
    *   显而易见，代码变长了
    *   它更复杂
    *   它不再有连续的流程。或者这样说:当代码被写在源代码中时，它以另一种顺序执行。
*   **可维护性**
    *   很难添加功能
    *   调试这段代码要复杂得多。你不能简单地穿过它
*   **可测试性**
    *   你不能简单地模仿一两个类并调用一个方法。您需要创建行为同步但实际上异步的对象。
*   **代码质量**
    *   因为通过阅读很难消化代码，所以会比同步代码有更多的 bug。
*   **发展速度**
    *   非常简单的任务，例如两个具有不同返回类型的 REST 请求，它们需要建立在彼此的基础上(例如，获取主数据，用一些相关数据丰富并返回合并到 UI)
    *   简而言之:你不能只是按照你的想法去写

我已经用 vert.x、RxJava 和最近的 Spring WebFlux 编写了专业应用程序，上述问题可以在所有这些程序中以不同的方式找到。

# 现在怎么办？

您真正想要的是可以同步读写的代码，但是由底层运行时以支持传统错误处理的异步方式执行:

```
 someFunction() {
        try {
            myValue = getAsyncValue()
            user = getUserFromDbAsync()
            print(user + myValue)
        } catch (ComputationException e) {
            print("can't get value")
        } catch (DbException e) {
            print("can't get user")            
        } 
```

Enter fullscreen mode Exit fullscreen mode

现在，您将拥有三行内的良好路径和独立的错误处理。代码非常容易理解。所发生的是，运行时将暂停`getUserFromDbAsync`的执行，直到数据库返回数据。同时，该线程将用于其他任务。

[Erlang](https://www.erlang.org/) 在其演员模型和 BEAM VM 中实现了这一点。当你从一个文件中读取数据时，你只需要调用`io:get_line`就可以得到结果。VM 将挂起当前的 actor 及其 Erlang 进程(这是一个轻量级线程，只占用几个字节的内存)。

对于 JVM，目前有一个项目[Loom](http://cr.openjdk.java.net/~rpressler/loom/Loom-Proposal.html)试图在 JVM 中实现延续，以及一个称为纤程的抽象作为 JDK 的一部分。对我来说，它看起来很有前途，但这需要一些时间，而且也不能 100%肯定它会成为 Java 的一部分。

Python 有 asyncio，它提供了一些比 Java 更高级的语言特性，但是它还是太暴露了。

# 结论

在我的文章中，我想强调的一点是，反应式编程并不是银弹(这是最常见的情况)。一定要知道你在做什么，以及你为什么需要它。

摘自我与乔希的讨论:

> ![𝙹𝚊𝚗 𝚆𝚎𝚍𝚎𝚕 profile image](img/b454ec2bcc80e189a896cf1718d1b8a9.png)[@ stealth music](https://dev.to/stealthmusic)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ starbuxman](https://twitter.com/starbuxman)我的观点是:WebMVC 和 WebFlux 都有存在的理由，但我认为 WebMVC 是大多数项目更好的选择，除非你有充分的理由并知道自己在做什么。这就像使用特斯拉疯狂模式。🚗2018 年 6 月 22 日上午 04:21[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1010014909906554881)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1010014909906554881)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1010014909906554881)

它认为目前只有少数场景需要构建完全反应式服务器应用程序。否则，就坚持用“经典”的方法。

问题是，在你用你的反应式锤子写了一段时间的应用程序之后(这有一个陡峭的学习曲线)，你把每一个任务都看成一个钉子。如果代码质量没有受到影响，但客观上确实受到了影响，这是可以的。

对于没有经验的开发人员来说，这可能看起来很酷，因为它很难理解。好像只有最好的开发者才会懂。但相信我，事实并非如此。最好的语言和 API 设计是那些*易于理解、编写和阅读*的设计。