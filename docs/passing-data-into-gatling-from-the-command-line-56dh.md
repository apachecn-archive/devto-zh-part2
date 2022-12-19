# 从命令行传递数据到加特林

> 原文：<https://dev.to/david_j_eddy/passing-data-into-gatling-from-the-command-line-56dh>

几个月前，我在一篇基础综述中谈到了加特林。这一次我们将更深入地创建一个[加特林](https://gatling.io) (3.0)测试模拟。

## 基本设置

自从七月份写了上一篇关于加特林的文章后，3.0-RC4 版本已经发布了。据我所知，记录流量的过程没有任何改变。大多数更改似乎都是请求/响应引擎内部的，还有一些函数重命名。[点击这里查看官方变更日志。](https://gatling.io/category/new-release/)

为了让我们快速启动并运行，我创建了一个负载模拟文件，该文件将在本文的其余部分使用。文件内容如下。

```
import scala.concurrent.duration._

import io.gatling.core.Predef._
import io.gatling.http.Predef._
import io.gatling.jdbc.Predef._

class PassingDataIntoGatling extends Simulation {

    val target = System.getProperty("target")

    val httpProtocol = http
        .baseUrl("http://" + target)
        .inferHtmlResources()
        .acceptHeader("text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8")
        .acceptEncodingHeader("gzip, deflate")
        .acceptLanguageHeader("en-US,en;q=0.5")
        .doNotTrackHeader("1")
        .userAgentHeader("Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:63.0) Gecko/20100101 Firefox/63.0")

    val headers_0 = Map(
        "Pragma" -> "no-cache",
        "Upgrade-Insecure-Requests" -> "1")

    val scn = scenario("PassingDataIntoGatling")
        .exec(
            http("request_0").get("/").headers(headers_0)
        )

    setUp(
        scn.inject(
            constantUsersPerSec(10) during (5 seconds)
        ).protocols(httpProtocol)
    )
}
```

将该文件另存为**/path/to/Gatling/user-files/simulations/passing data intogatling . Scala**。这样加特林会自动找到模拟类；使得剩下的过程更加容易。

## 创建全局变量

让我们从最高层次的抽象开始。为了将数据传递给 Gatling，我们首先需要将数据提供给 JAVA。这是通过使用 JAVA_OPTS 全局变量(Galting 是一个 JAVA 程序，它使用 Scala 脚本来执行带有 HTML/JS 输出的 HTTP 请求。#abstraction)设置 JAVA_OPTS 的语法如下:

```
JAVA_OPTS="-DVARIABLE_NAME=VARIABLE_VALUE"
```

注意引号内的-D。这不是偶然的。这个逻辑创建了一个全局 JAVA 选项键/值对。在执行过程中，加特林可以访问它。

接下来，在 Gatling 模拟类中，我们需要访问全局变量。

## 为班级增加价值

要访问 JAVA_OPTS 变量值，命令是:

```
val variable_name = System.getProperty("VARIABLE_NAME")
```

那很容易！现在我们可以在类函数中的任何地方访问这个值。...

```
...
  val httpProtocol = http
     .baseUrl("http://" + target)
...
```

## 测试它

一切就绪。测试负载模拟执行的时间。

```
JAVA_OPTS="-Dtarget=google.com" ./gatling.sh -s PassingDataIntoGatling -nr
...
================================================================================
2018-11-08 18:31:03                                           6s elapsed
---- Requests ------------------------------------------------------------------
  Global                                                   (OK=200    KO=0     )
  request_0                                                (OK=50     KO=0     )
  request_0 Redirect 1                                     (OK=50     KO=0     )
  request_0 Redirect 2                                     (OK=50     KO=0     )
  googlelogo_color_272x92dp.png                            (OK=50     KO=0     )
---- PassingDataIntoGatling ----------------------------------------------------
[##########################################################################]100%
          waiting: 0      / active: 0      / done: 50    

```

这看起来不错。让我们更改目标值并确保。

```
JAVA_OPTS="-Dtarget=yahoo.com" ./gatling.sh -s PassingDataIntoGatling -nr
...
================================================================================
2018-11-08 18:29:50                                          10s elapsed
---- Requests ------------------------------------------------------------------
  Global                                                   (OK=3093   KO=0     )
  request_0                                                (OK=50     KO=0     )
  request_0 Redirect 1                                     (OK=50     KO=0     )
  request_0 Redirect 2                                     (OK=50     KO=0     )
...
---- PassingDataIntoGatling ----------------------------------------------------
[##########################################################################]100%
          waiting: 0      / active: 0      / done: 50    

```

是的，工作正常！很好。现在我们可以在加特林模拟的执行过程中指定变量值。

## 那是一个包裹

当深入发展时，能让你保持“流动”的每一秒都是值得努力的。有了这些知识，我们现在可以快速地对目标执行负载测试，而不会丢失上下文。

希望你喜欢这篇快速的文章，如果你有问题，评论，或者想谈论更多关于自动化过程，软件工程，或者 DevOps 概念，请在下面留下评论。