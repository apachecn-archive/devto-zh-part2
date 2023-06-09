# 用 Java、Javalin 和 GraalVM 构建一个 22 兆的微服务

> 原文：<https://dev.to/birdayz/building-a-22-megabytes-microservice-with-java-javalin-and-graalvm-4afn>

Oracle 的 GraalVM 允许提前(AOT)编译 JVM 应用程序。这意味着，编译器构建一个本地二进制文件，而不是运行一个 JVM 进程来执行应用程序。它是如何工作的？在一个非常高的层次上，一个基本的运行时(称为 SubstrateVM)被编译成二进制，以及实际的应用程序。听起来有点像 Go，它也包括一个小的运行时，例如垃圾收集。在本文中，我将展示如何使用 GraalVM 本地编译构建一个小型的 restful webservice 示例。示例应用程序是用 Java 编写的。为什么有人会对 JVM 应用程序的本地编译感兴趣？遗憾的是，在 E.ON 的日常工作中，我仍然需要处理大量的 Java 应用程序。我们的技术栈完全是云原生的——我们在 Kubernetes 上运行几乎所有的东西。我们的应用是“典型的”12 因素应用。在这样一个 dockerized 环境中，我认为有三个主要原因可以解释为什么原生编译会很有趣。

*   应用程序启动时间

这可能不完全是 Java 的错。我们用的是 Spring Boot，启动真的很慢。我通常必须调整我的 Kubernetes 就绪探测器，使其在启动 pod 后 20 秒内不进行检查。这是一个非常小的应用程序，只有 500 行代码。

*   内存占用

根据我的经验，你不会想给你的 Spring Boot 应用程序少于 512MB 的内存。否则可能需要几分钟才能启动。虽然 Java，尤其是 JVM 的开销是罪魁祸首，但这也是一个框架问题。春天很臃肿，用了很多倒影，这已经不是什么秘密了。

*   应用程序大小

Java 应用程序容器的大小是另一个问题。这很烦人，但并不重要。我能找到的最小的 JRE 大约是 65MB 大(基于 alpine 的 openJDK)。如果您使用 Spring Boot，您的应用程序将至少有一个~40MB 的大 fat jar。如果你的应用程序更大，它显然会更大。每个容器至少有 100MB。请注意，Docker 映像的 JRE 层可能会被多个 Docker 映像重用，因此对于您的应用程序的每个映像来说，它实际上不是 100MB+。虽然我认为在 2018 年拥有 100MB 以上的大型 hello world 应用程序肯定是可以忍受的，但如果我能拥有 6MB 的 Go 二进制文件，那就太弱了。

GraalVM AOT 编译可能会改善这种情况。我希望启动时间很快，不需要 JVM，并且应用程序的大小要小得多。GraalVM 有一些严重的限制，因为 JVM 的一些特性不能很好地与静态编译兼容。完整的名单可以在找到[。这里的文档非常清楚:动态类加载是受支持的，也是不受支持的。相反，编译器分析代码并将所有需要的类编译成二进制文件。结合反射，这成为当前 Java 生态系统的噩梦。许多库和框架使用反射来动态实例化类。GraalVM 不能很好地处理这一点，在许多情况下，必须提供额外的编译器配置。其中一个原因是，对 Class.forName()的调用可能基于运行时信息。一个很简单的例子:](https://github.com/oracle/graal/blob/master/substratevm/LIMITATIONS.md) 

```
if (someVariable) {
    Class.forName("SomeClazz")
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

由于编译时不知道 someVariable 的值，所以编译器无法知道是否包含“SomeClazz”。且不说它只是一个字符串，编译器还要在编译时在类路径上搜索这个类。如果编译器决定包含这个类，它就会这样做，如果找不到这个类，就会抛出一个错误。真好。然而，这只是尽力而为。不能保证所有需要的类在编译时都被包含在内——这意味着类可能会丢失，并且在被实例化时会抛出运行时错误。有更多的限制，要获得完整的参考资料，请参考 GraalVM 的[文档](https://github.com/oracle/graal/blob/master/substratevm/LIMITATIONS.md)。
作为概念的证明，我在寻找一个没有过多使用 reflect 的 rest 库。显然这不是春季开机-我选择了 [javalin.io](https://javalin.io) 。只是 Jetty 上面的一个休息库，仅此而已。

# 入门

虽然我推荐在 Docker 中执行构建，但在本地安装 GraalVM 非常有帮助。
我使用 [sdkman](https://sdkman.io) ，它简化了 JDK 的管理。如果您还没有安装 sdkman:

`curl -s "https://get.sdkman.io" | bash`

安装 GraalVM JDK:

`sdk install java 1.0.0-rc6-graal && sdk use java 1.0.0-rc6-graal`

让我们从一个非常简单的 Hello World 开始: