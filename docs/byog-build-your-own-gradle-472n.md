# BYOG(建立你自己的梯度)

> 原文：<https://dev.to/cjbrooks12/byog-build-your-own-gradle-472n>

对于 Java 生态系统中的构建自动化和依赖性管理，Gradle 是一个非常强大的工具。几乎*太*强大，如果你明白我的意思。有时候和 Gradle 一起工作感觉有点*太神奇了*，取决于你想做什么，这可能看起来有点过头了。

本周，在努力将 Dokka 与 Orchid 集成起来以便我可以记录我的 Kotlin 代码时，我发现我需要下载一些 Maven 工件并执行它们，就好像我在运行一个 T2 JavaExec 任务 T3 一样。但是由于各种原因，我不想使用 Gradle 来做这件事，而且我也不能把它们捆绑在兰花本身中，所以我决定弄清楚 Gradle 是如何为我自己构建同样的东西，如果你跟着做，你也能为你自己做同样的事情！

## 第一步:如何运行 Java 程序

为了从 Orchid 运行 Dokka，我需要解决的第一件事就是如何从命令行执行一个 jar。

对于那些不熟悉 Java 的人来说，这基本上包括收集一堆 jar 文件(编译后的类文件和相关资源的 Java 档案)并使用`-classpath`标志将它们传递给`java`命令。您用`:`(或 Windows 上的`;`)连接运行程序所需的所有 jar 的路径，提供包含`main()`方法的类，它神奇地工作了！

对于运行 Dokka，这意味着类似于:

```
java -classpath /path/to/dokka.jar:/path/to/my-dokka-formatter.jar:/path/to/kotlin.jar org.jetbrains.dokka.MainKt 
```

Enter fullscreen mode Exit fullscreen mode

这里，我想运行 Dokka 程序，它包含在`dokka.jar` jar 中，使用我编译成`my-dokka-formatter.jar`的自定义格式化程序，它依赖于`kotlin.jar`。指定 Dokka 主类`org.jetbrains.dokka.MainKt`后，Java 将启动一个 JVM 实例，从我提供给它的 jar 中加载它需要的类，并执行 main 方法。现在我们开始工作了！

## 第二步:如何得到那些罐子

有了这样一个小的可执行文件，从技术上讲，手动下载所有这些 jar 文件是可行的。但是，当其中一个 jar 依赖于另一个 jar 中定义的类，而另一个 jar 又依赖于另一个 jar 中定义的类时，这就会变得非常困难.....

