# SpringBoot2 阻挡型网与反应型网

> 原文：<https://dev.to/bufferings/springboot2-blocking-web-vs-reactive-web-46jn>

你好，我是米茨。这是 dev.to 上的第一个帖子。很高兴见到你:)

# 流行语进我的工具箱

正如你们很多人听说过的微服务、区块链等，有各种各样的技术术语，它们经常出现和消失。因此，重要的是我们不要不经检查就相信它们，而是亲眼看看它们是如何工作的，然后把它们放进我们的工具箱。这就是我这次尝试使用部分反应式编程的原因。

反应式编程的好处之一是我们可以有效地使用机器资源。例如，在 web 应用程序的情况下，服务器可以用较少的线程处理比阻塞式应用程序更多的请求。

[![Reactive](../Images/67436c0c7032399858d60b8c602eb8ce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KfKv_JOy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rwxl8gcedmhv5o3lbte0.png)

# SpringBoot2 反应网

本月初发布的 SpringBoot2 引入了“反应式网络”功能。所以我试着比较了一下“弹簧网”和“弹簧反应网”。“Spring Web”是基于 Servlet 的传统模块化风格，而“Spring Reactive Web”是一种反应式编程的新风格。

[![Spring Web Reactive](../Images/46f291a192a5ed230845d421182c92d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VHl9cexS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sh7w6d8vc5cj05yhjw2h.png) 
(图片来自 https://spring.io/)

# 演示应用架构

我发现了一篇比较 SpringBoot1 和 SpringBoot2 的很棒的文章:
[原始性能数据——Spring Boot 2 web flux vs Spring Boot 1](https://dzone.com/articles/raw-performance-numbers-spring-boot-2-webflux-vs-s)。
我做了几乎相同的事情来比较 SpringBoot2 阻挡型 Web 和反应型 Web。架构是这样的:

[![Architecture](../Images/7af731cc8d9a44187bb8bc0739b563ec.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MQWWoevb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4uhi2r4b5wvdkbx0byp9.png)

有 3 个应用程序:

*   延迟服务
*   阻止应用程序
*   反应型 app

源代码在这里:
[https://github.com/bufferings/webflux-demo-201803](https://github.com/bufferings/webflux-demo-201803)

## 延时服务

延迟服务模拟一个外部 API，有一些延迟。我们可以用路径参数:
来设置延迟

```
 @GetMapping("/{delayMillis}")
  public Mono<String> get(@PathVariable int delayMillis) {
    return Mono.just("OK")
        .delayElement(Duration.ofMillis(delayMillis));
  } 
```

Enter fullscreen mode Exit fullscreen mode

## 屏蔽-app

blocking-app 是一个简单的 Spring Web app，以阻塞方式调用 delay-service，并以阻塞方式返回:

```
 private static final String DELAY_SERVICE_URL = "http://localhost:8080";

  private final RestTemplate client;

  public BlockingApp(RestTemplateBuilder builder) {
    client = builder.rootUri(DELAY_SERVICE_URL).build();
  }

  @GetMapping("/{delayMillis}")
  public String get(@PathVariable String delayMillis) {
    String result = client.getForObject("/" + delayMillis, String.class);
    return "Blocking:" + result;
  } 
```

Enter fullscreen mode Exit fullscreen mode

## 无功-app

reactive-app 是一个 Spring Web reactive app，用一个 Reactive 客户端调用 delay-service 并返回`Mono` :

```
 private static final String DELAY_SERVICE_URL = "http://localhost:8080";

  private final WebClient client = WebClient.create(DELAY_SERVICE_URL);

  @GetMapping("/{delayMillis}")
  public Mono<String> get(@PathVariable String delayMillis) {
    return client.get()
        .uri("/" + delayMillis)
        .retrieve()
        .bodyToMono(String.class)
        .map(s -> "Reactive:" + s);
  } 
```

Enter fullscreen mode Exit fullscreen mode

# 来看看性能吧！

启动延时服务:

```
./gradlew -p apps/delay-service clean bootRun

curl -w "\n%{time_total}s\n" localhost:8080/1000
# returns "OK" after 1000ms

curl -w "\n%{time_total}s\n" localhost:8080/2000
# returns "OK" after 2000ms 
```

Enter fullscreen mode Exit fullscreen mode

开始屏蔽-app:

```
./gradlew -p apps/blocking-app clean bootRun

curl -w "\n%{time_total}s\n" localhost:8081/2000
# returns "Blocking:OK" after 2000ms 
```

Enter fullscreen mode Exit fullscreen mode

启动反应式应用:

```
./gradlew -p apps/reactive-app clean bootRun

curl -w "\n%{time_total}s\n" localhost:8082/2000
# returns "Reactive:OK" after 2000ms 
```

Enter fullscreen mode Exit fullscreen mode

现在，这三个应用都在运行。

## 负载测试场景

我使用加特林([https://gatling.io/](https://gatling.io/))进行负载测试。

场景是这样的“1000 个用户以 1 到 2 秒的间隔调用 API 30 次”。我想将延迟服务的延迟设置为 300 毫秒。

```
 val myScenario = scenario("Webflux Demo").exec(
    repeat(30) {
      exec(
        http("request_1").get(targetUrl)
      ).pause(1 second, 2 seconds)
    }
  )
  setUp(myScenario.inject(rampUsers(simUsers).over(30 seconds))) 
```

Enter fullscreen mode Exit fullscreen mode

## 加特林去堵-app

```
./gradlew -p apps/load-test -DTARGET_URL=http://localhost:8081/300 \
    -DSIM_USERS=1000 gatlingRun 
```

Enter fullscreen mode Exit fullscreen mode

使用 VisualVM，我们可以看到工作线程数增加到 200，这是 Tomcat 的默认 maxThread 值。
[![blocking-app threads](../Images/c4837613dc864b3d370283176f8986f1.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--6JagAYE7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xit5gmoeavba9z7xg2wm.png)

## 加特林对反应式-app

```
./gradlew -p apps/load-test -DTARGET_URL=http://localhost:8082/300 \
    -DSIM_USERS=1000 gatlingRun 
```

Enter fullscreen mode Exit fullscreen mode

它只使用 4 个线程来处理请求。
[![reactive-app threads](../Images/934373e4a99b8484bdbd9b5440102a33.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--pFFu-zuq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/frrteunq1w2a4gxq3ctb.png)

# 负载测试结果

这是结果。

正如你所看到的，对于 1000 个用户来说，这两个应用都工作得很好，响应时间大约为 300 毫秒，正如我们所预期的那样。但是对于 3000 & 6000 用户来说，95%的屏蔽应用变得更糟。

另一方面，reactive-app 保持了大约 400 毫秒的良好响应速度，它在我的笔记本电脑(Core i7-7500U 2.7GHz/16GB RAM)上显示了大约 2000rps。

有趣的结果！

## 屏蔽-app

拥有 1000 名用户:
[![blocking1000](../Images/b50a0a8cbd9ebc21fabcc59d45330d60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FvHHJ9X8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a8j4tphcuyub9hjtt8qo.png)

拥有 3000 用户:
[![blocking3000](../Images/0924ee036187c115fe4bf253d7db4fa6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s_qDgds1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/70qqxhqy4s2jfqofsiyf.png)

拥有 6000 用户:
[![blocking6000](../Images/39b08eb12fbc49521c99ba6ddda7de47.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l_Q0AK2K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1tbmokeg2uni81kja5si.png)

## 无功-app

拥有 1000 名用户:
[![reactive1000](../Images/f9cf32aa86411929e527f07f1fe9eb73.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qdY1MfZY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4mu6puyip19yt5gfufo4.png)

拥有 3000 用户:
[![reactive3000](../Images/c32f2daf6bf69fb19aea63db146c6e58.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TeGeIyo5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6twi83fhanii7lxzwh9m.png)

拥有 6000 用户:
[![reactive6000](../Images/9338ad176cccc39a951467a6332732ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TFacsFpu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4wnzhlvc2gqwufm83sab.png)

# 还剩什么

## 增加 Tomcat maxThreads

由于我决定使用默认配置，Tomcat 线程数达到了 200，这是 maxThreads 的默认值。可能调整 maxThread 会提高阻塞应用程序的性能。

## 分隔 env

由于我在我的笔记本电脑上尝试了这个演示，所有的应用程序在资源使用方面都相互影响。因此，将每个应用程序分离到几台机器上可能会显示不同的结果。

# 结论

总之，我们可以知道 SpringBoot2 Reactive Web 如何高效地处理请求。但是我推荐你自己去查。特别是反应式 Web 风格编程，我认为需要 Java 工程师在某种程度上改变他们的思维模式。

无论如何，这太有趣了，我觉得 SpringBoot2 反应式网络已经在我的工具箱里了。谢谢大家！