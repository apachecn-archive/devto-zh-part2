# 为什么 Spring 云配置服务器对一个好的 CI/CD 管道至关重要(第 1 部分)

> 原文：<https://dev.to/paigen11/why-a-spring-cloud-config-server-is-crucial-to-a-good-cicd-pipeline-pt-1-2ib3>

[![Laptop screen filled with minified source code](../Images/e0f9c455d6f91c363d39db967fada59b.png "Laptop screen filled with minified source code")T2】](///static/24d70e51d7ba55ddbed9ad6d61d1d908/eea4a/screen-of-code.jpg)

## 简介

在我开始开发大型企业级软件之前，我并没有完全理解集成测试、自动化构建过程和共享库等东西的价值。

对于我在[编码训练营](https://www.paigeniedringhaus.com/blog/how-i-went-from-a-digital-marketer-to-a-software-engineer-in-4-months)期间构建的小应用程序来说，我很容易做像硬编码 URL 这样的事情，这样我的前端应用程序就可以与后端应用程序及其数据库对话。

我只有两个环境:我的笔记本电脑上的本地开发环境和我的 AWS 生产环境，我在那里托管我的项目组合，这是一个相当简单的，尽管完全是手动的过程(有很好的尝试和错误)来连接拼图的各个部分。

让我的软件工程工作快进几个月，维护和改进一个由不少于 13 个微服务支持的应用程序。不，我没开玩笑。是的，所有这 13 个要素对于这个应用程序的运行都是至关重要的。

我有没有提到，在我的团队的[测试驱动开发(TDD)](https://en.wikipedia.org/wiki/Test-driven_development) 敏捷过程中，在一个新特性投入生产之前，我们还有 4 个不同的开发环境？是的:一个本地开发空间，一个质量保证空间，一个 Q1 空间，一个验收空间，最后是我们的生产空间。

因此，请思考一下:当这些服务在开发过程中走向生产时，必须手动更新它们中的每一个是否有意义？当然不会。

您需要一种集中的、易于自动化的方式来更新这些环境变量。这就是云配置服务器可以大放异彩的地方。

让您快速了解一下，云配置服务器旨在:

> 为分布式系统中的外部化配置提供服务器和客户端支持。通过配置服务器，您可以集中管理所有环境中应用程序的外部属性。”
> 
> [-春云配置](https://cloud.spring.io/spring-cloud-config/)

基本上，配置服务器允许你在外部存储你的应用需要在所有环境中运行的变量，而不管它们的生命周期，并在一个集中的地方更新它们。

[![Spring Cloud logo](../Images/cd74362bbe98e6e7b421724d99d4e193.png "Spring Cloud logo")T2】](///static/4b53473c4b91ca751900865efaa74475/f93b5/spring-cloud.jpg)

*看哪，春云。*

我接触的第一个配置服务器是 [Spring Cloud Config](https://cloud.spring.io/spring-cloud-config/) 服务器，因为我的团队负责一个由许多 Java Spring Boot 后端微服务和一个 JavaScript Node.js 前端微服务组成的全栈应用。

* * *

## 设置您的配置服务器

设置配置服务器实际上非常简单。

首先，你可以访问 [Spring Initializr 站点](http://start.spring.io/)，将`Config Server`作为一个依赖项添加到项目中，然后点击浏览器中的“生成项目”按钮。

> 我还要补充一点，至少在我写这篇文章的时候，Spring Cloud Config Server 与 Spring Boot 版本 2 不兼容(我很艰难地发现了这一点)，所以尽可能选择最高的 Spring Boot 版本 1 快照。

[![Spring Initializr site](../Images/6a2d4007b0b4106ce182c26c810ffb24.png "Spring Initializr site")T2】](///static/44b116839628c34184589e88e7a330fa/c679a/spring-initializr.png)

这就是让 Spring Cloud 配置服务器运行所需要的一切:配置服务器依赖。请注意，Spring Boot 的版本是 1.5.14，而不是 2.0.2，这是它自动默认的最新版本。

一旦你下载了项目并在你选择的 IDE 中打开它(我的是 [IntelliJ](https://www.jetbrains.com/idea/) ，你可以直接进入主应用程序文件，添加`@EnableConfigServer`和`@SpringBootApplication` Spring Boot 注释，你就差不多准备好了。

下面是我实际的主方法文件，真的，这就是它所需要的。

```
package com.myExampleConfigServer; 
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.config.server.EnableConfigServer; 

@EnableConfigServer
@SpringBootApplication

public class MyExampleConfigServerApplication {  
  public static void main(String[] args) {
    SpringApplication.run(MyExampleConfigServerApplication.class, args); 
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

您需要配置的另一个文件是您的`resources/`文件夹中的`application.yml`文件。这个文件将是您设置云配置服务器对 GitHub 的访问的地方(以及它将用来提供应用程序配置变量的 GitHub 文件)。

如你所见，这里有一个 GitHub URL(我一会儿会回来)、用户名和密码的位置。

由于我们的应用托管在 [Pivotal Cloud Foundry](https://pivotal.io/platform) 上，我们使用所谓的 [VCAP 服务](https://docs.cloudfoundry.org/devguide/deploy-apps/environment-variable.html)(它代表 VMWare 云应用平台)。这是一种服务的奇特说法，其中存储了环境变量，只有有权访问 PCF 中该空间的人(和应用程序)才能访问。这让我们可以将敏感信息(如服务帐户和密码)放在公共 GitHub repo 之外的某个地方，其中凭证可用于配置服务器，但不可用于未经授权的任何人。

这是我的`application.yml`。配置好这两个文件和托管服务器的云平台后，您就可以开始使用配置服务器了。

**申请. yml**

```
server:  
  port:
    8888

spring:  
  application:    
    name: myexample-config-server  

cloud:    
  config:      
    server:        
      git:          
        uri: ${vcap.services.config-service.credentials.url}
        username: ${vcap.services.config-service.credentials.user}          
        password: ${vcap.services.config-service.credentials.token} 
```

Enter fullscreen mode Exit fullscreen mode

如果你想看配置服务器应用的例子，我在 GitHub 中放了一个启动项目[。](https://github.com/paigen11/spring-cloud-config-server-example/tree/master)

好了，现在你已经有了配置服务器。很好，现在是时候设置您的配置服务器属性了——配置服务器将实际访问这些文件来为您的项目提取环境变量。

## 设置您的配置服务器属性

这部分是轻而易举的。在 GitHub 中创建一个完全空的新 repo，然后使用以下命名约定创建一个新文件:

```
[application name]-[life cycle profile].yml

ex. my-app-to-config-QA.yml 
```

Enter fullscreen mode Exit fullscreen mode

在这个 YAML 文件中，您将能够为您的应用程序添加配置属性。以下是您可能会在 YAML 中包含的一些示例信息。

**我的应用到配置 QA.yml**

```
configurations:
  featureToggleFlag: true
  my-custom-flag: false
  sampleCronJob: "0  0  1  *  *  *"
  sampleUrl: http://google.com 
```

Enter fullscreen mode Exit fullscreen mode

将该文件提交给 GitHub repo，复制该 repo URL，并将其放在配置服务器的`application.yml`中的云配置服务器 URI 中。现在服务器知道在 GitHub 的什么地方可以找到您想要使用的配置文件。

你可以在 GitHub 中看到一个配置属性报告[的例子。](https://github.com/paigen11/spring-cloud-config-properties-sample)

## 将 Java Spring Boot 应用程序连接到配置服务器

使用配置服务器和您希望服务器提供给项目的属性，您需要项目能够在 Spring Boot 服务启动时获取这些属性。

同样，让这个工作起来并没有太多的开销。在需要这些配置服务器值的应用程序的`build.gradle`文件中，您将需要以下依赖项:

**build . gradle**T2】

```
compile group: 'org.springframework.cloud', name: 'spring-cloud-starter-config', version:'1.3.3.RELEASE'
compile group: 'org.springframework.boot', name: 'spring-boot-configuration-processor', version: "${springBootVersion}" 
```

Enter fullscreen mode Exit fullscreen mode

然后，您将创建一个`bootstrap.yml`文件，它将与您的`application.yml`文件共存于您的`src/main/resources/`文件夹中，并且它将包含指向配置服务器所在位置的信息。它看起来会像这样:

**自举。yml**

```
spring:
  application:
    name: ##APPLICATION NAME GOES HERE##
  cloud:
    config:
      uri: https://myexample-config-server.non-prod.com
---
spring:
  profiles: production
  cloud:
    config:
      uri: https://myexample-config-server.prod.com 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您将转到任何实际需要配置属性的文件，并添加注释`@Configuration`和`@ConfigurationProperties(prefix=”configurations")`(或者您在配置属性 YAML 中命名的任何配置属性)。

这里有一个例子:

**XyzProperties.java**T2】

```
@Configuration
@ConfigurationProperties(prefix = "configurations")
public class ConfigProperties {

} 
```

Enter fullscreen mode Exit fullscreen mode

如果您注意的话，这些属性会以`configurations`为前缀。要将这些属性绑定到 ConfigProperties 类，首先需要将一个`"prefix"`添加到`@ConfigurationProperties`注释中，如下所示:

要绑定`“featureToggleFlag”`属性，添加以下成员变量。以及 getter 和 setter。或者使用 [Lombok](https://projectlombok.org/features/constructor) 的`@NoArgsConstructor`并跳过 getters 和 setters 这是你的选择。

**XyzProperties.java**T2】

```
@Configuration
@ConfigurationProperties(prefix = "configurations")
public class ConfigProperties {
   private boolean featureToggleFlag;

   //Add Getters and Setters here if desired...

} 
```

Enter fullscreen mode Exit fullscreen mode

如果您将成员变量名称与配置属性中的实际属性名称相匹配，Spring 会自动将其绑定到成员变量。到目前为止一切顺利。

在启动服务之前，您必须做的最后一件事是确保将`SPRING_PROFILES_ACTIVE`设置为正确的配置属性环境。对于 QA 环境，当前值为`“QA”`，对于 Q1 环境为`“Q1”`，对于生产环境为`“prod”`，依此类推。请注意，这些值是区分大小写的，所以无论您在配置属性文件中如何命名它们，它们都必须与 IntelliJ 的启动脚本设置中的`"Active Profiles"`输入完全相同。

在 IntelliJ 中，这可以通过在设置要运行的 Spring Boot 项目时单击“编辑配置”属性，并在配置模式中途向`"Active Profiles"`输入添加正确的值来完成。

[![Active profile setting inside of IntelliJ IDE](../Images/f06298aea0080d8499156cbb8f1ddb28.png "Active profile setting inside of IntelliJ IDE")T2】](///static/6ae9167b7e83f687a89594e2da8007c4/0d390/intellij-active-profiles.png)

*在 IntelliJ 运行配置中，将这一行添加到“Active Profiles”输入中。*

仅此而已。

现在，当您启动 Spring Boot 服务时，您应该会看到日志中设置了`Spring Profile`，为了确保安全，您可能还会放入一些`System.out.println`消息，让您知道它是否成功到达配置服务器并获得了所需的属性。

太棒了，我们已经构建了一个配置服务器，我们已经将环境变量从您的 Spring Boot 项目移到了一个集中的位置，因此可以快速进行更改，并且我们已经配置了一个 Java 服务来从配置服务器中提取这些变量。干得好。

* * *

## 结论和第二部分:配置服务器和 Node.js

但是，如何用 JavaScript / Node.js 项目做同样的事情呢？你能使用同样的 Spring Boot 配置服务器吗？还是必须设置一个单独的基于节点的配置服务器？

你可以使用现有的，在我的下一篇文章中，我将向你展示如何设置它，以及如何在 JavaScript 项目中使用它来启用和禁用特性切换，以便更快地部署特性。

过几周再来看看——我会写更多关于 JavaScript、React、IoT 或其他与 web 开发相关的东西。

如果你想确保你不会错过我写的一篇文章，在这里注册我的时事通讯:[https://paigeniedringhaus.substack.com](https://paigeniedringhaus.substack.com)

感谢阅读！

* * *

## 进一步引用&资源

*   [春云配置初始化](https://cloud.spring.io/spring-cloud-config)
*   [云配置服务器报告示例](https://github.com/paigen11/spring-cloud-config-server-example)
*   [示例云配置属性报告](https://github.com/paigen11/spring-cloud-config-properties-sample)