您可以看到，要确保您下载了所有正确的 jar，以便整个程序能够正确运行，这可能会变得非常棘手。这就是像 [Maven](https://maven.apache.org/) 和 [Gradle](https://gradle.org/) 这样的工具存在并如此受欢迎的原因，因为它们让这个过程简单得几乎令人痛苦。

在许多其他事情中，这些工具能够接受一个简单的依赖关系声明，并找出如何下载它们并一起运行它们。Gradle 使用 Groovy 并提供了一个非常简单的 DSL，在那里可以简单地声明你的依赖项，它会完成剩下的工作。

```
repositories {
  jcenter()
}
dependencies {
  compile 'io.github.javaeden.orchid:OrchidAll:0.10.1'
} 
```

Enter fullscreen mode Exit fullscreen mode

Maven 依赖项由 3 部分组成，在 Gradle 语法中由`:`分隔。它们是:

*   生产工件的个人或组织
*   `artifactId`:项目名称
*   `version`:项目的版本，通常使用[语义版本](https://semver.org/)声明

这 3 个部分保证编译和执行使用一个唯一的 jar，因此在您的构建中使用它们是确定的(从一个构建到下一个构建没有什么变化)。

获取所有这些的过程出奇的简单。本质上，它们被组合成一个指向某个 web 服务器上的文件的 URL，比如 [JCenter](https://jfrog.com/knowledge-base/why-should-i-use-jcenter-over-maven-central/) ，并且您可以通过 HTTP 请求该文件。这些服务器通常被称为“Maven 仓库”，但实际上你可以使用任何服务器来托管你的工件([甚至 Github 页面](https://gist.github.com/fernandezpablo85/03cf8b0cd2e7d8527063)，如果你*真的*想的话)。但这真的就是全部了。您可以通过访问下面的链接下载上面声明的 OrchidCore jar 来亲自尝试一下。

[https://jcenter . bin tray . com/io/github/javaeden/orchid/orchid all/0 . 10 . 1/orchid all-0 . 10 . 1 . jar](https://jcenter.bintray.com/io/github/javaeden/orchid/OrchidAll/0.10.1/OrchidAll-0.10.1.jar)

概括的格式是:`[repository URL]/[groupId (replace dots with /)]/[artifactId]/[version]/[artifactId]-[version].jar`

## 步骤 3:解析 Jar 依赖关系

现在我们知道了如何获得这些 jar，我们需要一些方法来找出一个 jar 所依赖的其他 jar。创建 Maven 的聪明人让这变得非常简单，因为 Maven 仓库中托管的每个 Jar 都有相应的`POM`，或者“项目对象模型”。

POM 是一个 XML 文档，包含一些关于这个 jar 的基本元数据，比如它的名称和描述、贡献者列表、VCS url 等等。但是它还包含一个用于`dependencies`的特殊部分，如下所示:

```
<dependencies>
  <dependency>
    <groupId>io.github.javaeden.orchid</groupId>
    <artifactId>OrchidCore</artifactId>
    <version>0.10.1</version>
    <scope>compile</scope>
  </dependency>
  ...
</dependencies> 
```

Enter fullscreen mode Exit fullscreen mode

这看起来眼熟吗？它应该是，因为它是一个依赖列表，具有用于下载这个 POM 的相同的`artifactId`、`groupId`和`version`！依赖关系中还有一个额外的`scope`标记，通常是`compile`或`test`。为了编译和运行这个 jar，需要编译依赖项，并且需要测试依赖项来运行那个项目的测试。

无论您是使用 Maven 还是 Gradle 来构建您的项目，发布的工件都会有这个 POM 文件。好的一面是，您可以用与 jar 相同的方式下载这个文件，网址几乎相同。你只需要用`.pom`扩展名代替`.jar`就可以了。

[https://jcenter . bin tray . com/io/github/javaeden/orchid/orchid all/0 . 10 . 1/orchid all-0 . 10 . 1 . POM](https://jcenter.bintray.com/io/github/javaeden/orchid/OrchidAll/0.10.1/OrchidAll-0.10.1.pom)

## 第四步:把所有东西放在一起

对于 Maven 规范，这就是您需要知道的全部内容，现在您已经准备好编写自己的 Gradle 了！

首先，我们需要获得运行 Java 程序所需的所有 Maven 工件的列表。我将在这里使用 kotlin-y 伪代码，但是我将在本文末尾包含一个链接，指向我实际实现它的 kotlin 文件。

这是一个固有的递归过程(用罐子换罐子...)，但是它很自然地通过处理队列而不是使用递归来完成。本质上，我们需要从队列中弹出一个 Maven 工件，下载它的 jar 和它的 POM，在磁盘上缓存 JAR，然后将它的 POM 中的依赖项添加回队列。当队列为空时，我们就完成了！

```
val queue = queueOf("com.github.copper-leaf:dokka-json:0.1.0")
while(queue is not empty) {
  val artifact = queue.pop()
  val jar = downloadJar(artifact)
  val pom = downloadPom(artifact)
  queue.addAll(pom.dependencies)
} 
```

Enter fullscreen mode Exit fullscreen mode

太好了，我们已经下载了我们需要的所有 jar，包括我们所有的依赖项！现在我们只需要运行我们的 Dokka 程序。让我们稍微扩展一下这个例子，展示一下我们是如何完成的。

```
val queue = queueOf("com.github.copper-leaf:dokka-json:0.1.0")
val jars = emptyList()
while(queue is not empty) {
  val artifact = queue.pop()
  val jar = downloadJar(artifact)
  val jarPath = cacheJar(jar)
  jars.add(jarPath)
  val pom = downloadPom(artifact)
  queue.addAll(pom.dependencies)
}

ProcessBuilder()
  .command(
    "java",
    "-classpath", jars.joinToString(File.pathSeparator),                         
    "org.jetbrains.dokka.MainKt"
  )
  .start() 
```

Enter fullscreen mode Exit fullscreen mode

现在你知道了！在不到 20 行代码中，我们完全实现了自己的 Gradle，包括依赖解析和 Jar 执行！

当然，这不是一个非常稳健的解决方案。Gradle 在幕后做了很多工作，以确保它没有下载已经下载的文件，并在请求多个版本时确定使用哪个 jar，以及其他一百万件事情。

但是，我仍然发现这个过程如此简单，令人惊讶。仅仅用了几个小时，总共大约 100 行代码，我就在 Kotlin 中实现了这个，这样我就可以在 Orchid 中运行 Dokka，而且效果惊人地好。你可以去[这里](https://github.com/JavaEden/Orchid/blob/de15d707136c0c921381a2c3d7c6e4554c664017/plugins/OrchidKotlindoc/src/main/kotlin/com/eden/orchid/kotlindoc/helpers/KotlindocInvokerImpl.kt#L53-L172)查看源代码，或者就呆在这里，直到完全集成完成，你就可以用 Orchid 无缝地记录你的 Kotlin 项目了。

与此同时，你可能想去看看正在进行的关于 [Orchid](https://orchid.netlify.com/) 的很酷的东西，并学习如何为自己使用它，所以当 Kotlindoc 插件准备好了，你将准备好拿起它！你也可以看看[的一个](https://orchid.netlify.com/)的[的](https://javaeden.github.io/Clog/)的[的](https://www.caseyjbrooks.com/)的[的](https://github.com/copper-leaf/copper-leaf.github.io)的网站，我已经用兰花建立了许多的网站，为你的下一个项目获得灵感。

顺便说一下，兰花不仅仅是为了记录 Kotlin，你还可以做 [Javadoc](https://orchid.netlify.com/plugins/OrchidJavadoc) 和 [Swift](https://orchid.netlify.com/plugins/OrchidSwiftdoc) 代码文档，以及维护你的项目的 [wiki](https://orchid.netlify.com/plugins/OrchidWiki) 和[更多的](https://orchid.netlify.com/group/plugins)！