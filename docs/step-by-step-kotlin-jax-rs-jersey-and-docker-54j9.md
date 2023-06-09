# 循序渐进:科特林，JAX-RS(新泽西)，和多克

> 原文：<https://dev.to/baens/step-by-step-kotlin-jax-rs-jersey-and-docker-54j9>

*本文最初发表于我的[个人博客](https://blog.baens.net/posts/step-by-step-kotlin-jaxrs-hello-world/)*

大家好，欢迎来到我正在进行的一步一步教程系列中的另一篇博文。这一个将实际上建立在最后一个 [Hello Kotlin](https://blog.baens.net/posts/step-by-step-kotlin/) 之上，并成为一个 [JAX-RS](https://en.wikipedia.org/wiki/Java_API_for_RESTful_Web_Services) 网络服务。JAX-RS 的这种特殊实现将是[球衣](https://jersey.github.io/)。Jersey 实际上是 Oracle 的参考实现，但老实说，我还没有花足够的时间介绍其他一些实现。

## [第 0 步:先决条件](https://github.com/baens/blog-step-by-step-jaxrs/commit/830399adbaf4da38e03146b93cf4cfa58a03929e)

对于这一个，我将从我的 Kotlin Hello World 库开始。这已经有了一个 Gradle 包装器设置，以及一些基本的东西。我已经把`mainClassName`改成了`Server.kt`和`archivesBaseName`来匹配这个项目的名字。

## [第一步:Web 服务器](https://github.com/baens/blog-step-by-step-jaxrs/commit/cb1373152bf52d7cdf5aa08489bafc327cf7912e)

第一步是让一个工作的 web 服务器启动并运行。这个项目将使用 grizzly web 服务器，我推荐它作为嵌入式 web 服务器。它有着相当不错的历史表现，从我的使用来看，它做得相当不错。

让我们来看看如何设置一个快速服务器，这样我们就可以验证 HTTP 管道是否正确。

**src/Server.kt**

```
import org.glassfish.jersey.grizzly2.httpserver.GrizzlyHttpServerFactory
import javax.ws.rs.core.UriBuilder

fun main(args: Array<String>) {
    val url = UriBuilder.fromUri("http://0.0.0.0/")
            .port(8080)
            .build()

    val httpServer = GrizzlyHttpServerFactory.createHttpServer(
            url,
            true
    )

    if (System.getenv()["SHUTDOWN_TYPE"].equals("INPUT")) {
        println("Press any key to shutdown")
        readLine()
        println("Shutting down from input")
        httpServer.shutdownNow()
    } else {
        Runtime.getRuntime().addShutdownHook(Thread {
            println("Shutting down from shutdown hook")
            httpServer.shutdownNow()
        })

        println("Press Ctrl+C to shutdown")
        Thread.currentThread().join()
    }
} 
```

**第 6 行**:这为服务器绑定创建了一个 URI。这个特殊的使`http://localhost:8080`能够工作。可能有些过火，但这正是 Grizzly 服务器工厂想要的。

**第 9 行**:这创建了 Grizzly 服务器，第二个参数(`true`)使服务器立即启动。

**第 14 - 27 行**:现在，让我来解释一下，因为这是一种魔法。当在 Gradle ( `./gradlew run`)下运行时，Gradle 捕获所有的输入并将其传递给应用程序。例如，在命令行上，你通常通过点击`Ctrl+C`来停止应用程序。然而，当您在 Gradle 下运行它时，这不起作用，因为 Gradle 会捕获信号并停止运行应用程序的守护程序。有时，Gradle 守护进程没有正确地停止，或清理一切，并在下次运行时产生混乱。

所以我解决这一切的方法是在环境变量中设置一个特性标志。当在 Gradle 下运行时(等一下，我会告诉你怎么做),我们只是等待输入。当在其他所有东西下运行时(即 docker，`java -jar`)，我们将等待 Ctrl+C 或来自其他地方的 kill 信号。

现在，对于工具链位，我们需要添加依赖项并设置运行位，以便在没有关闭挂钩的情况下工作。

**build.gradle(仅零件)**

```
...
dependencies {
    compile 'org.jetbrains.kotlin:kotlin-stdlib-jre8'
    compile "org.glassfish.jersey.containers:jersey-container-grizzly2-http:2.26"
}
...
// Properties for `./gradlew run`
run {
    standardInput = System.in
    environment = ["SHUTDOWN_TYPE" : "INPUT"]
} 
```

**第 4 行**:我们添加 grizzly http 服务器和 Jersey 容器。

**第 8 - 11 行**:这是我们让`./gradlew run`工作的方式。我们设置的`standardInput`为正常的`System.in`(默认为破东西的空集)。然后我们用`environment`配置设置环境变量。

我们需要添加一种方式让 Docker 将端口暴露给外界。因为我们绑定在端口 8080 上，所以我们也需要公开它。所以在`Dockerfile`中，我们将在 CMD 语句前添加一行。

**Dockerfile(仅零件)**

```
EXPOSE 8080

CMD ["java","-jar","run.jar"] 
```

因此，请验证运行应用程序和查看`http://localhost:8080`的两种方式。

第一次运行`./gradlew run`，如果你按下任何键，它将关闭。

第二次运行 docker 命令`docker build -t myserver . && docker run -p 8080:8080 myserver`。你可以运行它，然后点击 *Ctrl+C* 来关闭它。

docker 命令有两个部分:首先构建想要运行的映像，然后运行它。您在 build 命令中传递的`-t`是一个标记，便于以后查找。在运行部分,`-p`是我们将绑定到主机的端口。在本例中，我们将容器的 8080 端口映射到主机的 8080 端口。

## [步骤 2:添加打印“Hello World”的简单 JAX-RS 端点](https://github.com/baens/blog-step-by-step-jaxrs/commit/c281332c897fa4bb0de573d447ab3c6adebd5ee1)

为此，让我们创建所需的管道来获得所需的资源。有一点点牦牛毛需要这个设置来获得所有正确的部分，但我想我已经把它归结为简单的部分。

首先，让我们看看第一个端点的资源(这些是端点组的名称)是什么样子的。

**src/jaxrs/resources/hello world . kt**

```
package jaxrs.resources

import javax.ws.rs.GET
import javax.ws.rs.Path

@Path("helloWorld")
class HelloWorldResource{
    @GET
    fun helloWorld() = "Hello World"
} 
```

这里真的没什么好谈的。`@Path`是终点所在的位置(所以在这种情况下是`/helloWorld`)。这将从 HTTP GET 中检索。

好了，下一步是让系统自动注册这些资源。因此，让我们设置所需的配置。

```
package jaxrs

import org.glassfish.jersey.server.ResourceConfig

class Application : ResourceConfig() {
    init {
        packages("jaxrs.resources")
    }
} 
```

同样，这是相当直接的。 [ResourceConfig](https://jersey.github.io/apidocs/latest/jersey/org/glassfish/jersey/server/ResourceConfig.html) 是安装所有基础应用程序的地方。在这个例子中。我们扫描`jaxrs.resources`名称空间，寻找可以向 JAX-RS 注册的任何资源或其他东西。这是一个特定的球衣，但它使事情更容易设置。

好了，现在我们已经为 JAX-RS 和一个资源设置了所有的配置，让我们让服务器识别它。所以在`Server.kt`文件中添加了这个。

**Server.kt(零件)**

```
val httpServer = GrizzlyHttpServerFactory.createHttpServer(
            url,
            Application(),
            true
    ) 
```

**第 12 行**:这就是你需要补充的。一个新的`Application`类实例和 Grizzly 服务器会为您完成所有剩下的工作。

现在，最后一件事，我们需要为 Jersey 添加一个依赖项。这将使所有神奇的依赖注入在幕后发生。Jersey 团队制作了这种可配置的依赖关系，因此如果您想要不同类型的注射系统，您可以。我们将使用的具体实现是 [HK2](https://javaee.github.io/hk2/) ，而不是 [Guice](https://github.com/google/guice) 或其他。这只是我用过的默认设置，没有任何问题，所以我推荐它。这是需要添加的一行。

**build.gradle (parts)**

```
dependencies {
    compile 'org.jetbrains.kotlin:kotlin-stdlib-jre8'
    compile "org.glassfish.jersey.containers:jersey-container-grizzly2-http:2.26"
    compile "org.glassfish.jersey.inject:jersey-hk2:2.26"
} 
```

继续运行以查看`http://localhost:8080/helloWorld`上的输出。

## [第三步:添加 JSON 处理](https://github.com/baens/blog-step-by-step-jaxrs/commit/173f0916dbea62ca4f5ed982bf7bdaf062af58fb)

现在我们有了一个简单的 web 服务器，让我们开始 JSON 处理。这不是开箱即用的配置，但它是直截了当的。让我们设置并运行它，并通过一个端点设置来演示这一点。

让我们设置我们的资源，以获得显示 JSON 输出所需的部分。

**src/jaxrs/resources/hello world . kt**

```
@GET
fun helloWorld() = "Hello World"

data class HelloJson(val prop1: Int, val prop2: String)

@GET
@Path("json")
@Produces(MediaType.APPLICATION_JSON)
fun helloJson() = HelloJson(1, "test") 
```

**第 13 行**:这就是我爱 Kotlin 的原因，这是一个数据类。一个非常纯粹和简单的结构来传递数据。我们有两个将向 JSON 接口公开的属性。一个是整数，另一个是字符串。

**第 15-18 行**:这是你如何设置一个将产生 JSON 对象的端点(好吧，也强制它，但是那是另外一天，或者去阅读 [Jersey 文档](https://jersey.github.io/documentation/latest/jaxrs-resources.html))。我们只是简单地返回一个数据类的实例。

现在，我们需要调整我们的依赖关系并添加 JSON 处理。当我们这样做的时候，我们也将违反 3 的[规则，并且我们将重构可能改变的公共事物。让我们看看结果，然后我们会讨论这到底意味着什么。](https://en.wikipedia.org/wiki/Rule_of_three_(computer_programming))

**build.gradle (parts)**

```
def jerseyVersion = "2.26"

dependencies {
    compile 'org.jetbrains.kotlin:kotlin-stdlib-jre8'
    compile "org.glassfish.jersey.containers:jersey-container-grizzly2-http:${jerseyVersion}"
    compile "org.glassfish.jersey.inject:jersey-hk2:${jerseyVersion}"
    compile "org.glassfish.jersey.media:jersey-media-json-jackson:${jerseyVersion}"
} 
```

**第 22 行**:我们添加了这一行，因为可以在所有这些球衣依赖项之间改变的部分是球衣版本。因此，让我们现在就开始工作，这样我们就可以在一个地方进行版本更改。3 法则的一个小应用，但是嘿，它有效。

**第 28 行**:这是添加[杰克森](https://github.com/FasterXML/jackson)来做我们的 JSON 处理。这是我们将需要使这一切工作的神奇的依赖。

此时，您可以运行它并点击`http://localhost:8080/helloWorld/json`，您应该会看到 JSON 输出。

有了这些，你现在应该有危险的基本知识了。您可以设置一个 HTTP REST 服务器，然后带着您需要的任何东西进城。但是，嘿，让我再加一点额外的奖励，也许能帮到你。

## [加分第四步:添加详细日志](https://github.com/baens/blog-step-by-step-jaxrs/commit/7c753f0d1ee6d474a2801a805a20fa7fa4db98e6)

只是为了更容易地在命令行上看到进出服务的请求。这有助于根据具体情况证明你是疯了还是正常，让我们添加一个记录器来输出每个请求。

谢天谢地，这相当简单。我们只需要调整一下`Application.kt`的设置。这是你需要的。

**src/jaxrs/Application.kt**

```
register(LoggingFeature(Logger.getLogger(LoggingFeature.DEFAULT_LOGGER_NAME),
        Level.INFO,
        LoggingFeature.Verbosity.PAYLOAD_ANY,
        Integer.MAX_VALUE)
) 
```

这些是您需要添加到`init`块中的行。相当直接(确保添加所有正确的`import`语句！).现在应该在命令行上显示所有的请求和响应，这样您就可以验证事情是否按照预期进行。