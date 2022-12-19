# Spring 中的反应式编程入门

> 原文：<https://dev.to/oktadev/get-started-with-reactive-programming-in-spring-1o6a>

反应式编程允许您构建对高负载有弹性的系统。处理大量流量不是问题，因为服务器是非阻塞的，不会阻塞客户端进程来等待响应。客户端不能直接观察服务器上发生的执行，也不能与之同步。当一个 API 在努力处理请求时，它应该以一种合理的方式做出响应。它不应该无法工作或以不受控制的方式丢弃消息。它应该向上游组件传达它正处于压力之下，并让它们减少负载。这被称为“背压”，是反应式编程的一个重要方面。

我和另一位 Java 冠军、Spring 开发者倡导者 Josh Long 以及 Pivotal 的所有优秀人士合作撰写了这篇文章。我是 Spring 的老用户了，Josh 是第一个让我看到 Spring Boot 的人，那是很多个月前在比利时的 Devoxx。我们成为好朋友已经有一段时间了，我们对 Java、开发人员和构建令人敬畏的应用程序有着同样的热情。

## 无功编程，或者，I/O，I/O，该下班了我们走…

反应式编程是一种编写包含异步 I/O 的软件的方法。异步 I/O 是一个预示着软件大变化的小想法。这个想法很简单:通过回收那些在等待 I/O 活动时处于空闲状态的资源来减少低效的资源利用。异步 I/O 颠倒了 I/O 处理的正常设计:客户机被通知新数据，而不是请求新数据；这使得客户端可以在等待新通知的同时做其他事情。让我们看一个比较异步 I/O 和同步 I/O 的例子。

让我们构建一个简单的程序，从一个源(特别是一个`java.io.File`引用)读取数据。首先，一个使用 trusty 'ol `java.io.InputStream`实现的实现:

例 1。从文件中同步读取数据

```
package com.example.io;

import lombok.extern.log4j.Log4j2;
import org.springframework.util.FileCopyUtils;

import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.util.function.Consumer;

@Log4j2
class Synchronous implements Reader {

    @Override
    public void read(File file, Consumer<BytesPayload> consumer) throws IOException {
        try (FileInputStream in = new FileInputStream(file)) { // (1)
            byte[] data = new byte[FileCopyUtils.BUFFER_SIZE];
            int res;
            while ((res = in.read(data, 0, data.length)) != -1) { // (2)
                    consumer.accept(BytesPayload.from(data, res)); // (3)
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  使用常规的`java.io.File`来获取文件
2.  *将*结果从源中取出，一次一行…
3.  我写了这段代码来接受一个在有新数据时被调用的`Consumer<BytesPayload>`

很简单，是吧？运行这个命令，您会在日志输出中看到，在每一行的左侧，所有的活动都发生在一个线程上。

我们正在*从数据源中提取*字节(在本例中，是一个`java.io.InputStream`子类，`java.io.FileInputStream`)。这个例子有什么问题？嗯，可能没什么！在这种情况下，我们使用一个指向本地文件系统数据的`InputStream`。如果文件在那里，并且硬盘在工作，那么这段代码将按我们预期的那样工作。

如果我们从网络套接字读取数据，而不是从`File`读取数据，并使用不同的`InputStream`实现，会怎么样？没什么好担心的！如果网络速度无限快，至少没什么好担心的。并且如果这个节点和另一个节点之间的网络链接从未失败。如果这些事情是真的，那就没什么好担心的了！这段代码会工作得很好。

如果网络速度慢或中断，会发生什么情况？在这种情况下，这意味着`in.read(…​)`操作返回所需的时间会延长。的确，可能一去不复返了！如果我们试图用正在读取数据的线程做其他事情，这就会成为一个问题。当然，我们可以旋转另一个线程，从那个线程中读取数据。我们可以在一定程度上保持这一点，但最终，我们会遇到一个极限，即增加线程不支持我们的扩展目标。超过我们机器上的内核数量，我们就不会有真正的并发。我们被困住了！在这种情况下，如果不增加线程，我们就无法处理更多的 I/O 和读取，而且我们通过更多线程进行扩展的能力最终会受到限制。

在这个例子中，大部分的工作都是在阅读中——其他地方都没有发生什么。我们被 *_I/O 绑定*。让我们看看异步解决方案如何帮助我们减轻线程的独占。

例 2。从文件中异步读取数据

```
package com.example.io;

import lombok.extern.log4j.Log4j2;
import org.springframework.util.FileCopyUtils;

