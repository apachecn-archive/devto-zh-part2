# 如何用 Spring Boot 在 Java 中构建 GraphQL 服务

> 原文：<https://dev.to/sambenskin/howto-build-graphql-services-in-java-with-spring-boot---part-1-38b2>

**注意:请注意这篇文章现在已经过时了，因为自从我在 2019 年写了这篇文章以来，技术已经发展了。**

让我们先快速浏览一下所涉及的技术。

## GraphQL

与 REST 相比，GraphQL 是块中的新手，但我认为改进是如此之大，以至于我正在移动我编写的任何新 API 来使用它。我见过很多这样的 API，它们返回了大量的数据，而用户需要的只是一组标识符，并在第二个端点上查询与这些标识符相关的其他数据，这突出了第二个问题。仅仅写这听起来很疯狂，我只是想对 API 进行一次调用，得到我需要的一切。

我不会详细解释为什么 GraphQL 更好，有很多文章可以让你自己做出判断。我要说的是，我决定进行转换有两个主要原因:在您的请求中轻松包含您想要接收的数据结构的能力；以及将多个 REST 请求组合成一个 GraphQL 查询的能力。两者都是对 REST 的巨大改进。当然，你可以尝试这样写你的 REST 端点，但是它们真的不再是 REST 了，GraphQL 就是这样工作的。

