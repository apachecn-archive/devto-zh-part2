# Java 企业 101:用 Spring Boot 构建 REST 服务器

> 原文：<https://dev.to/martinhaeusler/java-enterprise-101-building-a-rest-server-with-spring-boot-m7k>

继 Java 企业 101 的第一部分之后的[，这一次我们将在](https://dev.to/martinhaeusler/java-enterprise-101-3djl) [Spring](//spring.io) 的帮助下实现 JEE 堆栈。

你需要做的是:

*   [JDK 10](http://www.oracle.com/technetwork/java/javase/downloads/jdk10-downloads-4416644.html) (或更高)的工作装置
*   有效的互联网连接
*   或者，您最喜欢的编辑器或 IDE

# 设置项目

我们开始...嗯，开始的时候。准确的说是在 [start.spring.io](//start.spring.io) 。这个 URL 将把你带到**Spring Initializr**——一个方便的小网站，在那里你可以混合和匹配库和包来适应你的项目。在我们的例子中，我们希望拥有 **Web** 、 **JPA** 和 **H2** 包:

[![Spring Initializr Setup](../Images/83e15d63f12551fac60219713a405571.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fcdK8ld9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4ws08d21qq4qpbcasawy.png)

我还将项目名称从“com.example”更改为“org.example”，并将项目类型从 **Maven** 更改为 **Gradle** 。您也可以很好地使用 Maven，但是在本指南中，我将使用 Gradle。一旦你点击“生成项目”按钮，你将得到一个*。zip 文件。将其解压缩到您选择的目录中；这将成为我们的项目目录。在里面，你会发现:

*   **build.gradle** :这个文件包含您的构建配置。
*   **grad Lew**/**grad Lew . bat**:grad le Wrapper。永远用这些，永远不要“gradle”本身。
*   **settings.gradle** :基本上就是你的 gradle 模块结构。我们不会碰这个，至少现在不会。
*   **。gitignore** :排除二进制文件和生成文件的非常基本的配置。
*   src :你的代码的源目录。遵循 maven 项目结构。

让我们看看生成的 **build.gradle** 脚本，好吗？

```
buildscript {
    ext {
        springBootVersion = '2.0.2.RELEASE'
    }
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
    }
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

group = 'org.example'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = 1.8

repositories {
    mavenCentral()
}

dependencies {
    compile('org.springframework.boot:spring-boot-starter-data-jpa')
    compile('org.springframework.boot:spring-boot-starter-web')
    runtime('com.h2database:h2')
    testCompile('org.springframework.boot:spring-boot-starter-test')
} 
```

Enter fullscreen mode Exit fullscreen mode

就构建脚本而言，这实际上是非常可读的。它首先说它使用`spring-boot-gradle-plugin`和 [Maven Central](https://mvnrepository.com/repos/central) 库来获取它的依赖项。并不奇怪，是吗？

在下一节中，我们有一个应用于我们项目的 gradle 插件列表。你可以随意用你的 IDE 插件替换`eclipse`插件(例如，IntelliJ 用户的`idea`)。如果你坚持使用编辑器和命令行，你根本不需要这个。

在我的版本中，我将`sourceCompatibility`改为`1.10`，这样我就可以使用 Java 10 中的`var`关键字。构建脚本还提到了我们项目的 maven 坐标。做一个好公民，永远坚持[语义版本](https://semver.org/) (SemVer)！在最后一节中，我们看到了一个**依赖项**的列表，既用于*编译*，也用于*测试*我们的项目。

> **/！\重要:**
> 为了使这个构建脚本能够在 Java 9 和更高版本(模块化 Java 平台库)中正常工作，您还需要添加以下依赖项:
> 
> `compile('javax.xml.bind:jaxb-api')`
> 
> 我们实际上不会使用 JAXB，但是如果您缺少这个依赖项，您会得到一个`NoClassDefFoundError`。希望在不久的将来，Spring Boot 团队会将它添加到他们的入门包中。

**Spring Boot 启动器**包是“元包”,它包含了针对常见任务的自以为是的库选择。例如:

*   `spring-boot-starter-web`包含一个作为应用程序容器的(嵌入式)`Tomcat`和用于 JSON 处理的`Jackson`
*   `spring-bot-stater-data-jpa`包含作为 JPA 提供者的`Hibernate`
*   `spring-boot-starter-test`包含`JUnit`

那很好，但是如果你不同意 Spring Boot 团队的选择呢？别担心，他们会掩护你的。如果 Spring Boot 检测到另一个库的存在，比如 TestNG，这个库将自动优先于默认库。在许多情况下，您甚至不必手动配置任何东西来实现这一点；只需在 gradle 中添加依赖项，然后重新构建项目。如果您想要一些 Spring 无法识别的更奇特的替代方法，您将不得不做更多的工作，但这将在另一篇文章中讨论。现在，让我们利用我们从盒子里拿出来的东西。

# 开始工作了！

首先，您应该验证您的设置是否按预期工作。最好的方法是打开一个命令提示符(是的，甚至 windows `cmd`也可以...)并导航到您的项目文件夹(`build.gradle`所在的位置)。运行以下命令:

```
gradlew build 
```

Enter fullscreen mode Exit fullscreen mode

在 windows `cmd`下，你可能需要使用`gradlew.bat`来代替。无论如何，这将:

*   从 maven central 下载所有必需的依赖项
*   编译您的源文件
*   执行您所有的测试

“但是我们没有测试！”你说呢？嗯，Spring Initializr 总是包含一个特定的测试用例，乍一看可能有点奇怪，但实际上很有意义。打开文件`DemoApplicationTests.java`(如果你调用了你的应用程序而不是`Demo`，相应地替换它)。

```
@RunWith(SpringRunner.class)
@SpringBootTest
public class DemoApplicationTests {

    @Test
    public void contextLoads() {
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

这个测试正如其名称所暗示的那样:它断言 Spring 上下文正确加载。如果这个基本测试失败，这意味着您的 Spring / Spring Boot 设置有一个严重的问题，需要在继续之前解决。我们现在应该可以接受我们的设置了。

Spring Initializr 为您生成的另一个类是`DemoApplication.java`类:

```
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是您的主类，是服务器端应用程序的入口点。

> **/！\重要:**
> 这个类所在的**包**被 Spring Boot 认为是**根包**。确保你的源代码的所有**或者驻留在根包中，或者驻留在它的一个(直接或间接)子包中，否则 Spring Boot 将不能找到你的类。**

# 我们的第一个春季休息终点

准备好写一些实际的代码了吗？创建一个 Java 文件，并将它放在您的根包(或子包)中。我把我的放在**org . example . demo . rest**:

```
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloWorldRestController {

    @RequestMapping(method = RequestMethod.GET, path = "/api/hello-world")
    @ResponseBody
    public String sayHello(){
        return "Hello World!";
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

给你。这就是用 Spring Boot 构建 REST 端点所需要的全部内容。注释告诉 Spring Boot 如何处理这个类(在本系列教程的后面，我们还会看到其他例子)。方法`sayHello`(名称一点也不重要，只要是有效的 Java 标识符，您可以随意命名)实现了一个 REST 端点。`@RequestMapping`向 Spring 提供信息，将哪些传入请求路由到这个端点。在本例中，我们将其绑定到资源路径`/api/hello-world`和请求方法`GET`。通过`@ResponseBody`，我们告诉 Spring，这个方法产生的结果应该作为 HTML 响应的主体返回给客户端。

# 开机

现在是真正精彩的部分。再次进入您的命令行界面，并导航到您的项目文件夹(如果您还没有这样做)。再次运行`gradlew build`命令。成功后，导航到 **build/libs** 子目录。在那里，你会找到一个单身的**。jar** 文件。像平常一样用`java`运行这个文件:

```
java -jar demo-0.0.1-SNAPSHOT.jar 
```

Enter fullscreen mode Exit fullscreen mode

这将为您启动应用服务器。等等，什么？但是我们甚至还没有安装 Tomcat！有趣的是:你不需要。虽然您现在可以以“传统”方式将 java 企业应用程序作为一个 **war** 文件来部署，但是您也可以将它作为一个常规的 Java 应用程序来运行，因为应用程序容器已经**嵌入到您的应用程序中了**。启动完成后，打开网络浏览器并导航至:

```
http://localhost:8080/api/hello-world 
```

Enter fullscreen mode Exit fullscreen mode

你应该看看我们的“你好，世界！”显示消息。

# 测试

如果我们不编写适当的测试，我们在 2018 年就不会是一个很好的开发者，不是吗？幸运的是，Spring 让我们测试 REST 端点变得相对容易。但是，需要进行一些设置。为了简单起见，我将扩展我们现有的测试类(并在下面的清单中省略现有的测试用例)。下面是我们需要的设置代码:

```
package org.example.demo;

import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.web.context.WebApplicationContext;

import static org.junit.Assert.assertEquals;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@RunWith(SpringRunner.class)
@SpringBootTest
public class DemoApplicationTests {

    @Autowired
    private WebApplicationContext context;

    private MockMvc mvc;

    @Before
    public void setup(){
        this.mvc = MockMvcBuilders
                .webAppContextSetup(context)
                .build();
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

这有助于我们设置 [MockMVC](https://docs.spring.io/spring-security/site/docs/current/reference/html/test-mockmvc.html) 助手。这个职业比它最初看起来的要多得多**；我们将在另一篇教程中探讨这个类为什么对测试 Spring REST 端点如此有用。现在，就把它看作是制定 REST 请求的好帮手吧。请注意，上面的设置代码可以移动到测试基类；这是永远不会改变的，也没有必要在每次考试课上都重复。让我们更详细地看一下代码。**

 **`@Autowired`是 Spring 使用的[依赖注入](https://docs.spring.io/spring/docs/3.0.x/spring-framework-reference/html/beans.html)注释，非常类似于 [JSR 330](https://www.jcp.org/en/jsr/detail?id=330) 实现使用的`@Inject`(例如 [Google Guice](https://github.com/google/guice) )。这样，我们基本上告诉 Spring 将单个`WebApplicationContext`实例传递给我们的测试。然后，我们使用 JUnit 的`@Before`方法，通过我们的 web 上下文来设置我们的`MockMvc`实例。这就是`MockMvc`实例如何“找到”您的端点。

现在，让我们为我们的 REST 端点编写一个实际的测试:

```
@Test
public void testHelloWorldEndpoint() throws Exception {
    var response = this.mvc.perform(
            get("/api/hello-world")
    ).andExpect(status().isOk()).andReturn().getResponse().getContentAsString();
    assertEquals("Hello World!", response);
} 
```

Enter fullscreen mode Exit fullscreen mode

这个[内部 DSL](https://en.wikipedia.org/wiki/Domain-specific_language) 允许我们`perform`请求`get`，我们`expect`HTTP`status`代码为`ok`，我们`return`将`response` `content`作为一个字符串。这篇[读起来像散文](http://wiki.c2.com/?ReadsLikeProse)。编译器检查的散文，将几十行断言减少到一行。如果这不美，我不知道什么是美。

运行这个测试，看着它通过！您可以在命令行中使用
来完成

```
gradlew test 
```

Enter fullscreen mode Exit fullscreen mode

...(这将运行*所有*测试)或者直接在您的 IDE 中运行。

# 关闭思绪

这已经变成了一篇相当长的文章——比我预想的要长得多。然而，如果我们考虑我们实际写了多少行实际代码(大约 10 行？)考虑到我们在功能方面取得的成就，很难说 Java 是“如此冗长的语言”。在下一篇文章中，我们还为用数据模型和数据库连接扩展我们的服务器打下了基础。我们实际上只是触及了表面。抛开简洁性不谈，Spring Boot 和 JEE 建筑的真正优势尚不明显。请暂时容忍我；)**