import java.io.File;
import java.io.IOException;
import java.nio.ByteBuffer;
import java.nio.channels.AsynchronousFileChannel;
import java.nio.channels.CompletionHandler;
import java.nio.file.Path;
import java.nio.file.StandardOpenOption;
import java.util.Collections;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.function.Consumer;

@Log4j2
class Asynchronous implements Reader, CompletionHandler<Integer, ByteBuffer> {

    private int bytesRead;
    private long position;
    private AsynchronousFileChannel fileChannel;
    private Consumer<BytesPayload> consumer;
    private final ExecutorService executorService = Executors.newFixedThreadPool(10);

    public void read(File file, Consumer<BytesPayload> c) throws IOException {
        this.consumer = c;
        Path path = file.toPath(); // (1)
        this.fileChannel = AsynchronousFileChannel.open(path,
            Collections.singleton(StandardOpenOption.READ), this.executorService); // (2)
        ByteBuffer buffer = ByteBuffer.allocate(FileCopyUtils.BUFFER_SIZE);
        this.fileChannel.read(buffer, position, buffer, this); // (3)
        while (this.bytesRead > 0) {
                this.position = this.position + this.bytesRead;
                this.fileChannel.read(buffer, this.position, buffer, this);
        }
    }

    @Override
    public void completed(Integer result, ByteBuffer buffer) {
        // (4)
        this.bytesRead = result;

        if (this.bytesRead < 0)
            return;

        buffer.flip();

        byte[] data = new byte[buffer.limit()];
        buffer.get(data);

        // (5)
        consumer.accept(BytesPayload.from(data, data.length));

        buffer.clear();

        this.position = this.position + this.bytesRead;
        this.fileChannel.read(buffer, this.position, buffer, this);
    }

