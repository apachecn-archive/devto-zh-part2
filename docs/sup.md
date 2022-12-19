# 坞站上的 microservices & rabbitmq

> 原文:# t0]https://dev . to/usmaasahraf/micro services-rabbitq-on-docker-e2f

基于微服务的架构包括将你的 monolith 应用分解成多个完全独立部署和扩展的服务。除了这个基本定义之外，微服务的构成可能有些主观，尽管有一些像[网飞](https://www.nginx.com/blog/microservices-at-netflix-architectural-best-practices/)和[优步](https://eng.uber.com/building-tincup/)这样的巨头采用的经过战斗考验的实践应该总是被考虑的。我会讨论其中的一些。最终，我们希望*将我们的应用程序分成更小的应用程序，每个应用程序都是一个独立的系统&只处理整个应用程序的一个方面，并且做得非常好*。这个分解是一个非常重要的步骤，可以在[子域](http://microservices.io/patterns/decomposition/decompose-by-subdomain.html)的基础上完成，子域必须被正确识别。较小的应用程序更加**模块化&可管理**具有良好定义的边界，可以使用**不同的语言/框架**，**单独失败**以便整个应用程序不会崩溃(没有 SPOF)。以电影院售票为例:

[![](img/41f4d182331cf6f9320aa92bac69b68b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZrMJzjnn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m4cppxfwj2g8ml4uqqxr.png) 
<sub><sup>*来源:[https://code burst . io/build-a-nodejs-cinema-API-gateway-and-deploying-it-to-docker-part-4-703 C2 b 0 DD 269](https://codeburst.io/build-a-nodejs-cinema-api-gateway-and-deploying-it-to-docker-part-4-703c2b0dd269)*</sup></sub>

让我们来分解一下这个鸟瞰图:

I)用户应用程序可以是移动客户端、SPA 等，也可以是使用我们后端服务的任何客户端。

ii) **要求我们的客户分别与我们的每项服务进行沟通被认为是一种不好的做法**，原因我试图在这里解释。这就是 API 网关的用途:接收客户端请求，调用我们的服务，返回响应。因此，客户端只需与一台服务器对话，给人一种巨大的错觉。多个网关可用于*不同种类的客户端*(移动应用、平板电脑、浏览器等)。).它们可以并且应该负责有限的功能，比如合并/加入来自服务、认证、ACL 的响应。在需要动态扩展和移动的大型应用中，网关还需要访问一个[服务注册中心](http://microservices.io/patterns/service-registry.html)，它保存着我们的微服务实例、数据库等的位置。

iii) **每个服务都有自己的存储**。这是一个关键点，可以确保松耦合。然后，一些查询需要连接多个服务拥有的数据。为了避免这种严重的性能损失，可以对数据进行复制和分片。这个原则[不仅在微服务中被容忍，而且被鼓励](http://microservices.io/patterns/data/database-per-service.html)。

iv)对我们的 API 网关进行的 **REST 调用被传递给服务，服务又与其他服务对话，向网关返回一个结果，网关可能会对结果进行编译，并以此响应客户端。不应该在一个客户端对应用程序的请求上发生这样的服务间通信。否则，为了新引入的模块化，我们将牺牲性能，因为另一个 HTTP 往返。**

