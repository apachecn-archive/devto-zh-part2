# 一个 spring 开发者的旅程

> 原文：<https://dev.to/viniciusccarvalho/from-boot-to-native-kotlin-the-journey-of-a-spring-developer-584f>

# 简介

Spring boot 可能是我整个职业生涯中见过的最成功的框架。我从 2004 年开始使用 spring，不久前我甚至还做过 Spring committer。

Spring 也喜欢 Kotlin，框架和语言之间有很大的协同作用。这两个团队一直在紧密合作，使之成为一个无缝的集成，这比以前有很多反射问题的时候要好得多。

但是自从我开始用 kotlin 写代码以来，有一件事一直萦绕在我的脑海里:*现在如果没有 Spring，要写一个应用程序需要什么？*

这就是我这篇文章的目标，向你介绍几个 kotlin 框架，当它们结合在一起时，可以给你很多现代微服务(抱歉)工具包。

# 申请

我们将创建一个非常简单的 REST 应用程序，它从 mongodb 读取数据，并通过 RESTful 接口呈现出来。

这里的目标不是讨论应用程序本身，而是讨论如何在不利用 Spring Boot 便利性的情况下将东西放在一起。

我相信学习新概念的最好方法是在您已经熟悉的环境中介绍它们，这就是为什么我决定在谈到每一层的架构设计时遵循许多 Spring 应用程序中常见的方法。更有经验的 ktor 开发人员可能会觉得这很尴尬，但我发现这是一个非常简单的方法，可以将我目前的心态转化为一个新的框架。

作为大多数 spring 应用程序，我们将拥有以下组件:

*   引导容器的主类
*   配置类(或模块)
*   仓库
*   广告服务
*   控制器

## 先决条件

