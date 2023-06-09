# 春云循序渐进(第三部分)

> 原文：<https://dev.to/piczmar_0/spring-cloud-step-by-step-part-3-4jeg>

这是之前博文的延续:

*   [春云步步为营(上)](https://dev.to/piczmar_0/spring-cloud-step-by-step-part-1-1670)
*   [春云步步为营(下)](https://dev.to/piczmar_0/spring-cloud-step-by-step-part-2-12o2)

这部分的源代码可以在 GitHub 的 [spring-cloud-3](https://github.com/piczmar/spring-cloud-rental/tree/spring-cloud-3) 分支中找到。

假设我们不想在应用程序属性中硬编码配置服务的 URL，因为它可能会随环境的不同而变化。当然，我们可以在每个环境中将这个属性作为 JVM 环境变量传递，但是我们需要在应用程序启动脚本中考虑它。
相反，我们可以使用服务注册中心(例如[网飞尤里卡](https://github.com/Netflix/eureka))，并且只对服务使用友好的名称，而不是它的 URL。

在这里，我们将更新配置服务和定价服务应用程序，以便通过 Spring Cloud Discovery 使用服务发现机制。

## 配置尤里卡服务器

类似地，就像我们配置配置服务一样，我们将配置 Eureka 服务器。
我们可以在 [http://start.spring.io](http://start.spring.io) 再次生成带有 Spring Initializr 的配置服务 app。

我们只需要从依赖项中选择“Eureka Server”和“Web”。

[![Generate project](img/66b1c2f00117db8678f886488fb80a1c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aCxae3mV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/50iw2gzd2hj36jqxd9yh.png)

这将在 pom.xml:
中生成带有 maven 依赖项的 app

```
 <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency> 
```

然后我们需要在主类上启用带注释的发现服务器:

```
@EnableEurekaServer 
```

并将 Eureka 服务器配置为不尝试连接到自身——添加内容为
的`resources/application.yalm`

```
server:
  port: ${PORT:8761}

eureka:
  client:
    registerWithEureka: false
    fetchRegistry: false
    server:
      waitTimeInMsWhenSyncEmpty: 0 
```

## 配置尤里卡客户端

在定价服务和配置服务中，我们需要添加一个依赖关系

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency> 
```

并添加带有注释的 Eureka 客户端自动配置

```
@EnableEurekaClient 
```

请注意，在网上的一些例子中，如果您在类路径中有 Eureka 依赖项，您可能会发现注释`@EnableDiscoveryClient`基本上做了同样的事情，但它有细微的区别。
发现服务在 Spring Cloud 中有多种实现，比如 Eureka，Consul，Zookeeper。

`@EnableDiscoveryClient`位于 [spring-cloud-commons](https://github.com/spring-cloud/spring-cloud-commons) 中，从类路径中选择实现。

`@EnableEurekaClient`住在[斯普林-云-网飞](https://github.com/spring-cloud/spring-cloud-netflix/)，只为尤里卡工作。

一旦我们在主类上有了注释，当我们启动应用程序时，它将尝试在`http://localhost:8761`(默认值`eureka.client.serviceUrl.defaultZone`)联系 Eureka 服务器

现在启动 Eureka 服务和配置服务，并转到 Eureka 控制台的 [http://localhost:8761](http://localhost:8761)

您会在注册表中看到一个未知的服务。

[![Eureka dashboard](img/986597a05f57aa1ff5bab9769efe26ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--O6dfQEuv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nwp3uhxyptwmebqj3rxq.png)

这是因为我们没有指定配置服务名称。

在配置服务中创建文件`resources/bootstrap.properties`并添加属性:

```
spring.application.name=config-service 
```

这是一个类似于我们在定价服务`bootstrap.properties`中添加的属性，这就是 Spring Cloud 允许通过名称识别服务的方式。

我们实际上可以在`application.properties`中添加这个属性，在这种情况下，它仍然可以工作，但有些情况下，在应用程序上下文开始初始化
之前，名称是必需的，而`bootstrap.properties`是在应用程序启动时第一个加载的属性，因此将`spring.application.name`放在那里更安全。

现在，当我们重新启动配置服务，它将被命名在尤里卡仪表板列表。

类似地，我们可以用 Eureka 配置定价服务。

我们需要添加与在配置服务中添加的依赖项相同的依赖项，然后在主类上添加`@EnableEurekaClient`注释。

定价服务之前已经配置了`spring.application.name`属性。

剩下的就是用 Eureka discovery 替换硬编码的 URL 来配置服务。

在我们添加 Eureka 之前，我们在定价服务中有这样的配置`bootstrap.yaml` :

```
spring:
  application:
    name: pricing-service
  cloud:
    config:
      uri: http://localhost:8888 
```

尽管我们在 Eureka 中添加了配置服务注册，但默认情况下，它不会影响其他使用配置服务的服务。
我们必须通过在定价服务`bootstrap.yaml`中设置属性`spring.cloud.config.discovery.enabled=true`来明确告知我们想要使用 Eureka 客户端库来发现配置服务。

[Spring 文档](https://cloud.spring.io/spring-cloud-static/spring-cloud.html#_spring_cloud_config_client)声明:
**使用该选项的代价是启动时定位服务注册的额外网络往返。好处是配置服务可以改变它的坐标，只要发现服务是一个固定点。**

Eureka 客户端将尝试在 Eureka 注册表中查找名为`configserver`的服务，但不会找到，因为我们通过在配置服务`bootstrap.yaml`中设置属性`spring.application.name=config-service`来不同地命名配置服务。

然后我们必须在定价服务器`bootstrap.properties`中明确定义配置服务的名称。最终的配置将如下所示:

```
spring:
  application:
    name: pricing-service
  cloud:
    config:
      discovery:
        enabled: true
        service-id: config-service 
```

当我们开始为服务定价时，我们应该会看到如下日志:

```
c.c.c.ConfigServicePropertySourceLocator : Fetching config from server at: http://192.168.43.107:8888/ 
```

这意味着 Eureka 客户端成功解析了配置服务的位置。

它是由 Spring 自动处理的，但是我们还没有看到如何从 java 代码中手动调用其他服务(例如使用 RestTemplate)。

我将在下一篇文章中解释如何做。