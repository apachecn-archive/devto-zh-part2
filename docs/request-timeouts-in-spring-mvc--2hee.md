# 在 Spring MVC 中请求超时

> 原文：<https://dev.to/brightdevs/request-timeouts-in-spring-mvc--2hee>

上次我们回顾了如何配置 HTTP 客户端超时。这次让我们关注 HTTP 请求的另一面，即服务器。当我们编写 Spring MVC 应用程序时，几乎总是会涉及到线程池。线程池的配置会根据特定的 servlet 容器(Tomcat、Undertow、Jetty)而变化，所以我们必须注意细微的差别。然而，即使不是全部，大多数线程池都使用固定最大大小的线程池。正如我们已经知道的，资源池可能会耗尽。特别是，如果我们不努力控制超时，线程池更有可能耗尽。

## 参与 Spring MVC 请求处理的线程

典型的 servlet 容器将使用一个或多个线程池来处理请求。具体来说，其中一个线程池用于执行请求处理的 Spring MVC 部分。让我们称这个线程池为请求工作线程池。请求工作线程池将具有默认的最大大小:

*   雄猫:`server.tomcat.max-threads`控制 [`maxThreads`](https://tomcat.apache.org/tomcat-8.5-doc/config/http.html) ，默认值为 200
*   潜流:`server.undertow.worker-threads`控制 [`WORKER_TASK_CORE_THREADS`](http://undertow.io/undertow-docs/undertow-docs-1.2.0/listeners.html) 默认为 [`availableProcessors() * 8`](https://github.com/undertow-io/undertow/blob/b6a87a4b4a467b297363c46747c344faaee15ded/core/src/main/java/io/undertow/Undertow.java#L419)
*   Jetty:目前没有可用的 Spring 配置属性。人们可以通过代码和特定于 jetty 的配置来自定义 Jetty 线程池。默认的最大工作线程数是 200。

[![Thread pool](img/e21ff5f010bc98e3334b20110c7d3c7c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lu_i65dZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j1s29xy82vr9nbvdiapy.jpg)

## 请求处理线程池为空时会发生什么？

一旦请求处理线程池为空，servlet 容器通常会对请求进行排队。该队列由请求处理线程池处理。将请求排队会消耗服务器内存和套接字，因此通常会有一个限制，超过该限制后，新的传入请求将被立即拒绝。

*   当所有可能的请求处理线程都在使用时，传入连接请求的最大队列长度。[默认值为 100](https://tomcat.apache.org/tomcat-8.5-doc/config/http.html) 。
*   Undertow:据我所知，默认情况下，请求将被排队，唯一的限制是系统容量。有[请求限制处理程序](http://undertow.io/undertow-docs/undertow-docs-1.2.0/#built-in-handlers)可用，它允许配置最大并发请求和最大队列大小。
*   Jetty:默认情况下，将使用无限队列对请求进行排队。您可以通过[进行配置，如文档](https://wiki.eclipse.org/Jetty/Howto/High_Load)所示:

```
<Configure id="Server" class="org.eclipse.jetty.server.Server">
    <Set name="ThreadPool">
      <New class="org.eclipse.jetty.util.thread.QueuedThreadPool">
        <!-- specify a bounded queue -->
        <Arg>
           <New class="java.util.concurrent.ArrayBlockingQueue">
              <Arg type="int">6000</Arg>
           </New>
      </Arg>
        <Set name="minThreads">10</Set>
        <Set name="maxThreads">200</Set>
        <Set name="detailedDump">false</Set>
      </New>
    </Set>
</Configure> 
```

Enter fullscreen mode Exit fullscreen mode

在大多数 commons 场景中，对请求进行排队是处理临时负载高峰所必需的。例如，如果您的应用程序每秒可以处理 100 个请求，并且如果您允许它从一分钟的过度高负载中恢复，那么您可以将队列容量设置为 60*100=6000。

## 请求处理线程池与超时有什么关系？

让我们假设线程池(最大)大小为 100，平均来说处理一个请求需要 1 秒钟。在这样的服务器中，我们可以每秒处理 100 个请求。任何超过 rps 限制的请求都将被排队。现在，假设我们有一个单一类型的请求，由于某种原因，该请求的处理时间比平时长得多，例如，由于一些相关的服务问题，需要 120 秒。当这样的请求被处理时，它将首先阻塞后续的请求处理线程，直到它们都*忙着等待*。根据队列大小和系统容量的限制，我们的服务器将很快开始拒绝**所有新的请求**。值得注意的是，在达到线程池容量后，*慢速*请求也将被放入队列。

缓解问题并加速系统恢复的方法之一是**应用超时**。当特定请求超时时，理想情况下应该不会发生什么:

*   应该通知客户关于错误的信息(根据用例的不同， [503、504 或 408](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes) )
*   该请求应该从处理队列中删除
*   处理请求的线程应该被中断

让我们回顾一下有哪些选择。

*   Tomcat 有[阻塞线程检测阀](http://tomcat.apache.org/tomcat-8.5-doc/config/valve.html#Stuck_Thread_Detection_Valve) : >这个阀允许检测需要长时间处理的请求，这可能表明正在处理它的线程被阻塞了。此外，它可以选择性地中断这些线程，以尝试并解除对它们的阻塞。该阀有两种配置选项:
*   `threshold`:线程被认为阻塞的最短持续时间(秒)。默认值为 600 秒。
*   `interruptThreadThreshold`:以秒为单位的最短持续时间，在此时间之后，被阻塞的线程应该被中断，以试图“释放”它。

*AFAIK 该阀仅适用于由线程池开始处理的请求。*

*   Undertow 和 Jetty 不允许直接设置请求超时。它们都有*空闲*连接检测，并且可以相应地超时。不幸的是，由于 HTTP/2 多路复用，超时选项可能不适合对单个请求进行超时。

*   在 Spring MVC 中，没有办法配置超时，除非你使用[异步方法](https://spring.io/guides/gs/async-method/)。使用异步方法，可以使用`spring.mvc.async.request-timeout=`来设置异步请求处理超时之前的时间量(毫秒)。然而，使用带有 Spring MVC 的异步 Servlet 需要改变控制器方法的返回类型。

## 没有标准的请求超时配置

只有几个选项可用于设置包含超时请求处理。这部分是由于历史原因。在定义异步 servlet 之前，Servlet 容器规范不考虑超时。另一个原因是没有办法[安全地*停止*一个框架可以使用的线程](https://docs.oracle.com/javase/1.5.0/docs/guide/misc/threadPrimitiveDeprecation.html)。应用程序代码需要协作来安全地终止请求处理执行。在下一篇文章中，我们将展示如何给 Spring MVC 应用程序添加请求超时。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Piotr Mionskowski，软件工程师@光明发明
[邮箱](//piotr.mionskowski@brightinventions.pl) [Stackoverflow](https://stackoverflow.com/users/155213/miensol) [个人博客](https://miensol.pl/)