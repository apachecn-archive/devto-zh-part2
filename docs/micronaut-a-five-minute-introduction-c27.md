# Micronaut:五分钟的介绍。

> 原文：<https://dev.to/mkbaldwin/micronaut-a-five-minute-introduction-c27>

Micronaut 是一个新的开源 Java/JVM 框架，用于创建微服务应用。开发由 Grails 框架的创建者领导，并得到了[对象计算](https://objectcomputing.com/products/micronaut)的支持。该项目正积极地向其正式的 1.0 版本努力。

我过去曾在一些个人项目中使用过 Grails，目前主要从事 JVM 生态系统的专业工作。因此，当我看到关于 Micronaut 的公告时，我决定尝试一下，看看这是怎么回事。

Micronaut 的目标是提供快速的启动时间和较小的内存占用。这两者对于部署微服务都很重要。

## 入门

要开始构建应用程序，[用户指南](https://docs.micronaut.io/snapshot/guide/index.html#quickStart)建议安装 Micronaut CLI。完成安装后，您可以使用以下命令创建一个新项目。

```
mn create-app my-app-name 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，这会创建一个 Gradle 脚本来使用 Micronaut 构建 Java 应用程序。您也可以指定 maven 作为构建脚本，Java、Groovy 或 Kotlin 作为编程语言。对于我的例子，我选择使用 Groovy。当您查看生成的项目结构时，如果您以前使用过 Spring framework，会感觉非常熟悉。

要启动应用程序，只需运行:

```
./gradlew run 
```

Enter fullscreen mode Exit fullscreen mode

您还可以编译应用程序，并使用您的 IDE 直接运行`Application`类中的 main 方法。

## 控制器

很像在 Spring 框架中，控制器是使用`@Controller`注释以声明方式定义的。用`@Controller`或其他注释标注的类会被自动检测。与依赖运行时类路径扫描的其他框架(如 Spring)不同，这是在编译时完成的。

一个简单的控制器如下:

```
import io.micronaut.http.annotation.Controller
import io.micronaut.http.annotation.Get

@Controller("/api/todo")
class TodoController {
    @Get("/{id}")
    Todo getTodo(int id) {
      //Do something with the id from the URI
    }    
} 
```

Enter fullscreen mode Exit fullscreen mode

`@Get`注释被放在方法上，该方法将处理`@Controller`和`@Get`注释中组合 URL 的 GET 动作。对于其他 HTTP 方法(POST、PUT 等),还存在其他注释。).URL 支持路径变量，它由一个占位符(在本例中为`{id}`)和一个匹配的方法参数组成。从控制器方法返回的对象将自动转换成 JSON。

## 豆子

可以使用类上的注释来定义 Beans。与控制器一样，beans 将通过类路径扫描来找到和注册。有几种不同的注释(`@Singleton`、`@Prototype`等)。)来创建具有不同作用域的 beans。例如，我可以使用 Singleton 注释创建一个服务。

```
import javax.inject.Singleton

@Singleton
class TodoService {
  //Provide some implementation
} 
```

Enter fullscreen mode Exit fullscreen mode

## 依赖注入

使用 JSR-330 `@Inject`注释提供依赖注入。与其他框架不同，Micronaut 在编译时执行依赖注入。这避免了使用反射/代理，有助于加快启动时间。

`@Inject`注释可用于通过构造函数注入、字段注入和方法参数注入依赖关系。

扩展前面的控制器示例，我们可以注入如下服务:

```
import io.micronaut.http.annotation.Controller
import io.micronaut.http.annotation.Get
import javax.inject.Inject

@Controller("/api/todo")
class TodoController {
    TodoService todoService

    @Inject
    TodoController(TodoService todoService) {
      this.todoService = todoService
    }

    @Get("/{id}")
    Todo getTodo(int id) {
      //Do something with the id from the URI
    }    
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

这个快速的概述仅仅触及了 Micronaut 中已经可用的功能的表面。[用户指南](https://docs.micronaut.io/snapshot/guide/index.html)内容全面，为所有功能提供了大量有用的信息。从 Spring Framework 来到 Micronaut 的开发者会觉得非常熟悉。

考虑到这个框架还没有正式发布 1.0 版本，它看起来已经非常强大了。我期待看到它如何继续发展壮大。

 <small>**更新 2018-07-30** :感谢 [Jim Kleeh](https://twitter.com/Schlogen) 在 Twitter 上指出注释
不是通过类路径扫描找到的，而是在编译时发生的。</small>