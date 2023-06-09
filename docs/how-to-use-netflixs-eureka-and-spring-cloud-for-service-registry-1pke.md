# 如何使用网飞的 Eureka 和 Spring Cloud 进行服务注册

> 原文：<https://dev.to/paigen11/how-to-use-netflixs-eureka-and-spring-cloud-for-service-registry-1pke>

[![Spring Eureka logo](img/7970d587aa0b4b09b2597f39c6ba2407.png "Spring Eureka logo")T2】](///static/fdcc8b77e660e739c655243d32170e9a/dba9a/spring-eureka-logo.png)

## 简介

**[微服务架构模式](https://microservices.io/patterns/microservices.html)** 的主要原则之一是，一组松散耦合的协作服务一起工作，形成一个内聚的整体应用。通过这种方式，它不是关于单个服务，而是关于确保服务之间的交互保持可靠和容错。

实际上，这意味着删除硬编码的信息，用动态更新的环境变量替换它，为每个服务建立单独的数据库，尽可能地删除服务之间的依赖关系(因此，如果需要，需要更多实例的服务可以在不影响其对应服务的情况下轻松扩展)，并通过增加每个服务的复杂性来降低管理整体的复杂性。

这听起来是一个不错的策略，但是跟踪整体的所有较小部分会给客户管理所有这些带来负担。

这让我想到了我今天博客的主题:网飞尤里卡服务注册中心——以及如何使用它和 Spring Cloud 来更有效地管理跟踪服务的复杂任务。

正如网飞尤里卡注册中心的 Github repo 所说:

> “Eureka 是一种基于 REST(表述性状态转移)的服务，主要用于 AWS 云中定位服务，以实现中间层服务器的负载平衡和故障转移。”
> 
> — [网飞:尤里卡一瞥，Github](https://github.com/Netflix/eureka/wiki/Eureka-at-a-glance)

听起来不错？很好。事不宜迟，让我们开始设置这个 Eureka 服务注册中心和几个服务，看看它的实际应用。

* * *

## 设置尤里卡服务器

设置一个基于 Spring 的 Eureka 服务器实际上非常简单。 [Spring.io](https://spring.io/) 本身有一个很棒的 **[在这里](https://spring.io/guides/gs/service-registration-and-discovery/)** ，它帮助我快速启动并运行了一个 Eureka 服务器和一个样本 Spring Boot 项目。

我不会一步一步来，因为你可以用我链接的教程来做，或者你可以在这里 下载我的 **[工作代码示例。但是我将强调服务器设置中包含的重要内容。](https://github.com/paigen11/eureka-services-example)**

> **注意:**在我的示例 repo 中，下面显示的所有代码片段都有链接到实际 GitHub 文件的文件名。请随意点击它们，查看完整的工作代码。

### [`build.gradle`](https://github.com/paigen11/eureka-services-example/blob/master/eureka-service/build.gradle) 文件

```
ext {
    springCloudVersion = 'Finchley.SR1'
}

dependencies {
    compile('org.springframework.cloud:spring-cloud-starter-netflix-eureka-server')
    testCompile('org.springframework.boot:spring-boot-starter-test')
}

dependencyManagement {
    imports {
        mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
    }
}

repositories {
    maven {
        url 'https://repo.spring.io/libs-milestone'
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在您的`build.gradle`文件中，您必须包含上面的依赖项:

*   **[`spring-cloud-starter-netflix-eureka-server`](https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-netflix-eureka-server)T4】**
*   还有 **[`spring-cloud-dependencies`](https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-dependencies)** 。

完成后，接下来是 resources 文件夹中的`application.yml`文件。

### [`application.yml`](https://github.com/paigen11/eureka-services-example/blob/464d42752919fc8ba554ee9cce8797a00c8a226e/eureka-service/build/jib-docker-context/resources/application.yml) 文件

```
spring:
    application:
        name: eureka-service

server:
    port: 8761

eureka:
    client:
        register-with-eureka: false
        fetch-registry: false
        serviceUrl:
            defaultZone: http://eureka-service:8761/eureka

logging:
    level:
        com:
            netflix:
                eureka: OFF
                discovery: OFF 
```

Enter fullscreen mode Exit fullscreen mode

这个文件也不需要太多。它需要一个指定的服务器端口，这样服务就不会在端口 8080 上自动启动，这将在本地运行时与我们的其他 Spring Boot 客户端服务冲突。

为了方便起见，还指定了几个配置。尤里卡客户端被指示不要在启动时注册*本身*:`eureka.client.register-with-eureka: false`，并被告知不要搜索其他要连接的注册节点，因为没有注册节点(至少在本地运行时没有):`eureka.client.fetch-registry: false`。

列出了一个默认的 URL，并关闭了 Eureka 和任何后续服务发现的详细日志记录。

好了，现在在主类路径上还有最后一步。

### [`EurekaServerApp.java`](https://github.com/paigen11/eureka-services-example/blob/464d42752919fc8ba554ee9cce8797a00c8a226e/eureka-service/src/main/java/eurekaServer/EurekaServerApplication.java) 文件

```
package eurekaServer;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@EnableEurekaServer
@SpringBootApplication
public class EurekaServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(EurekaServerApplication.class, args);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

主文件需要的唯一附加内容是 **`@EnableEurekaServer`** 注释，它告诉 Spring Boot 服务启用服务器。很简单。

好了，我们现在可以继续讨论服务了——这是事情开始变得更有趣的地方。

## 为尤里卡注册设置一个 Spring Boot Java 服务

再一次，Spring.io **[在这里](https://spring.io/guides/gs/service-registration-and-discovery/)** 做了很好的工作，建立了第一个 Spring Boot 服务器和客户端项目，所以我将再次强调您必须包含的内容。

```
dependencyManagement {
    imports {
        mavenBom 'org.springframework.cloud:spring-cloud-dependencies:Finchley.SR1'
    }
}

repositories {
    maven {
        url 'https://repo.spring.io/libs-milestone'
    }
}

dependencies {
    compile('org.springframework.cloud:spring-cloud-starter-netflix-eureka-client')
    compile('org.springframework.boot:spring-boot-starter-web')
    testCompile('org.springframework.boot:spring-boot-starter-test')
    testCompile('org.springframework.cloud:spring-cloud-starter-eurekaServer')
} 
```

Enter fullscreen mode Exit fullscreen mode

对于客户端服务的`build.gradle`文件，一定要包含 **[`spring-cloud-starter-netflix-eureka-client`](https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-netflix-eureka-client)** ， **[`spring-cloud-dependencies`](https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-dependencies)** 。

包含了 **[`spring-boot-starter-web`](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web)** 依赖项，因此可以在服务中创建一个 web 端点，它将向我们显示与 Eureka 服务器相关的服务信息。

### `bootstrap.yml` & `application.yml`文件

**[`bootstrap.yml`](https://github.com/paigen11/eureka-services-example/blob/master/eureka-client-java/bin/main/bootstrap.yml)**T5】

```
spring:
    application:
        name: a-java-service 
```

Enter fullscreen mode Exit fullscreen mode

同样，`boostrap.yml`和`application.yml`文件需要一些小的配置。上图是`bootstrap.yml`，它是 Spring 在`application.yml`文件之前获取的，所以这是我们设置服务名称的地方。

我为这个项目选择了最原始的`spring.application.name: a-java-service`。这就是尤里卡服务器将如何参考它前进。

**[`application.yml`](https://github.com/paigen11/eureka-services-example/blob/master/eureka-client-java/bin/main/application.yml)**T5】

```
server:
    port: 8091

eureka:
    client:
        serviceUrl:
            defaultZone: http://localhost:8761/eureka 
```

Enter fullscreen mode Exit fullscreen mode

然后,`application.yml`文件的配置类似于 Eureka 服务器的设置。Eureka 服务器的端口号和默认 URL。

**[`EurekaClientApplication.java`](https://github.com/paigen11/eureka-services-example/blob/master/eureka-client-java/src/main/java/eurekaClient/EurekaClientApplication.java)** 文件

```
package eurekaClient;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@EnableDiscoveryClient
@SpringBootApplication
public class EurekaClientApplication {

    public static void main(String[] args) {
        SpringApplication.run(EurekaClientApplication.class, args);
    }
}

@RestController
class ServiceInstanceRestController {

    @Autowired
    private DiscoveryClient discoveryClient;

    @RequestMapping("/service-instances/{applicationName}")
    public List<ServiceInstance> serviceInstancesByApplicationName(
            @PathVariable String applicationName) {
        return this.discoveryClient.getInstances(applicationName);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，服务在主类路径文件中用 **`@EnableDiscoveryClient`** 进行了注释，它告诉 Spring Boot 服务激活网飞 Eureka DiscoveryClient 实现，并向 Eureka 服务器注册自己的主机和端口。

REST 控制器上下面定义的 REST 端点可用于查看在本地 URL:[http://localhost:8091/service-instances/a-Java-service](http://localhost:8091/service-instances/a-java-service)的 Eureka 注册表中注册的所有服务实例。

服务的 JSON 信息如下所示:

[![JSON data from the Java microservice](img/bbd29e9614af0aa9d3ed5f8935d0685a.png "JSON data from the Java microservice")T2】](///static/034e76e225ebcaa5fb9d4f765115309c/f8836/a-java-service-json.png)

## 设置尤里卡注册的节点服务

现在，这是一个好的开始，但是为了更加真实，我决定也向 Eureka 服务器注册一个 Node.js 服务，这被证明更具挑战性。

幸运的是，有一个方便的小 NPM 包可以满足这样的需求，叫做 **[`eureka-js-client`](https://www.npmjs.com/package/eureka-js-client)** ，它被宣传为:

> 网飞 OSS 服务注册中心 Eureka 的客户端的 JavaScript 实现。

文档必须通读一遍，因为在 Eureka 中使用 Spring 实现时有一些特殊的问题，但我通过一些尝试和错误成功地让它工作了。

下面是启动并运行一个示例 Node.js 项目所需的内容。

**[`package.json`](https://github.com/paigen11/eureka-services-example/blob/master/eureka-client-node/package.json)**T5】

```
 "dependencies":  {  "eureka-js-client":  "^4.4.1",  "express":  "^4.16.3",  "nodemon":  "^1.18.4"  } 
```

Enter fullscreen mode Exit fullscreen mode

对于这个 Node.js 服务，我们只需要安装 **[`eureka-js-client`](https://www.npmjs.com/package/eureka-js-client)** ，我添加了 **[`express`](https://www.npmjs.com/package/express)** 和 **[`nodemon`](https://www.npmjs.com/package/express)** ，这样我就可以轻松地对应用程序进行一些 REST 调用，并在我对系统进行调整时让节点服务器自动重新加载。

**[`server.js`](https://github.com/paigen11/eureka-services-example/blob/master/eureka-client-node/server.js)**T5】

```
'use strict';

const express = require('express');
const Eureka = require('eureka-js-client').Eureka;

// Constants
const PORT = 3000;
const HOST = '0.0.0.0';
const app = express();

// example configuration
const client = new Eureka({
  // application instance information
  instance: {
    app: 'a-node-service',
    hostName: 'localhost',
    ipAddr: '127.0.0.1',
    statusPageUrl: 'http://localhost:3000',
    vipAddress: 'a-node-service',
    port: {
      $: PORT,
      '@enabled': 'true',
    },
    dataCenterInfo: {
      '@class': 'com.netflix.appinfo.InstanceInfo$DefaultDataCenterInfo',
      name: 'MyOwn',
    },
    registerWithEureka: true,
    fetchRegistry: true,
  },
  eureka: {
    // eureka server host / port
    host: 'localhost',
    port: 8761,
    servicePath: '/eureka/apps/',
  },
});

client.logger.level('debug');
client.start(error => {
  console.log(error || 'NodeJS Eureka Started!');

  // App
  app.get('/', (req, res) => {
    res.send('Hello from NodeJS Eureka Client\n');
    res.end();
  });
});  

app.listen(PORT, HOST);
console.log(`Running on http://${HOST}:${PORT}`); 
```

Enter fullscreen mode Exit fullscreen mode

为了方便，我只使用了一个`server.js`文件，因为这个项目一开始就很小。

重要的是`new Eureka()`的设定。对于 Spring Boot 项目，我们在`bootstrap.yml`和`application.yml`中设置应用程序配置，而对于 JavaScript 项目，这个配置在`server.js`文件中设置或者注入一个配置文件(对于更大的项目或者基于产品生命周期具有多个不同配置的项目)。

这里定义了应用名:`app: 'a-node-service'`，主机名，IP 地址，端口，数据中心信息等。被定义了。

直到`registerWithEureka`和`fetchRegistry`选项的每个参数都是必需的，否则服务器会崩溃，但是`vipAddress`和`dataCenterInfo`字段可以完全按照它们在`eureka-client-js` [文档](https://www.npmjs.com/package/eureka-js-client#add-eureka-client-to-a-node-application)中的写法添加。它们只是需要被填写。

最后但并非最不重要的一点是，一旦客户机服务被设置好，它就会用`client.start()`命令启动，如果启动失败，我会在控制台记录一条`error`消息，如果连接到 Eureka 注册中心成功，我会记录一条`‘Node.js Eureka Started’`消息。

## 运行尤里卡服务注册中心

现在让我们启动并运行这个服务注册中心。我在这里 得到了运行我的 **[示例项目的`README.md`中所有服务的全部细节，但这里也是。](https://github.com/paigen11/eureka-services-example)**

如果你有一个类似于 Sping.io 入门教程中描述的文件结构，我们将`cd`进入一个主项目文件夹中包含的每个服务。下面是我的样子供参考:

```
root/
├── eureka-client-java/ 
| ├── build.gradle 
| ├── src/ 
| | | ├── main/ 
| | | | ├── java/ 
| | | | ├── resources/ 
| | | | | ├── application.yml 
| | | | | ├── bootstrap.yml
├── eureka-client-node/ 
| ├── server.js
| ├── package.json
| ├── node-modules/
├── eureka-service/
| ├── build.gradle 
| ├── src/ 
| | | ├── main/ 
| | | | ├── java/ 
| | | | ├── resources/ 
| | | | | ├── application.yml 
```

Enter fullscreen mode Exit fullscreen mode

正如我所说的，将`cd`放入每个回购`eureka-client-java/`、`eureka-client-node/`和`eureka-service/`，对于两个 Spring Boot 项目，从命令行运行`gradle clean build`，然后运行`gradle bootRun`。

对于 Node.js 项目，从另一个终端窗口运行`npm start`。

给它一分钟，让所有的项目旋转起来，被注册服务找到，然后去 [http://localhost:8761](http://localhost:8761) ，这就是你应该看到的。

[![Spring Eureka Registry application](img/dde749ec90ea728d98dbe0e79e0723d6.png "Spring Eureka Registry application")T2】](///static/d39ff749c9fc87c85d8f5481e153c8cf/2cefc/spring-eureka-registry.png)

*如果您仔细查看“当前在 Eureka 注册的实例”部分，您会看到我们的应用程序。*

这是 Eureka 服务器主页，如果您查看页面下方的第二部分，您会看到“Instance currently registered with Eureka”，以及我们构建的两个注册了它的服务。

要验证这些服务是否真的在运行，你可以去[http://localhost:8091/service-instances/a-Java/service](http://localhost:8091/service-instances/a-java/service)和 [http://localhost:3000/](http://localhost:3000/) 查看每个服务的信息。

现在，让我们更进一步，因为拥有一个服务注册中心的全部目的是使在那里注册的客户更容易询问关于如何与其他可用服务连接和通信的问题:

> “每个服务都向服务注册中心注册自己，并告诉注册中心它的位置(主机、端口、节点名称)以及其他特定于服务的元数据——其他服务可以使用这些信息做出明智的决定。客户可以询问有关服务拓扑的问题(“是否有任何‘履行服务’可用，如果有，在哪里？”)和服务能力(“你能处理 X、Y、Z 吗？")."
> 
> — [Spring，通过 Spring Cloud 和网飞的 Eureka 进行微服务注册和发现](https://spring.io/blog/2015/01/20/microservice-registration-and-discovery-with-spring-cloud-and-netflix-s-eureka)

## 为服务设置额外的端点进行通信

我想采取的下一步是创建它，这样我就可以通过 Node.js 服务的一个端点获得 Java 服务提供的服务实例 JSON 数据——它只能通过与 Eureka 服务注册中心提供的信息进行通信来获得。

以下是要做的事情:

**[`server.js`](https://github.com/paigen11/eureka-services-example/blob/master/eureka-client-node/server.js)**T5】

```
var javaInstance = '';

client.logger.level('debug');
client.start(error => {
  console.log(error || 'NodeJS Eureka Started!');

  javaInstance = client.getInstancesByAppId('A-JAVA-SERVICE');
  // console.log(javaInstance);

  // App
  app.get('/', (req, res) => {
    res.send('Hello from NodeJS Eureka Client\n');
    res.end();
  });

  const javaUrl = `${javaInstance[0].hostName}:${
    javaInstance[0].port.$
  }/service-instances/${javaInstance[0].app}`;

  console.log(javaUrl);

  // get java service info endpoint
  app.get(`/serviceInfo/${javaUrl}`, (req, res) => {
    res.send(JSON.stringify(javaInstance), null, 2);
    res.end();
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

就在`server.js`文件中的`client.start()`命令之前，我定义了一个名为`javaInstance`的变量，然后使用`eureka-client-js`插件`getInstancesByAppId()`提供的方法调用注册为`a-java-service`的 Spring Boot 服务来获取其服务信息。

这样，我就可以重建由 Spring Boot REST 端点提供的 URL 信息，以获取该服务的信息。简而言之，我能够从 Eureka 服务器提供的 Java 实例中解析出所有必要的信息，以便通过 Node.js 端点到达它。

一旦获得了主机名、端口号和应用程序名，URL 就会变成这样:[http://localhost:3000/SERVICE info/192 . 168 . 1 . 18:8091/SERVICE-instances/A-JAVA-SERVICE](http://localhost:3000/serviceInfo/192.168.1.18:8091/service-instances/A-JAVA-SERVICE)。

如果 Node.js 和 Java 服务都在运行并向 Eureka 服务器注册，您会看到以下内容:

[![JSON for Spring Boot service](img/47dc91b4ccc26c27346429a7a9333c36.png "JSON for Spring Boot service")T2】](///static/27a9344e3220259fa3460c346407cf9d/2cefc/spring-boot-json.png)

*Spring Boot 服务数据由 Node.js 服务端点提供。*

瞧。上面的 JSON(格式不正确)是针对 a-java-service 的，通过 Node.js 服务上的端点进行访问。只需确保在 client.start()命令中对 Spring Boot 服务进行 URL 调用，否则您将无法访问从对 Eureka 服务器的 getInstancesByAppId()调用中提供的所有实例信息。

在这一点上，Eureka 正在做它被设计来做的事情:它注册服务并从一个服务向另一个服务提供信息，减去了开发人员过去必须负责记忆和手动实现的硬编码的复杂性。

* * *

## 结论

正如我在开始时所说的，根据微服务架构模式构建应用程序意味着管理单个服务更加复杂，但是应用程序作为一个整体具有更高的容错能力和可靠性。

像网飞的 Eureka server 这样的服务注册有助于管理这种额外的复杂性，当与 Spring 的云技术结合时，它变得非常容易使用。之后的下一步将是使用类似 Zuul 的东西来协助动态路由、实例之间的负载平衡、处理安全性等等。但那是另一篇博文。

过几周再来看看——我会写更多关于 JavaScript、React、IoT 或其他与 web 开发相关的东西。

如果你想确保你不会错过我写的一篇文章，在这里注册我的时事通讯:[https://paigeniedringhaus.substack.com](https://paigeniedringhaus.substack.com)

感谢您的阅读，我希望这证明是有帮助的，并让您更好地了解如何在 Spring Cloud 的帮助下使用网飞的 Eureka 服务器，并发现 Java 和 Node.js 服务。

* * *

## 参考文献&进一步资源

*   [微服务架构](https://microservices.io/patterns/microservices.html)
*   [尤里卡服务注册示例 Github repo](https://github.com/paigen11/eureka-services-example)
*   [网飞尤里卡 Github 回购](https://github.com/Netflix/eureka/wiki)
*   [Spring:服务注册和发现](https://spring.io/guides/gs/service-registration-and-discovery/)
*   [尤里卡客户端 JS](https://www.npmjs.com/package/eureka-js-client)
*   [Spring:通过 Spring Cloud 和网飞的 Eureka 进行微服务注册和发现](https://spring.io/blog/2015/01/20/microservice-registration-and-discovery-with-spring-cloud-and-netflix-s-eureka)