    @Override
    public void failed(Throwable exc, ByteBuffer attachment) {
        log.error(exc);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  这一次，我们将`java.io.File`改编成 Java NIO `java.nio.file.Path`
2.  当我们创建`Channel`时，我们指定了一个`java.util.concurrent.ExecutorService`，当有数据可用时，它将被用来调用我们的`CompletionHandler`
3.  开始读取，传入对一个`CompletionHandler<Integer, ByteBuffer>` ( `this`)的引用
4.  在回调中，我们将字节从一个`ByteBuffer`读入一个`byte[]`容器
5.  正如在`Synchronous`示例中一样，`byte[]`数据被传递给消费者

首先:这段代码的 *waaaay* 更加复杂！这里有一大堆事情要做，看起来可能会让人不知所措，但是请允许我说一下……这段代码从 Java NIO `Channel`读取数据，并在回调处理程序中的一个单独线程上异步处理这些数据。开始读取的线程没有被独占。我们在调用了`.read(..)`之后几乎立即返回，当最终有数据可用时，我们的回调被调用，并且在不同的线程上。如果在`.read()`调用之间有延迟，那么我们可以继续前进，用我们的线程做其他事情。从第一个字节到最后一个字节，异步读取的持续时间最多与同步读取的持续时间一样短。可能会稍微长一点。但是，由于这种复杂性，我们可以更有效地使用我们的线程。我们可以处理更多的工作，在一个有限的线程池中复用 I/O。

我在一家云计算公司工作。如果您通过购买更多应用程序实例来解决您的横向扩展问题，我们会很高兴！当然，我在这里有点开玩笑。异步 I/O*确实让事情变得有点复杂，但希望这个例子突出了反应式代码的最终好处:如果我们的工作是 I/O 受限的，我们可以在相同的硬件*上使用异步 I/O 来处理更多的请求，做更多的工作。如果它是 CPU 受限的(例如:斐波那契、比特币挖掘或密码学)，那么反应式编程不会给我们带来任何东西。**

 *现在，我们大多数人在日常工作中并不使用`Channel` *或* `InputStream`实现！他们从更高层次的抽象角度思考问题。数组之类的东西，或者更有可能是`java.util.Collection`层次结构。一个`java.util.Collection`非常好地映射到一个`InputStream`:它们都假设你能够处理所有的数据，几乎是即时的。你希望能够尽快读完大多数`InputStreams`的内容。当您移动到更大数量的数据时，集合类型开始变得有点笨拙；当你在处理一些潜在的无限的事情时，比如 websockets，或者服务器发送的事件，会发生什么呢？当记录之间有延迟时会发生什么？一个记录现在到达，而另一个记录要等一分钟或一小时才能到达，比如在聊天时，或者当网络出现故障时？

我们需要一种更好的方式来描述这些数据。我们正在描述一些异步的东西——一些最终会发生的事情。这可能看起来很适合一个`Future<T>`或者一个`CompletableFuture<T>`，但是这仅仅描述了*一个*最终的事情。不是一整串潜在的无限事物。Java 并没有真正提供一个合适的比喻来描述这种数据。`Iterator`和 Java 8 `Stream`类型都可以是无界的，但它们都是以拉为中心的；您要求下一条记录，而不是让类型回调您的代码。一种假设是，如果它们支持基于推送的处理，让您可以用线程做更多的事情，那么 API 也会公开线程和调度控制。`Iterator`实现没有提到线程和 Java 8 流*所有*共享同一个 fork-join 池。

如果`Iterator`和`Stream`支持基于推送的处理，那么我们会遇到另一个问题，这个问题只会在 I/O 环境中出现:我们需要某种方式来*推回*！作为异步生产的数据的消费者，我们不知道什么时候或者有多少数据可能在管道中。我们不知道下一次回调会产生一个字节还是一个 if 太字节！

当您从`InputStream`中提取数据时，您读取的数据是您准备处理的数据，不会更多。在上面的例子中，我们读入一个固定且已知长度的`byte[]`缓冲区。在一个异步的世界里，我们需要某种方式来告诉生产者我们准备处理多少数据。

没错。我们*肯定*遗漏了一些东西。

## 比喻为失踪的人解决

我们想要的是能够很好地映射到异步 I/O 的东西，并且在分布式系统中支持这种推回机制，或者说*流控制*。在反应式编程中，客户端发出它能处理多少工作的信号的能力被称为*背压*。

有很多项目——vert . x、Akka Streams 和 rx Java——都支持反应式编程。Spring 团队有一个项目叫做[反应堆](http://projectreactor.io)。这些不同的方法有足够多的共同点，被提取为事实上的标准，即[反应流倡议](http://www.reactive-streams.org)。反应流计划定义了四种类型:

`Publisher<T>`是最终可能到来的价值的生产者。一个`Publisher<T>`产生类型为`T`的值给一个`Subscriber<T>`。

例 3。反应流`Publisher<T>`。

```
package org.reactivestreams;

public interface Publisher<T> {

    void subscribe(Subscriber<? super T> s);
} 
```

Enter fullscreen mode Exit fullscreen mode

`Subscriber`订阅了一个`Publisher<T>`，通过它的`onNext(T)`方法接收关于任何类型为`T`的新值的通知。如果有任何错误，就调用它的`onError(Throwable)`方法。当处理正常完成时，调用订户的`onComplete`方法。

例 4。反应流`Subscriber<T>`。

```
package org.reactivestreams;

public interface Subscriber<T> {

    public void onSubscribe(Subscription s);

    public void onNext(T t);

    public void onError(Throwable t);

    public void onComplete();
} 
```

Enter fullscreen mode Exit fullscreen mode

当一个`Subscriber`第一次连接到一个`Publisher`时，它在`Subscriber#onSubscribe`方法中被赋予一个`Subscription`。`Subscription`可以说是整个规范中最重要的部分:它支持背压。`Subscriber`使用`Subscription#request`方法请求更多数据，或使用`Subscription#cancel`方法停止处理。

例 5。反应流`Subscription<T>`。

```
package org.reactivestreams;

public interface Subscription {

    public void request(long n);

    public void cancel();
} 
```

Enter fullscreen mode Exit fullscreen mode

Reactive Streams 规范为*提供了一种更有用的*，尽管这是显而易见的:一个`Processor<A,B>`是一个扩展了`Subscriber<A>`和`Publisher<B>`的简单接口。

例 6。反应流`Processor<T>`。

```
package org.reactivestreams;

public interface Processor<T, R> extends Subscriber<T>, Publisher<R> {
} 
```

Enter fullscreen mode Exit fullscreen mode

该规范并不意味着是实现的处方，而是定义互操作性的类型。反应流类型显然非常有用，以至于它们*最终*在最近的 Java 9 版本中找到了自己的路，成为了`java.util.concurrent.Flow`类中一对一语义等价的接口，例如:`java.util.concurrent.Flow.Publisher`。

## 遇见电抗器

反应流类型不够；您将需要更高阶的实现来支持过滤和转换之类的操作。这里的反应堆项目是个不错的选择；它建立在反应流规范之上。它提供了`Publisher<T>`的两个专门化。

第一个是`Flux<T>`，是一个产生零个或多个值的`Publisher`。是无界的。第二个是`Mono<T>`，它是一个产生 0 或 1 值的`Publisher<T>`。他们都是发布者，你可以这样对待他们，但是他们比反应流规范走得更远。它们都为操作符提供了处理值流的方法。反应器类型组合得很好——一个东西的输出可以是另一个东西的输入，如果一个类型需要处理其他数据流，它们依赖于`Publisher<T>`实例。

`Mono<T>`和`Flux<T>`都实现`Publisher<T>`；我们的建议是，您的方法接受`Publisher<T>`实例，但返回`Flux<T>`或`Mono<T>`来帮助客户端区分它被给予的数据类型。

假设给你一个`Publisher<T>`，并要求你为这个`Publisher<T>`呈现一个用户界面。您是否应该为一条记录呈现一个详细页面，因为您可能会得到一个`CompletableFuture<T>`？还是应该呈现一个概览页面，用一个列表或网格以分页的方式显示所有的记录？很难知道。

而`Flux<T>`和`Mono<T>`则非常具体。如果给你一个`Flux<T>`，你知道要呈现一个概览页面，而给你一个`Mono<T>`，你知道要呈现一个(或没有)记录的详细页面。

Reactor 是 Pivotal 启动的开源项目；它变得非常受欢迎。脸书在他们的[反应式 RPC 机制 RSocket](https://github.com/rsocket/rsocket-java) 中使用它，由 RxJava 创建者 Ben Christensen 领导。Salesforce 在他们的[反应式 gRPC 实施](https://github.com/salesforce/reactive-grpc)中使用它。它实现了 Reactive Streams 类型，因此可以与支持这些类型的其他技术互操作，如[网飞的 RxJava 2](https://github.com/ReactiveX/RxJava/blob/2.x/src/main/java/io/reactivex/Flowable.java) 、 [Lightbend 的 Akka Streams](https://doc.akka.io/docs/akka/current/stream/operators/Sink/asPublisher.html#aspublisher) 和 [Eclipse Foundation 的 Vert.x 项目](https://vertx.io/docs/vertx-reactive-streams/java/)。RxJava 2 的负责人 David Karnok 也在 Reactor 上与 Pivotal 进行了广泛的合作，使它变得更好。当然，从 Spring Framework 4.0 开始，它就以某种形式出现在 Spring Framework 中了。

## 用 Spring WebFlux 进行无功编程

尽管反应堆项目很有用，但它只是一个基础。我们的应用程序需要与数据源对话。他们需要生产和消费 HTTP、SSE 和 WebSocket 端点。他们需要支持认证和授权。春天提供了这些东西。如果 Reactor 给了我们缺失的隐喻，那么 Spring 帮助我们说同一种语言。

2017 年 9 月发布了 Spring Framework 5.0。它建立在反应器和反应物流规格的基础上。它包括一个新的反应式运行时和组件模型，名为 [Spring WebFlux](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/web-reactive.html#webflux) 。

Spring WebFlux 不依赖也不要求 Servlet APIs 工作。它附带了适配器，允许它在需要时在 Servlet 引擎上工作，但这不是必需的。它还提供了一个全新的基于 Netty 的 web 运行时，名为 Spring WebFlux。Spring Framework 5 使用 Java 8 和 Java EE 7 的基线，现在是 Spring 生态系统的基线，包括 Spring Data Kay、Spring Security 5、Spring Boot 2 和 Spring Cloud Finchley。

## 了解关于反应式编程和 Spring 的更多信息

这篇文章是关于反应式编程、Spring WebFlux 和用 React 处理实时数据的系列文章的第一篇。这个系列的下一篇文章可以在下面找到。

*   [用 Spring WebFlux 构建反应式 APIs】](https://developer.okta.com/blog/2018/09/25/spring-webflux-websockets-react)
*   [全栈对 Spring WebFlux、WebSockets 和 React 的反应](https://developer.okta.com/blog/2018/09/24/reactive-apis-with-spring-webflux)

如果您想了解更多，请查看这些关于反应式编程和 Spring 的有趣资源。

*   [反动宣言](https://www.reactivemanifesto.org/)

*   [Spring Boot 2.0 正式上市](https://spring.io/blog/2018/03/01/spring-boot-2-0-goes-ga)

*   [乔希·朗和于尔根·赫勒的《反应之春》](https://www.youtube.com/watch?v=RJpbuqsoLPo)

有问题吗？请在下面留下评论，在 Twitter 上点击 [@starbuxman](https://twitter.com/starbuxman) 或 [@mraible](https://twitter.com/mraible) ，或者在我们的[开发者论坛](https://devforum.okta.com/)上发表问题。

喜欢你今天学到的吗？关注 [@oktadev](https://twitter.com/oktadev) ，喜欢我们[上脸书](https://www.facebook.com/oktadevelopers/)，[订阅我们的 YouTube 频道](https://www.youtube.com/channel/UC5AMiWqFVFxF1q9Ya1FuZ_Q)。***