您将需要一个 mongodb 服务器，在项目文件夹的根目录下有一个名为`bitcoin.csv`的文件。该文件包含一枚 BTC 硬币从 2018 年 1 月到 2018 年 7 月的价格。请使用`mongoimport -d kotlin -c coins --type csv --file bitcoin.csv --headerline --columnsHaveType`导入它，如果你需要更多关于如何使用`mongoimport`工具的信息，请查看 [mongodb 文档](https://docs.mongodb.com/manual/reference/program/mongoimport/)

# 入门

完整的项目源代码可在[这里](https://github.com/viniciusccarvalho/boot2kotlin)获得。

我们将依靠 3 个科特林框架

*   ktor:kot Lin 的异步 web 堆栈
*   阿迪框架帮助我们进行依赖注入
*   [kmongo](http://litote.org/kmongo/) :为 mongo 客户端提供扩展

KTor 有一个漂亮的[生成器](https://ktor.io/quickstart/generator.html#)，看起来非常类似于 Spring initializr。访问生成器 e，使用以下配置创建一个示例项目:

*   Group : io.igx.kotlin(那是示例 app 的包，可以随意更改，但是从 github repo 复制可能需要更改包名)
*   服务器功能选择以下内容:静态内容、位置、默认标题、GSON

点击 Build 并下载 zip 文件。

在您的 IDE 上打开项目，并将以下依赖项添加到您的构建中

```
compile "org.kodein.di:kodein-di-generic-jvm:5.2.0"
compile "org.litote.kmongo:kmongo-native:3.8.1" 
```

Enter fullscreen mode Exit fullscreen mode

不要太担心生成的名为`application.kt`的文件，我们很快就会改变它，现在让我们关注依赖注入...

## 依赖注入与 Kodein

[kodein](http://kodein.org/) 文档非常全面，我不会试图在这里解释一切，但相反，我会尝试追踪一些与 Spring 的相似之处。
Kodein 有一个上下文，很像 Spring，它可以被分成模块(我相信人们可以把它与 Spring 中的`@Configuration`文件联系起来，它产生了父上下文的子上下文)
在这个项目中有一个文件`KodeinModules.kt`，它包含了我们需要的所有模块，这个文件的一个示例:

```
val common = Kodein.Module(name = "common") {
   bind("mongoClient") from singleton { KMongo.createClient() }
}

val repositories = Kodein.Module(name = "repositories") {
   bind(tag = "coinRepository") from singleton { CoinRepository(instance("mongoClient")) }
}

val services = Kodein.Module(name = "services") {
   bind(tag="coinService") from singleton { CoinService(instance("coinRepository")) }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个文件创建了三个模块(暂时)并将它们连接在一起，包含`CoinService`和`CoinRepository`代码的类在
下面列出

```
package io.igx.kotlin.repository

import com.mongodb.MongoClient
import com.mongodb.client.MongoCollection
import io.igx.kotlin.domain.Ticker
import org.litote.kmongo.*
import java.time.LocalDateTime

/**
 * @author vinicius
 *
 */
class CoinRepository(val client: MongoClient) {

    val collection: MongoCollection<Ticker> = client.getDatabase("kotlin").getCollection<Ticker>("coins")

    fun findInRange(symbol: String, start: LocalDateTime, end: LocalDateTime) : List<Ticker> {
        return collection.find(Ticker::symbol eq symbol, Ticker::lastUpdated gt start, Ticker::lastUpdated lt end).sort(ascending(Ticker::lastUpdated)).toList()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
package io.igx.kotlin.service

import io.igx.kotlin.domain.Ticker
import io.igx.kotlin.repository.CoinRepository
import java.time.Duration
import java.time.LocalDateTime

/**
 * @author vinicius
 *
 */
class CoinService(val repository: CoinRepository) {

    fun find(symbol: String, start: LocalDateTime, end: LocalDateTime) : List<Ticker> {
        if(Duration.between(end, start).toDays() > 31){
            throw IllegalStateException("Maximum number of days for full queries can not exceed 31 calendar days")
        }
        return repository.findInRange(symbol, start, end)
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

对于要编译的项目，您将需要包含我们的域对象
定义的`Model.kt`文件

```
package io.igx.kotlin.domain

import io.ktor.locations.Location
import java.time.LocalDate
import java.time.LocalDateTime

/**
 * @author vinicius
 *
 */
data class Ticker(val symbol: String, val name: String, val price: Double, val marketCap: Double, val lastUpdated: LocalDateTime)

@Location("/coins/{symbol}")
data class TickerRequest(val symbol: String, val start: LocalDate, val end: LocalDate ) 
```

Enter fullscreen mode Exit fullscreen mode

暂时忽略`@Location`声明，我们将很快回来。

连接这些文件后，你就有了一个完全正常工作的`Kodein`应用程序，你可以写一个非常简单的单元测试来尝试一下，比如测试文件夹
中的`RepositoryTests`类中的

```
class RepositoryTests {

    val context = Kodein{
        import(common)
        import(repositories)
    }

    @Test
    fun testFindInRange() {
        val repository: CoinRepository by context.instance("coinRepository")
        val results = repository.findInRange("BTC", LocalDateTime.of(2018, 6, 1, 0, 0) ,LocalDateTime.now())
        assertTrue { results.isNotEmpty() }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是启动 kodein 上下文并运行测试所需的全部内容，我们在 kodein 声明中使用`import`来仅导入我们想要测试的模块
，这不会将其他模块如`services`或`controllers`模块添加到上下文中。

## 带 ktor 的异步 web 服务器

Ktor 是一个构建异步服务器的框架，HTTP 是其中之一。

原来的`application.kt`是这样的:

```
fun main(args: Array<String>): Unit = io.ktor.server.netty.DevelopmentEngine.main(args)

fun Application.module() {
    install(ContentNegotiation) {
        gson {
        }
    }

    install(Locations) {
    }

    install(DefaultHeaders) {
        header("X-Engine", "Ktor") // will send this header with each response
    }

    routing {
        get("/") {
            call.respondText("HELLO WORLD!", contentType = ContentType.Text.Plain)
        }

        // Static feature. Try to access `/static/ktor_logo.svg`
        static("/static") {
            resources("static")
        }

        get("/json/gson") {
            call.respond(mapOf("hello" to "world"))
        }

        get<MyLocation> {
            call.respondText("Location: name=${it.name}, arg1=${it.arg1}, arg2=${it.arg2}")
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这将使用`HOCON`文件来[引导](http://ktor.io/servers/configuration.html)正确的模块配置并安装所有的[特性](http://ktor.io/features/index.html)。
但是我们也需要引导 kodein。

整合`ktor`和`kodein`需要在`ktor`方面做一些调整。两个框架都有类似的概念`Application`和`Module`
，但是我们需要确保这两个框架的入口点配置正确。

Ktor 有一个非常灵活的机制来修改它的[生命周期](http://ktor.io/servers/lifecycle.html)，所以我们将以此为切入点来启动
服务器，并利用 kotlin 接收器函数在应用程序中混合 Kodein 代码。

让我们修改文件`application.kt`，使其看起来像这样:

```
package io.igx.kotlin

import io.igx.kotlin.config.common
import io.igx.kotlin.config.controllers
import io.igx.kotlin.config.repositories
import io.igx.kotlin.config.services
import io.ktor.application.*
import io.ktor.features.*
import io.ktor.server.engine.*
import io.ktor.gson.*
import io.ktor.locations.Locations
import io.ktor.server.netty.Netty
import io.ktor.util.DataConversionException
import org.kodein.di.Kodein
import org.kodein.di.generic.bind
import org.kodein.di.generic.singleton
import java.time.LocalDate
import java.time.format.DateTimeFormatter

fun main(args: Array<String>){
    embeddedServer(Netty, port = 8080) {
        kodeinApplication{
            application ->
            application.install(ContentNegotiation) {
                gson {
                }
            }
            application.install(DefaultHeaders){
                header("X-Engine", "Ktor")
            }

            application.install(Locations)
            application.install(DataConversion){
                convert<LocalDate> {
                    val format = DateTimeFormatter.ofPattern("yyyy-MM-dd")
                    decode { values, _ ->
                        values.singleOrNull()?.let { LocalDate.parse(it, format) }
                    }
                    encode { value ->
                        when(value) {
                            null -> listOf()
                            is LocalDate -> listOf(value.format(format))
                            else -> throw DataConversionException("Cannot convert $value as Date")
                        }
                    }
                }
            }
            import(common)
            import(repositories)
            import(services)
            import(controllers)
        }
    }.start(wait = true)
}

fun Application.kodeinApplication(kodeinMapper : Kodein.MainBuilder.(Application) -> Unit = {}) {
    val app = this
    val kodein = Kodein {
        bind<Application>() with singleton { app }
        kodeinMapper(this, app)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个`kodeinApplication`函数是我的灵感来源！感谢 slack 上的 ktor 开发人员分享了这个。基本上，这是一个高阶函数
，接收器将在 kodein 上下文中注册来自 ktor 的应用程序对象。
在这之后，你可以把它传递给`embededdServer`，作为一种通过隐式对象配置应用程序的方式，或者导入 kodein 模块
，就像你在常规的`Kodein Builder`上所做的那样

这里最重要的是 kodein 模块的`import`和`kodeinApplication`函数。

让我们不要忘记我们的控制器声明，如果你回到`KodeinModule.kt`添加这个部分:

```
val controllers = Kodein.Module(name = "controllers") {
    bind(tag="coinController") from eagerSingleton { CoinController(kodein) }
} 
```

Enter fullscreen mode Exit fullscreen mode

这次我们使用了一个`eagerSingleton`,因为我们将在启动时在控制器中注册路线，否则您将无法访问该路线。

`CoinController`

```
package io.igx.kotlin.controllers

import io.igx.kotlin.domain.TickerRequest
import io.igx.kotlin.service.CoinService
import io.ktor.application.Application
import io.ktor.application.call
import io.ktor.routing.routing
import org.kodein.di.Kodein
import org.kodein.di.KodeinAware
import org.kodein.di.generic.instance
import io.ktor.locations.*
import io.ktor.response.respond

/**
 * @author vinicius
 *
 */
class CoinController(override val kodein: Kodein) : KodeinAware {

    val app: Application by instance()
    val service: CoinService by instance("coinService")

    init {
        app.routing {
           get<TickerRequest> { request ->
                call.respond(service.find(request.symbol, request.start.atStartOfDay(), request.end.atTime(23, 59, 59)))
           }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用[位置](http://ktor.io/features/locations.html)特性映射一个`get`操作，将一个类型安全数据类`TickerRequest`
绑定到请求操作:

```
@Location("/coins/{symbol}")
data class TickerRequest(val symbol: String, val start: LocalDate, val end: LocalDate ) 
```

Enter fullscreen mode Exit fullscreen mode

顺便说一下，关于自举的部分`application.install(DataConversion){`用于处理将`start`
和`end`参数上的查询字符串转换为`LocalDate`

现在修改你的 build.gradle `mainClassName`部分:

`mainClassName = "io.igx.kotlin.ApplicationKt"`

# 奔跑吧

是时候尝试一下了，只需运行`./gradlew build`并通过构建在`/build/libs/`文件夹上的 fat-jar 启动服务器:

`java -jar build/libs/boot2kotlin-0.0.1-all.jar`

# 最后的想法

就像所有的新事物一样，一开始会很奇怪，没有 Spring Boot 来为你配置一切会让人感到有些畏惧。但是之后奇怪的
就变得正常了，甚至简单了。几天后，我非常有信心，我有另一个堆栈来尝试和实验。手头有多个工具总是好的。