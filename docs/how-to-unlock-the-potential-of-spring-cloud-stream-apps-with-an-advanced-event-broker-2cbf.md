# 如何利用高级事件代理释放 Spring 云流应用的潜力

> 原文：<https://dev.to/solacedevs/how-to-unlock-the-potential-of-spring-cloud-stream-apps-with-an-advanced-event-broker-2cbf>

[![](img/b734209859e6bb14b5e034649c778c29.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B1PJPred--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://solace.com/wp-content/uploads/2018/12/spring-cloud-stream-ms-feat-img.png)

使用 Spring Cloud Stream 框架构建应用程序的开发人员现在可以将他们的微服务连接到 Solace PubSub+ event broker，并利用与其他应用程序、云服务和连接设备的简单、全功能的基于消息的集成。

*   [在 GitHub 上](https://github.com/SolaceProducts/spring-cloud-stream-binder-solace)
*   [在 Maven 上](https://mvnrepository.com/artifact/com.solace.spring.cloud/spring-cloud-stream-binder-solace)

事件正在成为当今现代微服务交互的支柱。与以 API 为中心的请求/响应模型相比，发布/订阅和事件流等消息交换模式提供了许多优势，例如能够分离服务，以及随着使用的增加和减少独立地扩展服务。所有这些优势都以更高的敏捷性、更快的上市时间和更丰富、更灵敏的用户体验的形式提供了切实的商业价值。

Pivotal 最近推出了 [Spring Cloud Stream](https://cloud.spring.io/spring-cloud-stream/) ，这是一个新的事件驱动框架，旨在更容易地编写基于消息的微服务。

用他们自己的话说:

> “Spring Cloud Stream 是一个框架，用于构建与共享消息传递系统相连的高度可扩展的事件驱动微服务。该框架提供了一个灵活的编程模型，该模型建立在已经建立和熟悉的 Spring 习惯用法和最佳实践的基础上，包括对持久发布/订阅语义、消费者组和有状态分区的支持。”

Spring Cloud Stream 基于其他 Spring 框架(如 Spring Boot)的“自以为是的配置”方法，将消息中间件的细微差别从应用程序开发人员那里抽象出来，使他们能够专注于他们的微服务的业务逻辑。开发人员不再需要学习事件代理客户端 API、协议或异步通信的复杂语义。

Spring Cloud Stream 提供了一个声明性的、基于注释的接口，该接口跨供应商代理实现进行了标准化。

[![](img/ab06a3f1d1d978eaf16d5be7ac03b82f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9t4eCxIF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://solace.com/wp-content/uploads/2018/09/spring-cloud-stream-ms-diagram-no-bg-750W-205H.png)

复杂事件/数据集成的挑战日益降低开发人员的工作效率。这些开发人员正在使用 Spring Cloud Stream 等现代框架来加速事件驱动的微服务的开发，但由于无法访问从遗留系统、记录系统或移动/物联网设备流中流出的事件，这种效率受到了阻碍。

开发人员经常面临挑战，即使只是试图跨越组织边界交换事件。所有这些全新的绿地微服务正在产生事件和见解，在许多情况下，这些事件和见解必须由非 Spring 云流应用程序以各种方式(如流式传输、排队和重放)使用。

这些组织真正需要的是一个被称为**事件网格**的架构层。

事件网格使企业能够支持事件驱动的架构，从最小的微服务部署到将应用程序扩展到混合云，以一种受治理的、高性能的、健壮的、安全的、架构良好的方式。它提供了集成传统应用、现代微服务、设备、数据存储、SaaS 的能力—**动态地**和实时地。

事件网格为应用程序开发人员和架构师提供了构建和部署分布式事件驱动应用程序的基础，无论他们需要在哪里构建和部署。

Solace PubSub+ event brokers 能够独一无二地帮助组织创建事件网格，当与 [Pivotal 的 Spring framework](https://pivotal.io/spring-app-framework) 结合使用时，它是开发人员创建生产级、反应式微服务的绝佳方式，这些微服务可以在任何地方使用任何事件，甚至可以跨越长距离 WAN 链接和组织边界。

Spring Cloud Stream 允许开发人员在配置中定义事件代理的细节(如代理主机/端口和凭证/证书),并使用 Spring 注释简单地“绑定”到中间件。

您可以在我们的[示例库](https://github.com/SolaceLabs/solace-samples-spring-cloud-stream)中找到使用 Solace PubSub+Spring Cloud Stream binder 的示例。这些样本是 [Spring Cloud Steam 样本](https://github.com/spring-cloud/spring-cloud-stream-samples)的子集，主要关注 Solace PubSub+的配置和基本功能。

微服务要么是源，要么是接收器，要么是处理器，它们指定事件的目的地，在这些事件中它们打算消费和/或生产。Solace 的独特之处在于，这些目的地及其对应的事件可以在网格的一个部分产生(即，在一个事件代理或 Pivotal 平台服务实例上)，并且可以由连接到网格的某个其他部分(即，不同的代理)的应用程序/微服务使用。

现代应用程序极其动态，因为微服务越来越短暂。企业最初可能在内部运行应用程序，但由于利用率的变化，可能需要将其部署到云中(例如，云爆发)。Solace 提供的动态智能路由功能对于实现业务敏捷性和整体上市时间至关重要。

我们来看一个春天云流 app 的例子:

```
 @SpringBootApplication
@EnableBinding(Sink.class)
public class LoggingConsumerApplication {

    public static void main(String[] args) {
        SpringApplication.run(LoggingConsumerApplication.class, args);
    }

    @StreamListener(Sink.INPUT)
    public void handle(Person person) {
        System.out.println("Received: " + person);
    }

    public static class Person {
        private String name;
        public String getName() {
            return name;
        }
        public void setName(String name) {
            this.name = name;
        }
        public String toString() {
            return this.name;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

从事件代理的角度来看，Spring 已经定义了一个“绑定器”接口，在所有绑定器实现中使用标准的[服务提供者接口](https://en.wikipedia.org/wiki/Service_provider_interface)(SPI)来“在幕后”连接代理。这确保了 Spring Cloud Stream 应用程序是事件代理不可知的:它们在代理供应商和版本之间的行为是相同的，并且不会将您局限于任何一个代理。

上面突出显示的代码展示了如何使用特定于消息的注释将应用程序绑定到绑定器(在@EnableBinding 注释中)并从通道接收消息(在@StreamListener 注释中)。在后台，Spring Cloud Stream 将调用正确的绑定器类来执行请求的操作。开发人员只需更改绑定器配置，就可以轻松调用不同的绑定器或使用不同的输入/输出通道。

Solace 完全接受了 Spring Cloud Stream 方法，并发布了 Spring Cloud Stream binder，让应用程序在 Solace PubSub+软件和硬件代理上利用主题、队列和大量其他事件路由功能的高性能和灵活性。该绑定器是开源的，可以在 GitHub 和 T2 Maven Central 上获得，作为 Spring Boot 应用程序的一个附件。

我们还开发了一些 Spring Cloud 示例应用程序，您可以下载并试用。

由于我们与 Pivotal 的强大合作伙伴关系(我们最近获得了他们的 [2018“做正确的事”奖](https://solace.com/press-center/pivotal-recognizes-solaces-commitment/)，我们是首批发布 Spring Cloud Stream binder 的供应商之一。我们很高兴看到我们的客户可以使用 Spring Cloud Stream 以及 [Spring Cloud Data Flow](https://cloud.spring.io/spring-cloud-dataflow/) 构建什么样的应用，这是一个用于创建丰富的数据集成和实时数据处理管道的强大工具包。

使用 GUI 设计器或基于类似 UNIX 的管道语法的 DSL，可以很容易地构造流。

开始使用 Solace Spring Cloud Stream Binder 和 PubSub+ Event Broker，释放您的反应式流和微服务的力量！

[![](img/9aac1a7b585e3f3f1977460ff726d1b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZRej3jIA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://play.vidyard.com/wKDMfTwQA8xMwvjQm9kCJm.jpg)

帖子[如何通过高级事件代理](https://solace.com/blog/unlock-potential-spring-cloud-stream/)释放 Spring Cloud Stream 应用的潜力首先出现在 [Solace](https://solace.com) 上。