好了，现在已经解决了这个问题，让我们继续写一些代码。我们将使用 [Spring Boot](https://projects.spring.io/spring-boot) 在 Java 中构建一个简单的准系统 GraphQL API

## Spring Boot

我主要是 PHP 背景，最近才发现 Spring 框架的乐趣，尤其是 Spring Boot。它使得建立一个新项目变得极其容易；对如何配置和构造控制器、数据访问等的大量传统样板代码持有自己的观点，但当您想要按照自己的方式配置时，就会避开这些问题。在这个例子中，我们不需要编写任何控制器；只有我们的实体模型、类型和 GraphQL 模式。

## 先决条件

对于这个项目，我们将使用 Java 8，我尝试了 Java 10 和 9，但是 lombok 依赖关系有一个[问题，所以在本教程中必须退回到 8。修好后我会把它更新到 10。我们将使用 Spring Boot 2，它使用 Spring 框架的第 5 版，并为您设置好一切。为了简单起见，我们还将使用 Maven 构建框架来管理我们的 java 依赖项。我们还将使用优秀的 GraphQL-Java 库 spring boot starter 为我们提供 GraphQL 和 GraphQL 端点(稍后将详细介绍)。最后，我添加了 Lombok 项目，它允许您对类、方法、变量等进行注释，以提供样板功能。](https://github.com/rzwitserloot/lombok/issues/1572)

以下是我将使用的确切版本:

*   [Java 8 (1.8.0_172)](https://java.com)
*   [Spring Boot 2.0.2](https://projects.spring.io/spring-boot)
*   [GraphQL-Java Spring Boot 启动器 4.0.0](https://github.com/graphql-java/graphql-spring-boot)
*   [胃 3.5.2](https://maven.apache.org/)
*   [龙目岛项目 1.16.20](https://projectlombok.org/)

## 走吧！

首先，创建一个新文件夹，并在您选择的 IDE 中打开它。我用的是[微软 Visual Studio 代码](https://code.visualstudio.com/)。抱歉，Atom，它真的是最好的免费代码编辑器。

创建一个名为 pom.xml 的新文件，并将它放入:

```
<?xml version="1.0" encoding="UTF-8"?>
<project  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>uk.co.benskin</groupId>
    <artifactId>graphql_spring_boot_tutorial</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>graphql_spring_boot_tutorial</name>
    <description>Learn how to build a graphql spring boot based java service</description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.2.RELEASE</version>
        <relativePath/>
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
        </dependency>
        <dependency>
            <groupId>com.graphql-java</groupId>
            <artifactId>graphql-java-tools</artifactId>
            <version>4.3.0</version>
        </dependency>
        <dependency>
            <groupId>com.graphql-java</groupId>
            <artifactId>graphql-spring-boot-starter</artifactId>
            <version>4.0.0</version>
        </dependency>
        <dependency>
            <groupId>com.graphql-java</groupId>
            <artifactId>graphiql-spring-boot-starter</artifactId>
            <version>4.0.0</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.16.20</version>
        </dependency>
    </dependencies>
</project> 
```

Enter fullscreen mode Exit fullscreen mode

上面的文件在前半部分定义了我们的项目，所以将项目名称、描述等更改为您自己的项目细节。在后半部分，我们定义了六个依赖项:

*   Spring Boot 网络-通过网络协议提供支持我们端点的功能
*   Spring Boot 开发工具——对开发构建和调试有用
*   Java 工具——加载并驱动我们的 GraphQL 模式
*   graph QL-Java Spring boot starter for graph QL-在我们的 Spring 上下文中的/graphql 端点托管我们的模式
*   graph QL-Java Spring boot starter for graph QL-一个基于 web 的 UI，用于与/graphql 端点进行交互，并了解端点的模式
*   减少 java 代码中样板文件的 Lombok 项目

继续安装所有的依赖项

```
mvn install 
```

Enter fullscreen mode Exit fullscreen mode

在第一次安装依赖项的大量输出之后，您应该会看到一条消息，上面写着“构建成功”。

好了，现在我们已经准备好了开始的所有东西。

创建文件夹

```
src/main/java/uk/co/benskin/graphql_spring_boot_tutorial/ 
```

Enter fullscreen mode Exit fullscreen mode

在其中，创建一个文件

```
GraphQLSpringBootTutorialApplication.java 
```

Enter fullscreen mode Exit fullscreen mode

放以下内容

```
package uk.co.benskin.graphql_spring_boot_tutorial;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class GraphQLSpringBootTutorialApplication {

    public static void main(String[] args) {
        SpringApplication.run(GraphQLSpringBootTutorialApplication.class, args);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这加载了一个新的 Spring 应用程序上下文，它与我们的 GraphQL-Java starter 依赖项自动集成在一起。

现在，让我们尝试启动我们的应用程序，看看我们会得到什么

```
mvn spring-boot:run 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到很多信息输出，希望有几行内容

```
INFO 64612 --- [  restartedMain] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
INFO 64612 --- [  restartedMain] b.g.GraphQLSpringBootTutorialApplication : Started GraphQLSpringBootTutorialApplication in 4.886 seconds (JVM 
```

Enter fullscreen mode Exit fullscreen mode

现在打开浏览器，进入 [http://localhost:8080](http://localhost:8080)

您应该会看到这样的错误页面

```
Whitelabel Error Page
This application has no explicit mapping for /error, so you are seeing this as a fallback.

[DATE]
There was an unexpected error (type=Not Found, status=404).
No message available 
```

Enter fullscreen mode Exit fullscreen mode

这很好！服务器正在运行，但我们还没有告诉如何响应根“/”的请求，所以现在尝试[http://localhost:8080/graphiql](http://localhost:8080/graphiql)，您应该会看到一个很好的 UI，用于与您的(尚未构建的)端点进行交互。

## 构建 GraphQL 模式

现在进入有趣的部分，GraphQL 模式。我不会详细介绍 GraphQL 是如何工作的，请看看 GraphQL 教程。

创建此文件

```
src/main/resources/petshop.graphqls 
```

Enter fullscreen mode Exit fullscreen mode

并放入以下内容

```
type Query {
    pets: [Pet]
}

type Pet {
    id: Int
    type: Animal
    name: String
    age: Int
}

enum Animal {
    DOG
    CAT
    BADGER
    MAMMOTH
} 
```

Enter fullscreen mode Exit fullscreen mode

上面我们已经定义了主查询，它将返回“宠物”作为宠物类型的数组。宠物的类型是动物类型的枚举，在它下面定义。

## 构建 API

现在回到 java 代码。我们将创建几个文件夹来帮助我们组织代码。对于如何命名它们以及把它们放在哪里没有限制，但是我强烈建议你使用子文件夹来更好地组织你的代码，无数未来的开发者会感谢你的。

创建此文件夹

```
src/main/java/uk/co/benskin/graphql_spring_boot_tutorial/enums 
```

Enter fullscreen mode Exit fullscreen mode

并创建这个文件

```
src/main/java/uk/co/benskin/graphql_spring_boot_tutorial/enums/Animal.java 
```

Enter fullscreen mode Exit fullscreen mode

这是内容

```
package uk.co.benskin.graphql_spring_boot_tutorial.enums;

public enum Animal {
    DOG,
    CAT,
    BADGER,
    MAMMOTH
} 
```

Enter fullscreen mode Exit fullscreen mode

这定义了我们的枚举，现在在宠物实体模型上

创建此文件夹

```
src/main/java/uk/co/benskin/graphql_spring_boot_tutorial/entities 
```

Enter fullscreen mode Exit fullscreen mode

创建此文件

```
src/main/java/uk/co/benskin/graphql_spring_boot_tutorial/entities/Pet.java 
```

Enter fullscreen mode Exit fullscreen mode

这是内容

```
package uk.co.benskin.graphql_spring_boot_tutorial.entities;

import lombok.Data;
import uk.co.benskin.graphql_spring_boot_tutorial.enums.Animal;

@Data
public class Pet {
    private long id;

    private String name;

    private Animal type;

    private int age;
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个简单的 POJO，带有@Data 注释来处理样板文件的 getters、setters 和构造函数。

现在为 GraphQL 解析器创建目录

```
src/main/java/uk/co/benskin/graphql-spring-boot-tutorial/resolvers 
```

Enter fullscreen mode Exit fullscreen mode

并创建文件

```
src/main/java/uk/co/benskin/graphql_spring_boot_tutorial/resolvers/Query.java 
```

Enter fullscreen mode Exit fullscreen mode

用这个装满它

```
package uk.co.benskin.graphql_spring_boot_tutorial.resolvers;

import java.util.ArrayList;
import java.util.List;
import com.coxautodev.graphql.tools.GraphQLQueryResolver;
import org.springframework.stereotype.Component;
import uk.co.benskin.graphql_spring_boot_tutorial.entities.Pet;
import uk.co.benskin.graphql_spring_boot_tutorial.enums.Animal;

@Component
public class Query implements GraphQLQueryResolver {

    public List<Pet> pets() {
        List<Pet> pets = new ArrayList<>();

        Pet aPet = new Pet();
        aPet.setId(1l);
        aPet.setName("Bill");
        aPet.setAge(9);
        aPet.setType(Animal.MAMMOTH);

        pets.add(aPet);

        return pets;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

好了，这就是我们调用 GraphQL 端点来获取宠物列表(本例中是一个)所需的一切

通过停止 maven (ctrl/cmd + c)并使用 mvn spring-boot:run 再次启动它来重新启动您的构建

要查看它的运行情况，请访问[http://localhost:8080/graphi QL](http://localhost:8080/graphiql)，您将会看到一个不错的用户界面。同样，我不会详细讨论如何使用 GraphIQL 接口。

在 GraphIQL 标题下面的框中，历史框的右边，是您输入请求的地方。复制并粘贴下面的内容:

```
{
    pets {
        name
        age
        type
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后单击播放图标(在 GraphIQL 标题旁边)。您应该会看到如下响应:

```
{
    "data": {
        "pets": [
            {
                "name": "Bill",
                "age": 9,
                "type": "MAMMOTH"
            }
        ]
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

恭喜你！您刚刚用 Java 和 SpringBoot 编写了一个 GraphQL 服务。好了，除了那条记录之外，它不会返回任何东西，所以在[第 2 部分](https://dev.to/sambenskin/howto-integrate-a-mysql-database-into-your-java-spring-boot-graphql-service-26c)中，我们通过利用 Java 持久性 API (JPA)的 [Spring Data](https://projects.spring.io/spring-data/) 项目引入了数据库访问

非常感谢您阅读这篇文章！如果你喜欢它，请评论让我知道，或者如果你有任何改进的建议；并确保点击下面的心形/独角兽/书签按钮，我总是非常感谢:)