**理想情况下，单个请求应该只调用一个服务来获取响应**。这意味着服务之间的任何同步请求都应该最小化，而这并不总是可能的；像 [gRPC](https://grpc.io/) 、 [Thrift](https://thrift.apache.org/) 甚至简单的 HTTP(如我们的例子)这样的机制在必要的时候经常被使用。正如您可能已经猜到的，这意味着数据必须在我们的服务之间复制。比方说，`GET /catalog/<<cityId>>` endpoint 还应该返回当时该城市每家影院的首映。根据我们的新策略，首映也必须存储在*影院目录*服务的数据库中。于是，点`iii)`。

## 服务间异步通信

所以，比方说，首映式的改变是由于对 *Movies* 服务的一些 CRUD 操作。为了保持数据同步，更新事件必须被发出并应用到*影院目录*服务。**试着把我们的微服务想象成一个状态机集群，其中状态的更新可能必须在集群间传递，以实现最终的一致性**。当然，我们永远不应该期望我们的最终用户必须等待更长的时间来完成请求，并为了我们的利益而牺牲他们的模块化时间。因此，所有这些通信都必须是非阻塞的。这就是 RabbitMQ 的用武之地。

RabbitMQ 是一个非常[强大的](https://www.rabbitmq.com/#features)消息代理，它实现了 [AMQP 消息协议](https://www.rabbitmq.com/amqp-0-9-1-quickref.html)。摘要如下:首先，在系统上安装一个 RabbitMQ 服务器实例(代理)。然后一个*发行商/生产商*程序连接到这个服务器并发出一条消息。RabbitMQ 将该消息排队，并将其虹吸给一个或多个在 RabbitMQ 服务器上监听的订阅者/消费者程序。

在我进入这篇文章的核心之前，我想明确声明微服务要复杂得多，我们不会讨论像 T2 容错这样的关键话题，因为分布式系统的复杂性，API 网关的完整 T4 角色，服务发现，数据一致性模式，比如使用断路器防止服务故障级联，健康检查和架构模式，比如 CQRS 更别提[如何决定微服务对你是否管用](https://martinfowler.com/articles/microservice-trade-offs.html)。

## rabbit MQ 如何工作

更具体地说，消息被*发布*到 RabbitMQ 代理内部的*交易所*。然后，该交易所根据开发人员定义的称为*绑定*的规则，将该消息的副本分发到*队列*。消息旅程的这一部分被称为*路由*。这种间接性当然有助于非阻塞消息传输。*监听那些收到消息的*队列*的消费者*会收到消息。很简单，对吧？

不完全是。有四种不同类型的*交换* ，每一种都与*绑定*一起定义了一种路由算法。“路由算法”本质上意味着消息如何在队列中分发。在这里详细讨论每一种类型可能有点过了，所以我将只扩展我们将要使用的一种类型:主题交换*:*

 *对于将消息推送到队列的交换，该队列必须绑定到交换。我们可以显式地创建多个具有唯一名称的交换。然而，当您部署 RabbitMQ 时，它会附带一个默认的无名交换。我们创建的每个队列都将自动绑定到这个交换。为了便于描述，我将手动创建一个命名的 exchange，然后将一个队列绑定到它。这个绑定由一个*绑定密钥*定义。同样，绑定密钥的确切工作方式取决于交换的类型。下面是主题交流的工作方式:

*   使用*字符串模式*(绑定密钥)将*队列*绑定到*交换机*
*   发布的消息连同一个*路由密钥*一起被传送到*交换机*
*   *交换*根据之前定义的*绑定密钥*模式检查哪些*队列*与*路由密钥*匹配。

<sub><sup>`*`可以替代恰好一个词。`#`可以代替零个或多个单词。 <sup><sub>[![](img/91df060f68477622e14eb5a0cbc50743.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LTuEnrWN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zepcpbrn07266rcc71sc.png)
<sub><sup>*来源:[https://www . rabbit MQ . com/tutorials/tutorials-five-python . html](https://www.rabbitmq.com/tutorials/tutorial-five-python.html)*</sup></sub></sub></sup></sup></sub>

任何带有路由关键字`"quick.orange.rabbit"`的消息都将被传递到两个队列。然而，带有`"lazy.brown.fox"`的消息只会到达`Q2`。那些路由关键字与任何模式都不匹配的将会丢失。

从某种角度来看，让我们先看一下另外两种交换类型:

*   **扇出交换机**:发送到这种交换机的消息将被发送到所有绑定到它的队列。路由关键字(如果提供的话)将被完全忽略。例如，这可以用于在分布式系统中广播全局配置更新。
*   **直接交换**(最简单):将消息发送到绑定关键字*恰好等于给定路由关键字*的队列。如果有多个使用者在监听队列，那么消息将在它们之间进行负载平衡，因此，通常使用循环方式在多个工作者之间分配任务。

我的例子将会非常简单:一个带有单个 POST 端点的 Python [Flask](http://flask.pocoo.org/) 应用程序，当被调用时，它将声称更新用户信息，向 RabbitMQ 代理发出一条消息(当然是非阻塞的)并返回一个 201。一个单独的 Go 服务将监听来自代理的消息，因此有机会相应地更新它的数据。这三个都将在不同的容器中托管。

## 使用 Docker Compose 建立我们的容器化微服务&代理

提供一堆容器以及随之而来的一切可能是一件痛苦的事情，所以我总是依赖 [Docker Compose](https://docs.docker.com/compose/) 。

这里是完整的代码。我们声明了将用于三个容器的三个服务。需要两个[卷](https://docs.docker.com/storage/volumes/)来将我们的代码放入容器:

```
# docker-compose.yml

version: "3.2"
services:
    rabbitmq-server:
        build: ./rabbitmq-server

    python-service:
        build: ./python-service
        # 'rabbitmq-server' will be available as a network reference inside this service 
        # and this service will start only after the RabbitMQ service does.
        depends_on:
            - rabbitmq-server
        # Keep it running. 
        tty: true
        # Map port 3000 on the host machine to port 3000 of the container.
        # This will be used to receive HTTP requests made to the service.
        ports:
            - "3000:3000"
        volumes:
            - './python-service:/python-service'

    go-service:
        build: ./go-service
        depends_on:
            - rabbitmq-server
        tty: true
        volumes:
            - './go-service:/go-service'

# Host volumes used to store code.
volumes:
    python-service:
    go-service: 
```

Enter fullscreen mode Exit fullscreen mode

Docker 文件基本上是来自 [Docker Hub](https://hub.docker.com/) 的标准文件，我已经添加了:

*   `/go-service`Go 服务容器中的工作目录。
*   `/python-service`Python 服务容器中的工作目录。
*   Go 的 RabbitMQ 客户端库名为 [amqp](https://github.com/streadway/amqp)
*   Python 的 RabbitMQ 客户端[鼠兔](https://pypi.org/project/pika/0.11.0/)&T2】烧瓶

我们的 Flask 应用程序只有一个端点接收一个`user_id`和一个`full_name`，这两个端点将用于更新用户的个人资料。然后，将向 RabbitMQ 代理发送一条表明此更新的消息。

```
# main.py 
from flask import Flask
from flask import request
from flask import jsonify
from services.user_event_handler import emit_user_profile_update

app = Flask(__name__)

@app.route('/users/<int:user_id>', methods=['POST'])
def update(user_id):
    new_name = request.form['full_name']

    # Update the user in the datastore using a local transaction... 
    emit_user_profile_update(user_id, {'full_name': new_name})

    return jsonify({'full_name': new_name}), 201 
```

Enter fullscreen mode Exit fullscreen mode

向其他服务发出事件的逻辑应该总是与应用程序的其余部分分开，所以我将它提取到一个模块中。发布者和消费者都应该明确检查并创建*交换*,因为我们无法知道(也不应该依赖)哪个服务先启动。这是一个很好的实践，大多数 RabbitMQ 客户端库都无缝地促进了这个实践:

```
# services/user_event_handler.py 
import pika
import json

def emit_user_profile_update(user_id, new_data):
    # 'rabbitmq-server' is the network reference we have to the broker, 
    # thanks to Docker Compose.
    connection = pika.BlockingConnection(pika.ConnectionParameters(host='rabbitmq-server'))
    channel    = connection.channel()

    exchange_name = 'user_updates'
    routing_key   = 'user.profile.update'

    # This will create the exchange if it doesn't already exist.
    channel.exchange_declare(exchange=exchange_name, exchange_type='topic', durable=True)

    new_data['id'] = user_id

    channel.basic_publish(exchange=exchange_name,
                          routing_key=routing_key,
                          body=json.dumps(new_data),
                          # Delivery mode 2 makes the broker save the message to disk.
                          # This will ensure that the message be restored on reboot even  
                          # if RabbitMQ crashes before having forwarded the message.
                          properties=pika.BasicProperties(
                            delivery_mode = 2,
                        ))

    print("%r sent to exchange %r with data: %r" % (routing_key, exchange_name, new_data))
    connection.close() 
```

Enter fullscreen mode Exit fullscreen mode

不要被`channel`迷惑了。一个*通道*只是一个[虚拟的轻量级连接](https://www.rabbitmq.com/tutorials/amqp-concepts.html#amqp-channels) *在*TCP 连接中，意在防止打开多个昂贵的 TCP 连接。尤其是在多线程环境中。

`durable`参数确保*交换*被持久化到磁盘上，并且如果代理由于任何原因崩溃或离线，可以被恢复。发布者(Python 服务)创建一个名为`user_updates`的*交换*，并将用户的更新数据以`user.profile.update`作为*路由关键字*发送给它。这将与一个`user.profile.*` *绑定键*匹配，我们的 Go 服务将定义这个绑定键:

```
// main.go

package main

import (
    "fmt"
    "log"
    "github.com/streadway/amqp"
)

func failOnError(err error, msg string) {
    if err != nil {
        // Exit the program.
        panic(fmt.Sprintf("%s: %s", msg, err))
    }
}

func main() {
    // 'rabbitmq-server' is the network reference we have to the broker, 
    // thanks to Docker Compose.
    conn, err := amqp.Dial("amqp://guest:guest@rabbitmq-server:5672/")
    failOnError(err, "Error connecting to the broker")
    // Make sure we close the connection whenever the program is about to exit.
    defer conn.Close()

    ch, err := conn.Channel()
    failOnError(err, "Failed to open a channel")
    // Make sure we close the channel whenever the program is about to exit.
    defer ch.Close()

    exchangeName := "user_updates"
    bindingKey   := "user.profile.*"

    // Create the exchange if it doesn't already exist.
    err = ch.ExchangeDeclare(
            exchangeName,   // name
            "topic",        // type
            true,           // durable
            false,
            false,
            false,
            nil,
    )
    failOnError(err, "Error creating the exchange")

    // Create the queue if it doesn't already exist.
    // This does not need to be done in the publisher because the
    // queue is only relevant to the consumer, which subscribes to it.
    // Like the exchange, let's make it durable (saved to disk) too.
    q, err := ch.QueueDeclare(
            "",    // name - empty means a random, unique name will be assigned
            true,  // durable
            false, // delete when the last consumer unsubscribes
            false, 
            false, 
            nil,   
    )
    failOnError(err, "Error creating the queue")

    // Bind the queue to the exchange based on a string pattern (binding key).
    err = ch.QueueBind(
            q.Name,       // queue name
            bindingKey,   // binding key
            exchangeName, // exchange
            false,
            nil,
    )
    failOnError(err, "Error binding the queue")

    // Subscribe to the queue.
    msgs, err := ch.Consume(
            q.Name, // queue
            "",     // consumer id - empty means a random, unique id will be assigned
            false,  // auto acknowledgement of message delivery
            false,  
            false,  
            false,  
            nil,
    )
    failOnError(err, "Failed to register as a consumer")

    forever := make(chan bool)

    go func() {
        for d := range msgs {
            log.Printf("Received message: %s", d.Body)

            // Update the user's data on the service's 
            // associated datastore using a local transaction...

            // The 'false' indicates the success of a single delivery, 'true' would
            // mean that this delivery and all prior unacknowledged deliveries on this
            // channel will be acknowledged, which I find no reason for in this example.
            d.Ack(false)
        }
    }()

    fmt.Println("Service listening for events...")

    // Block until 'forever' receives a value, which will never happen.
    <-forever
} 
```

Enter fullscreen mode Exit fullscreen mode

对于非 TLS 连接，RabbitMQ 默认使用端口 5672，并使用“guest”作为用户名和密码。你可以研究可用的[过多的配置选项](https://www.rabbitmq.com/configure.html)以及如何将它们与 [Pika](https://pika.readthedocs.io/en/0.11.2/modules/index.html) 和 [Go amqp](https://godoc.org/github.com/streadway/amqp) 一起使用。

你可能想知道这一行是干什么的: **`d.Ack(false)`**

这告诉代理消息已经被传递，处理成功，可以删除了。默认情况下，这些确认会自动发生。但是我们在订阅队列时指定了另外一个名字: **`ch.Consume`** 。

现在，如果 Go 服务崩溃(由于任何不可预见的原因)，确认将不会被发送，这将导致代理将消息重新排队，以便它有机会被处理。

## 启动微服务

好吧，让我们发动起来:

跑 **`docker-compose up`**

当这三个服务构建完成后(第一次至少需要几分钟)，使用 **`docker ps`** 检查它们的名称:

[![](img/7c58d69f6f1a2998e1da2b57f9b790e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EnlpQ-pj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/694axi1gm9nxudiu418g.png)

打开两个新的终端，使用各自的容器名称 SSH 到 Python 和 Go 容器，并启动服务器:

【T2`docker exec -it microservicesusingrabbitmq_python-service_1 bash`
**`FLASK_APP=main.py python -m flask run --port 3000 --host 0.0.0.0`**

【T2`docker exec -it microservicesusingrabbitmq_go-service_1 bash`
**`go run main.go`**

打开第三个终端发送 POST 请求。我将使用 Curl:

**T2`curl -d "full_name=usama" -X POST http://localhost:3000/users/1`**

你会看到传输:

[![](img/3141d1b328ba5d4dc010896cb537bfd5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M2sJig-B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9945lbxplrxsm5e21qsp.png)

[![](img/df7a98fa4c182cda4e14b9f43f5dc8e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RiQ9whFE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ufwdryd0l0zw53k11wm2.png)

在任何时候，您也可以 SSH 到 RabbitMQ 容器，然后四处看看:

*   `rabbitmqctl list_exchanges`(列出该代理节点上的所有交易所)
*   `rabbitmqctl list_queues`(列出该代理节点上的所有队列)
*   `rabbitmqctl list_bindings`(列出此代理节点上的所有绑定)
*   `rabbitmqctl list_queues name messages_ready messages_unacknowledged`(列出所有队列，每个队列有多少条消息*准备发送给客户*和*已发送但尚未确认*

正如我在开始时提到的，这绝不是对微服务的深入研究。有很多问题要问，我将尝试回答一个重要的问题:我们如何使这种沟通具有交易性？那么，如果我们的 Go 服务(消费者)在更新其终端的状态时抛出异常，并且我们需要确保更新事件回滚到受其影响的所有服务，会发生什么呢？。想象一下，当我们有几个微服务和数千个这样的“更新事件”时，这将变得多么复杂。**本质上，我们需要合并执行回滚**的独立事件。

在我们的例子中，如果 Go 服务在更新数据时抛出异常，它必须向 Python 服务发回一条消息，告诉它回滚更新。*同样重要的是要注意，在这种错误的情况下，消息传递必须得到确认(即使处理不成功)，这样消息就不会被代理*重新排队。在编写我们的消费者时，我们必须决定哪些错误意味着消息应该重新排队(重试),哪些错误意味着消息不应该重新排队而只是回滚。

但是，我们如何指定回滚哪个更新事件，回滚将如何发生呢？[传奇模式](https://blog.couchbase.com/saga-pattern-implement-business-transactions-using-microservices-part/)和[事件源](http://microservices.io/patterns/data/event-sourcing.html)被广泛用于确保这样的数据一致性。

## 关于设计经纪人的几句话

考虑两件事:要使用的的**类型的交易所和**如何对交易所进行分组**。**

如果你需要向你系统中的所有服务广播某些类型的消息，看看[扇出交换类型](https://www.rabbitmq.com/tutorials/amqp-concepts.html#exchange-fanout)。然后，对交换分组的一种方式可以是基于事件，例如三个扇出交换，命名为`user.profile.updated`、`user.profile.deleted`、`user.profile.added`。这可能不是您一直想要的，因为您可能会有太多的交换，如果不创建新的*交换*、*队列*和*绑定*，您将无法过滤特定消费者的消息。

另一种方法是根据你系统中的实体创建*主题交换*。因此，在我们的第一个示例中，`user`、`movie`、`cinema`等可以是实体，比如说，绑定到`user`的*队列*可以使用绑定键，如`user.created`(创建用户时获取消息)、`user.login`(用户刚刚登录时获取消息)、`user.roles.grant`(获取消息，告知用户已被授予授权角色)、`user.notify`(向用户发送通知)等。

始终使用路由来过滤消息并将其传递给特定的使用者。编写代码来丢弃某些传入的消息，同时接受其他消息是一种反模式。消费者应该只接收他们需要的消息。

最后，如果您的需求很复杂，并且您需要基于多个属性将消息过滤到特定的消费者，那么使用[头交换](https://www.rabbitmq.com/tutorials/amqp-concepts.html#exchange-headers)。

[*尽情享受！*T3】](https://github.com/UsamaAshraf/microservices-using-rabbitmq)*