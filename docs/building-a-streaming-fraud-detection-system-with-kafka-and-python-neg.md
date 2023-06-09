# 用 Kafka 和 Python 构建流媒体欺诈检测系统

> 原文：<https://dev.to/florimondmanca/building-a-streaming-fraud-detection-system-with-kafka-and-python-neg>

这是我关于用 Apache Kafka 构建流应用程序的系列文章的第二篇。如果你错过了，你可以读读[的开场](https://dev.to/florimondmanca/streaming-applications-with-apache-kafka-the-opening-58f0)，知道为什么这个系列会存在，会有什么期待。

这一次，我们将亲自动手，使用 Python 客户机创建我们的第一个由 Apache Kafka 支持的流应用程序。我会试着让它尽可能接近**现实世界**的卡夫卡应用。

我写这篇博文是作为**教程**。你不需要知道任何关于流处理和 Apache Kafka 的知识——我会边走边解释概念和术语——你将能够理解我们构建系统的过程。

我们要建立的是一个实时欺诈检测系统。我们将从头开始做**。我们将生成一个交易流，并对其进行处理，以检测哪些交易是潜在的欺诈。**

 **听起来很刺激？希望如此！

你可以在 GitHub 上找到支持这篇博文的完整代码。

## 先决条件

我写了这篇文章，这样你就可以**跟着**一起开发各种应用。但是，会有一些前提条件。

首先，我们需要在本地运行一个 Kafka 集群。这通常不会占用太多资源，但是您可能需要一台像样的开发机器。

接下来，我们将使用 **Docker Compose** 运行集群，因为这将使我们尽可能地接近生产系统——真实世界的*。所以确保你已经安装了 [Docker](https://docs.docker.com/install/) 和 [Docker Compose](https://docs.docker.com/compose/) 。*

正因为如此，我也期望你有**一些 Docker 和 Docker Compose** 的知识。虽然我将解释更详细的细节，但您至少应该知道如何读取一个`Dockerfile`和一个`docker-compose.yml`文件。

最后，因为我们将使用 **Python** 构建应用程序，所以最后的先决条件是对 Python 语言和生态系统有一个基本的了解。

差不多就这些了，让我们言归正传吧！

## 点燃了当地的卡夫卡集群

Apache Kafka 是一个软件，和所有软件一样，它运行在真实的计算机上，也就是你自己的计算机上。

首先，讲一点**术语**。Kafka 是一个**分布式系统**，它在一个**集群**中运行，即多个计算机(也称为**节点**)相互通信。在卡夫卡的行话中，节点被称为**经纪人**。你的电脑将是这里唯一的代理。

卡夫卡需要什么才能奔跑？实际上，它只需要两样东西——一个 Kafka broker 和一个动物园管理员实例。

“什么是动物园管理员？!"

保持冷静——你真的不需要知道那是什么。但简而言之，它是一个协调软件，也是分布式的，Apache Kafka 使用它来跟踪集群状态和成员。它是任何 Kafka 集群的基本组成部分，但我们不会谈论更多。

在介绍中，我提到我们将使用 **Docker Compose** 来运行集群。让我们看看结果如何。

#### 坞站复合配置

这是我们将使用的`docker-compose.yml`文件。它使用了来自[汇合平台](https://www.confluent.io/product/confluent-platform/) — [汇合平台](https://www.confluent.io)的 Docker 图像，该平台是阿帕奇卡夫卡社区的主要参与者，其灵感来自他们的[卡夫卡单节点示例](https://github.com/confluentinc/cp-docker-images/blob/5.0.0-post/examples/kafka-single-node/docker-compose.yml)。

它有两个服务:一个用于 Kafka 代理，一个用于 Zookeeper 实例。

```
version: '3'

services:

  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000

  broker:
    image: confluentinc/cp-kafka:latest
    depends_on:
      - zookeeper
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://broker:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1 
```

Enter fullscreen mode Exit fullscreen mode

我将详细说明这里发生了什么—但是如果您只是想运行它，您也可以跳到下一部分。

为**动物园管理员**准备的`zookeeper`服务。它使用融合平台的 Docker 镜像，`cp-zookeeper`。这两个环境变量定义了 Zookeeper 可以在哪个端口上访问(我们将把这个端口传递给代理)，以及它的基本时间单位(一个必需的配置细节)。

第二，`kafka`服务是我们的**卡夫卡经纪人**。在为数不多的环境变量中，我们需要关心的两个是`KAFKA_ZOOKEEPER_CONNECT`，它告诉代理在哪里可以找到 Zookeeper，以及`KAFKA_ADVERTISED_LISTENERS`，它定义了我们在哪里可以从其他应用程序连接到代理。你不需要关心复制因子；我只想告诉您，它被设置为 1，因为我们在集群中只有一个代理。

现在，我们已经定义了集群的 Docker 组合配置，让我们…

#### 旋转起集群！

现在是时候**启动我们当地的卡夫卡集群**了。怎么会？如果你了解 Docker Compose，那就很简单了。只需转到您的项目根目录(`docker-compose.yml`所在的位置)并运行:

```
$ docker-compose up 
```

Enter fullscreen mode Exit fullscreen mode

您将在控制台中看到一大堆日志——别担心！这很正常。卡夫卡和动物园管理员在启动时都相当啰嗦。要知道它们何时完成初始化，您可以在一个单独的终端中运行。它等待`broker`服务输出一个“started”消息。

```
$ docker-compose logs -f broker | grep started
broker_1 | INFO [KafkaServer id=1] started (kafka.server.KafkaServer) 
```

Enter fullscreen mode Exit fullscreen mode

如果真的发生了，恭喜你！现在，您已经有了一个启动并运行的单节点 Kafka 集群。它本身可能没什么用。我们开始传输一些数据怎么样？

## 进入:欺诈检测系统

没错——现在是我们开始建造东西的时候了！🎉

我本可以经历一个“你好，世界！”流应用程序，你在一端(生产者)输入一些消息，在另一端(消费者)实时接收它们。但是在 [Confluent 的 Docker Quickstart](https://docs.confluent.io/current/quickstart/cos-docker-quickstart.html#cos-docker-quickstart) 和其他地方已经有了这个教程。

相反，我希望我们建立一个**真实系统**来解决一个**实际业务用例**。控制文章的范围并不是一件容易的事情，但是我们会看到它是如何进行的。

我们将构建的系统是一个简单的**欺诈检测机制**——你可能会在银行和交易监控系统中遇到同样的机制。

我们将在一端生成虚假交易，在另一端过滤并记录那些看起来可疑的交易。这将涉及一个**交易生成器**(仅仅因为我们需要处理材料)和一个**欺诈检测器**。这两个应用程序都将在 Docker 容器中运行，并与我们的 Kafka 集群交互。

<figure>

[![](img/e21da2e79c2524dce347072ebb066636.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8FZUJGTq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://florimondmanca-personal-website.s3.amazonaws.com/media/markdownx/d37ac6ca-64e7-4f26-9f51-baccd9022544.png)

<figcaption>Block diagram of the fraud detection system.</figcaption>

</figure>

现在我们已经看到了这个系统背后的基本思想，让我们开始写一些代码，好吗？

## 创建应用框架

我们将首先为交易生成器和欺诈检测器创建集装箱化的框架。它们将是直接的 Python 应用程序，所以我们将在两种情况下使用相同的`Dockerfile`:T1

```
# Dockerfile
FROM python:3.6

WORKDIR /usr/app

ADD ./requirements.txt ./
RUN pip install -r requirements.txt
ADD ./ ./

CMD ["python", "app.py"] 
```

Enter fullscreen mode Exit fullscreen mode

我们将创建两个包含这个`Dockerfile`的文件夹:一个用于交易生成器，一个用于欺诈检测器。

我们还将有一个 Python 依赖项— [Kafka Python](https://github.com/dpkp/kafka-python) ，Kafka 的一个 Python 客户端(以及其他)。我们将使用它来构建与 Kafka 集群交互的应用程序。

```
# requirements.txt
kafka-python 
```

Enter fullscreen mode Exit fullscreen mode

实际的 Python 代码将保存在每个应用程序的`app.py`文件中。最后，这是我们最终得到的文件夹结构:

```
.
├── docker-compose.yml
├── detector
│   ├── Dockerfile
│   ├── app.py
│   └── requirements.txt
└── generator
    ├── Dockerfile
    ├── app.py
    └── requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

稍后，我们将把这些服务集成到我们的开发环境中。但在此之前，让我带您看一下将 Kafka 放入其自己的 Docker Compose 的过程，这将允许我们像使用实际的生产集群一样使用它。

## 孤立卡夫卡集群

在制作中，**卡夫卡总是作为一种服务**。我的意思是，集群独立于使用它的应用程序运行**。后者只需**将**连接到集群，以从集群发布或读取事件。**

我们正试图构建一个真实世界的系统，那么让我们的应用程序也与一个独立的、类似生产的 Kafka 集群交互怎么样？

为了在本地做到这一点，我们将把我们的`zookeeper`和`broker`服务转移到一个**单独的 Docker 组合配置**。让我们把它放在一个`docker-compose.kafka.yml`文件里。稍后，我们将只对我们的应用服务使用常规的`docker-compose.yml`。

```
# docker-compose.kafka.yml
version: '3'

services:
    # ... zookeeper and broker as before ... 
```

Enter fullscreen mode Exit fullscreen mode

虽然你可能认为这已经足够了，但还缺少一些东西。为了允许两个组合访问**相同的网络**(记住每个 Docker 组合文件默认使用它们自己的私有网络)，我们将使用一个**外部 Docker 网络**。我们就叫它`kafka-network`。现在让我们继续创建网络:

```
$ docker network create kafka-network
85351a30f253b0fb8ae197ea07ddeddd0412c8a789e910dd4a3f424057122d30 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以在`docker-compose.kafka.yml` :
中添加`networks`部分

```
# docker-compose.kafka.yml
version: '3'

services:
    # ... zookeeper and broker as before ...

# NEW
networks:
  default:
    external:
      name: kafka-network 
```

Enter fullscreen mode Exit fullscreen mode

(如果你愿意，可以在 Docker Compose 中阅读更多关于[网络的内容)。](https://docs.docker.com/compose/networking/)

酷，我们现在有一个单独的 Docker Compose 配置的 Kafka 集群。*那又怎样？*

嗯，这意味着我们将能够把集群视为一个**完全隔离的系统**。您可以独立于 Docker 应用程序的其余部分来旋转或拆卸它——就像现实世界中发生的一样。

实际上，操作这个 Docker 组合需要我们使用`-f`标志来指定我们使用哪个配置。

例如，要加速 Kafka 集群，您现在可以使用:

```
docker-compose -f docker-compose.kafka.yml up 
```

Enter fullscreen mode Exit fullscreen mode

如果你想访问日志:

```
docker-compose -f docker-compose.kafka.yml logs broker 
```

Enter fullscreen mode Exit fullscreen mode

你明白要点了。

现在已经完成了，我们可以创建一个具有相同网络配置的空的`docker-compose.yml`，稍后我们将向其中添加事务生成器和欺诈检测服务:

```
# docker-compose.yml
version: '3'

services:
    # TODO: generator and detector coming soon

# Give this composition access to the Kafka network
networks:
  default:
    external:
      name: kafka-network 
```

Enter fullscreen mode Exit fullscreen mode

转移注意力到此为止——我想我们都准备好了！让我们开始构建**流式欺诈检测系统**。

## 构建交易生成器

要进行流处理，我们首先需要一个流。这就是事务生成器发挥作用的时候了。我们将使用它来创建一个**事务流**。

在 Kafka 中，您可以构建一些不同类型的应用程序。一类应用是**生产者**。

制片人做的正是我们需要的——他们**向卡夫卡集群发布消息**。这些消息将存储在我们的代理中，我们称之为**主题**——一个命名的消息磁盘存储。

<figure>

[![](img/a9fddde70938ac76473395e79fe8cc10.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nlsL6MOI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://florimondmanca-personal-website.s3.amazonaws.com/media/markdownx/b51601f8-0fd0-4acc-bc94-14ec01e507fe.png)

<figcaption>How a Kafka producer works. Simple, right?</figcaption>

</figure>

现在，我有一些好消息。我们使用的 Python 客户端(Kafka Python)允许我们构建生产者。🎉

因此，让我们使用使用 [Kafka Python 的生产者 API](https://github.com/dpkp/kafka-python#kafkaproducer) 将消息发送到`transactions`主题中。

我很难不在这里复制粘贴一些代码。这不是一个关于 Kafka Python 客户端的教程，所以我将带您完成这些步骤。如果你想要更多的细节，我们可以简单地参考 [Kafka Python docs](https://kafka-python.readthedocs.io/en/latest/index.html) 。

首先，让我们从 Kafka Python 库抓取`KafkaProducer`开始。

```
from kafka import KafkaProducer 
```

Enter fullscreen mode Exit fullscreen mode

然后我们获取我们的代理的 URL(从一个环境变量中——最佳实践仍然适用)。生产者使用该 URL 引导他们与 Kafka 集群的连接。

```
import os
# ... KAFKA_BROKER_URL = os.environ.get('KAFKA_BROKER_URL') 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以实例化实际的生产者。它公开了一个简单的 API(详见[文档](https://kafka-python.readthedocs.io/en/latest/apidoc/KafkaProducer.html))向 Kafka 主题发送消息。

```
producer = KafkaProducer(bootstrap_servers=KAFKA_BROKER_URL) 
```

Enter fullscreen mode Exit fullscreen mode

下一步是——我们该怎么处理制片人？我们可以用它来发送信息。什么消息？好问题！让我们暂时跳过这一步，假设我们发送空字符串。

现在，事务生成器的目标是*伪造*一个**无限的事务流**流经我们的系统。所以我们将简单地运行一个无限循环，为一个主题生成这些消息。

旁注——我喜欢用[如何画自行车棚:卡夫卡主题命名惯例](https://medium.com/@criccomini/how-to-paint-a-bike-shed-kafka-topic-naming-conventions-1b7259790073)中的建议来命名主题。这就是为什么我将使用`queueing.transactions`作为事务主题名称，它有效地表示了要处理的事务队列。

下面是我们如何实现这样一个循环:

```
from time import sleep
# ... while True:
    message = ''  # TODO
    # Kafka messages are plain bytes => need to `.encode()` the string message
    producer.send('queueing.transactions', value=message.encode())
    sleep(1)  # Sleep for one second before producing the next transaction 
```

Enter fullscreen mode Exit fullscreen mode

最后，这是我们得到的 Python 文件:

```
# generator/app.py import os
from time import sleep
from kafka import KafkaProducer

KAFKA_BROKER_URL = os.environ.get('KAFKA_BROKER_URL')

if __name__ == '__main__':
    producer = KafkaProducer(bootstrap_servers=KAFKA_BROKER_URL)
    while True:
        message = ''  # TODO
        producer.send('queueing.transactions', value=message.encode())
        sleep(1) 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们将`generator`应用添加到我们的`docker-compose.yml`配置:

```
# docker-compose.yml
services:
  # ...
  generator:
    build: ./generator
    environment:
      KAFKA_BROKER_URL: broker:9092 
```

Enter fullscreen mode Exit fullscreen mode

在我们启动这个应用程序之前，我们需要用一个代表事务的**实际值来填充消息。**

为此，我构建了一个实用函数`create_random_transaction()`,我们可以用它来创建随机事务。这些账户将有一个`source`账户、一个`target`账户、一个`amount`账户和一个`currency`。

这非常简单，所以我只是将`transactions.py`文件复制粘贴到这里:

```
# generator/transactions.py from random import choices, randint
from string import ascii_letters, digits

account_chars: str = digits + ascii_letters

def _random_account_id() -> str:
    """Return a random account number made of 12 characters."""
    return ''.join(choices(account_chars, k=12))

def _random_amount() -> float:
    """Return a random amount between 1.00 and 1000.00."""
    return randint(100, 1000000) / 100

def create_random_transaction() -> dict:
    """Create a fake, randomised transaction."""
    return {
        'source': _random_account_id(),
        'target': _random_account_id(),
        'amount': _random_amount(),
        # Keep it simple: it's all euros
        'currency': 'EUR',
    } 
```

Enter fullscreen mode Exit fullscreen mode

不错！我们现在可以用一个假交易来替换空消息:

```
from transactions import create_random_transaction

# ... transaction: dict = create_random_transaction()
message: str = json.dumps(transaction)
producer.send('queueing.transactions', value=message.encode())
# ... 
```

Enter fullscreen mode Exit fullscreen mode

让我们通过使我们的`generator`应用程序更加可配置来添加最后的润色——这总是一个好的实践。让我们:

*   将`queueing.transactions`主题提取到`KAFKA_TRANSACTIONS_TOPIC`环境变量中
*   通过`TRANSACTIONS_PER_SECOND`环境变量配置睡眠时间。
*   利用`KafkaProducer`的`value_serializer`参数自动将消息从 dict 序列化为 JSON 字节。

下面是交易生成器 :
的**最终版本**

```
# generator/app.py import os
import json
from time import sleep
from kafka import KafkaProducer
from transactions import create_random_transaction

KAFKA_BROKER_URL = os.environ.get('KAFKA_BROKER_URL')
TRANSACTIONS_TOPIC = os.environ.get('TRANSACTIONS_TOPIC')
TRANSACTIONS_PER_SECOND = float(os.environ.get('TRANSACTIONS_PER_SECOND'))
SLEEP_TIME = 1 / TRANSACTIONS_PER_SECOND

if __name__ == '__main__':
    producer = KafkaProducer(
        bootstrap_servers=KAFKA_BROKER_URL,
        # Encode all values as JSON
        value_serializer=lambda value: json.dumps(value).encode(),
    )
    while True:
        transaction: dict = create_random_transaction()
        producer.send(TRANSACTIONS_TOPIC, value=transaction)
        print(transaction)  # DEBUG
        sleep(SLEEP_TIME) 
```

Enter fullscreen mode Exit fullscreen mode

这是 Docker 编写服务的样子:

```
# docker-compose.yml
services:
  generator:
      build: ./generator
      environment:
        KAFKA_BROKER_URL: broker:9092
        TRANSACTIONS_TOPIC: queueing.transactions
        TRANSACTIONS_PER_SECOND: 1000 
```

Enter fullscreen mode Exit fullscreen mode

## 试用事务生成器

在这一点上，让事情运转起来并检查一切是否顺利可能是个好主意。毕竟，如果你自己都不能证实，我又有什么资格告诉你这行得通呢？

为此，让我们首先确保本地 Kafka 集群启动，然后启动主 Docker Compose:

```
$ docker-compose up 
```

Enter fullscreen mode Exit fullscreen mode

这将启动`generator`的制作人。

我们的 debug `print`将把事务从 Python 输出到控制台，但是这并不保证消息被有效地生成到主题。

为了验证这一点，我们可以使用融合平台中内置的一个脚本，并显示在`confluent/kafka-cp` Docker 映像中。这个脚本叫做`kafka-console-consumer`。它允许您在控制台上读取和打印主题的内容。

该命令需要在`broker`容器中运行，这使得它有点晦涩，但是它在这里(您需要在一个单独的终端中运行):

```
docker-compose -f docker-compose.kafka.yml exec broker kafka-console-consumer --bootstrap-server localhost:9092 --topic queueing.transactions --from-beginning 
```

Enter fullscreen mode Exit fullscreen mode

`--from-beginning`标志意味着该命令将读取整个主题，而不仅仅是读取新消息。

你可以让它运行一段时间，然后按`Ctrl+C`停止`kafka-console-consumer`，看阅读消息总数:

```
...
{"source": "Jpe2zq1QDYTn", "target": "lLbLnl0FSr7T", "amount": 254.83, "currency": "EUR"}
{"source": "mfGWAdLKW8jh", "target": "ede8FQpjBeej", "amount": 785.22, "currency": "EUR"}
{"source": "GufdRdi1TElh", "target": "u0ANirFVu76B", "amount": 407.64, "currency": "EUR"}
^C
Processed a total of 4783 messages 
```

Enter fullscreen mode Exit fullscreen mode

精彩！它起作用了——虚假交易被有效地产生到主题。

您可以暂时停止发电机:`Ctrl+C`或`$ docker-compose down`。

那么，到目前为止我们有什么发现？

*   一个本地 Kafka 集群启动并运行🚀
*   一个基于生成器的应用程序，它生成虚假的事务并将它们发布到 Kafka 主题📝

这是架构图:

<figure>

[![](img/20ae4b701faa60f1d111ead2edd6b17b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--N7SIVTIR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://florimondmanca-personal-website.s3.amazonaws.com/media/markdownx/8237aeaa-30d4-4ad8-8f23-6fa637af8ed9.png)

<figcaption>Transaction generator: check!</figcaption>

</figure>

我们只是忽略了一件事——实际的**欺诈检测器**处理交易流并检测可疑交易！

## 构建欺诈检测器

欺诈检测器是**流处理应用**的典型示例。

它将一个交易流作为输入，执行某种过滤，然后将结果输出到两个独立的流中——合法的和可疑的，这种操作也被称为**分支**。

<figure>

[![](img/0f7aaa0164983af07b8def339b0c7562.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gkM6dgTn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://florimondmanca-personal-website.s3.amazonaws.com/media/markdownx/288f76de-ccdb-4be3-83d8-672e0f9dd326.png)

<figcaption>The detector branches out the transaction stream in two separate streams. ✂️</figcaption>

</figure>

除了将消息发布到主题中的生产者之外，还有另一种类型的 Kafka 应用程序可以从主题中读取消息。他们被称为**消费者**。实际上，我们已经通过上面的`kafka-console-consumer`脚本使用了一个！

消费者可以阅读一个或多个主题的信息。实际上，他们**订阅这些主题**，卡夫卡会在这些主题发布时向他们广播。这是 Kafka 制作的流媒体应用程序的**反应性**的核心特征。

再说一次，我们很幸运！Kafka Python 客户端允许我们用 Python 构建消费者。为此，我们将使用 [Kafka Python 的消费者 API](https://github.com/dpkp/kafka-python#kafkaconsumer) 。

然而，欺诈检测者不会是一个普通的消费者。是一个**流应用**。因此，它使用消费者来读取消息，然后对这些消息进行自己的处理，并将消息生成回两个输出主题中的一个。所以我们需要**一个消费者和一个生产者**。

首先，我们来写消费者部分。同样，我将一步一步来，并最终向您展示最终结果。

让我们从抓住`KafkaConsumer` :
开始

```
# detector/app.py from kafka import KafkaConsumer 
```

Enter fullscreen mode Exit fullscreen mode

让我们从环境变量中获取代理 URL 和事务主题:

```
import os
# ... KAFKA_BROKER_URL = os.environ.get('KAFKA_BROKER_URL')
TRANSACTIONS_TOPIC = os.environ.get('TRANSACTIONS_TOPIC') 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以实例化消费者。我们知道 transactions 主题包含 JSON 编码的值(由 transactions generator 生成)，所以我们可以使用`KafkaConsumer`的`value_deserializer`将原始字节自动加载到 Python dictionnary 中。这类似于我们以前用过的`KafkaProducer`的`value_serializer`。

```
import json
# ... consumer = KafkaConsumer(
    TRANSACTIONS_TOPIC,
    bootstrap_servers=KAFKA_BROKER_URL,
    value_deserializer=lambda value: json.loads(value),
) 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以通过迭代消费者来处理消息流:

```
for message in consumer:
    transaction: dict = message.value
    # TODO: determine if transaction is suspicious
    # TODO: output transaction in the correct topic (legit or fraud) 
```

Enter fullscreen mode Exit fullscreen mode

现在一个有趣的问题是——我们如何确定一项交易是否是欺诈？

有很多选项，这在很大程度上取决于业务用例。

在现实世界中，**检测欺诈是一个困难的问题**,因为交易可能以多种方式表明欺诈。例如，如果某人在 24 小时内从 50 个不同的账户收到 50 笔交易，每笔正好 1000 欧元，这可能是可疑的。你甚至可以考虑添加一个人工智能层来进一步改善预测。

虽然这是可行的，但这种规则在卡夫卡那里比我们在这里讨论的更难实现。我们在这里感兴趣的是卡夫卡如何被用来**将流处理方法带入生活**。

为了简单起见，我们必须作弊一点。想象一下，我们与一个虚构的银行系统打交道，在这个系统中，一次汇款超过 900 欧元是非法的。因此，任何`amount`大于 900 的交易都可以被认为是欺诈。

以下是确定交易是否可疑的相应函数:

```
def is_suspicious(transaction: dict) -> bool:
    return transaction['amount'] >= 900 
```

Enter fullscreen mode Exit fullscreen mode

猜猜看——我们现在有办法决定将事务重定向到哪个主题了！

我们现在准备实现检测器的主体。为了将消息生成到主题中，我们需要一个`KafkaProducer`。让我们实例化它——就像我们对事务生成器所做的那样。

```
from kafka import KafkaProducer
# ... producer = KafkaProducer(
    bootstrap_servers=KAFKA_BROKER_URL,
    value_serializer=lambda value: json.dumps(value).encode(),
) 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以利用`is_suspicious()`和生产者来确定事务应该到达哪个主题，然后将事务生成到那个主题:

```
import os
# ... LEGIT_TOPIC = os.environ.get('LEGIT_TOPIC')
FRAUD_TOPIC = os.environ.get('FRAUD_TOPIC')
# ... for message in consumer:
    transaction: dict = message.value
    topic = FRAUD_TOPIC if is_suspicious(transaction) else LEGIT_TOPIC
    producer.send(topic, value=transaction) 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！我们把所有的部件都插在一起了。

在我们尝试之前，这里是欺诈检测器 :
的**最终版本**

```
# detector/app.py import os
import json
from kafka import KafkaConsumer, KafkaProducer

KAFKA_BROKER_URL = os.environ.get('KAFKA_BROKER_URL')
TRANSACTIONS_TOPIC = os.environ.get('TRANSACTIONS_TOPIC')
LEGIT_TOPIC = os.environ.get('LEGIT_TOPIC')
FRAUD_TOPIC = os.environ.get('FRAUD_TOPIC')

def is_suspicious(transaction: dict) -> bool:
    """Determine whether a transaction is suspicious."""
    return transaction['amount'] >= 900

if __name__ == '__main__':
    consumer = KafkaConsumer(
        TRANSACTIONS_TOPIC,
        bootstrap_servers=KAFKA_BROKER_URL,
        value_deserializer=lambda value: json.loads(value),
    )
    producer = KafkaProducer(
        bootstrap_servers=KAFKA_BROKER_URL,
        value_serializer=lambda value: json.dumps(value).encode(),
    )
    for message in consumer:
        transaction: dict = message.value
        topic = FRAUD_TOPIC if is_suspicious(transaction) else LEGIT_TOPIC
        producer.send(topic, value=transaction)
        print(topic, transaction)  # DEBUG 
```

Enter fullscreen mode Exit fullscreen mode

以及我们`docker-compose.yml`配置中对应的服务:

```
# docker-compose.yml
services:
  # ... generator here ...
  detector:
    build: ./detector
    environment:
      KAFKA_BROKER_URL: broker:9092
      TRANSACTIONS_TOPIC: queueing.transactions
      LEGIT_TOPIC: streaming.transactions.legit
      FRAUD_TOPIC: streaming.transactions.fraud 
```

Enter fullscreen mode Exit fullscreen mode

## 试用欺诈检测器

让我们尝试一下吧！和以前一样，让我们从启动 Docker Compose 开始，这一次将启动事务生成器和欺诈检测器:

```
$ docker-compose up 
```

Enter fullscreen mode Exit fullscreen mode

正如我们在测试生成器时所做的那样，我们可以通过启动两个`kafka-console-consumer`来验证检测器是否正确地消费、处理和产生事务——每个输出主题一个。

下面是这样做的命令:

```
docker-compose -f docker-compose.kafka.yml exec detector kafka-console-consumer --bootstrap-server localhost:9092 --topic streaming.transactions.legit
docker-compose -f docker-compose.kafka.yml exec detector kafka-console-consumer --bootstrap-server localhost:9092 --topic streaming.transactions.fraud 
```

Enter fullscreen mode Exit fullscreen mode

我们来考察一下这些题目的内容吧！

*   `streaming.transactions.legit`包含合法交易(金额低于 900 欧元),如预期:

```
...
{"source": "v16xO8Oj7s4u", "target": "QgvC9nMnBQMO", "amount": 231.67, "currency": "EUR"}
{"source": "wqduJdB5focL", "target": "sxKNYdlSQqQl", "amount": 229.57, "currency": "EUR"}
{"source": "VNWKUvsMOOWA", "target": "7wWynSlnuYpP", "amount": 752.4, "currency": "EUR"}
{"source": "bjt8hci7q8Wm", "target": "LobJtJJB3RDt", "amount": 410.42, "currency": "EUR"}
{"source": "E6NpRQcSJ3es", "target": "E9QeU7qfekKB", "amount": 659.23, "currency": "EUR"}
{"source": "IijVKIGtiGrc", "target": "eWVPvLGm8JLi", "amount": 846.07, "currency": "EUR"}
{"source": "yGfZ1Xa6k1r0", "target": "N5RvY7RO5sQF", "amount": 217.46, "currency": "EUR"}
...
^C
Processed a total of 13593 messages 
```

Enter fullscreen mode Exit fullscreen mode

*   `streaming.transactions.fraud`也不出所料，只包含可疑交易(金额在 900 欧元以上)!

```
...
{"source": "jwRMZyzGAsmG", "target": "i33smnlpnYtd", "amount": 980.05, "currency": "EUR"}
{"source": "WcmydQNdDxya", "target": "ykFPmUSysJuJ", "amount": 914.81, "currency": "EUR"}
{"source": "UuCOtfIqc4oU", "target": "PrD271ASM3mZ", "amount": 911.9, "currency": "EUR"}
{"source": "ZHdwSxcIi1gv", "target": "CNLyxBAHT2lW", "amount": 925.55, "currency": "EUR"}
{"source": "gQUtarjghjWx", "target": "ABKEhcJuYiEo", "amount": 945.14, "currency": "EUR"}
{"source": "NS2HA0HoMQPt", "target": "h989MZ4lcDqg", "amount": 901.25, "currency": "EUR"}
{"source": "0w22LoewVfYW", "target": "bUejK0tbzLT2", "amount": 955.0, "currency": "EUR"}
...
^C
Processed a total of 1519 messages 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，大约十分之一的邮件被标记为欺诈邮件。考虑到交易范围在 0-1000 欧元之间，触发点为 900 欧元，我们预计为 10%,因此一切似乎都很好！

## 后退一步

就这样，我们现在有了**一个有效的欺诈检测渠道**:

*   一个本地 Kafka 集群，作为一个中央流媒体平台。
*   向集群产生事务的**事务生成器**。
*   一个**欺诈检测器**，它处理交易，检测潜在的欺诈交易，并在两个独立的主题中产生结果。

你可能想知道— **下一步是什么？**

有许多方法可以改进或扩展这个系统。以下是一些想法:

*   实施更好的欺诈检测算法
*   向某处发送关于欺诈交易的警报或报告:电子邮件、通知、Slack bot —任何东西！

您可能也想知道——那么**部署**呢？嗯，我故意跳过了这一部分。虽然在生产中获得一组 Kafka 应用程序应该与您通常的工作流程没有任何不同，但它可能需要自己的最佳实践。另一方面，将 Kafka 集群投入生产是一个完全不同的话题(双关语)。

尽管我们已经实现了建立一个流媒体系统，我们甚至还没有触及到 T2 卡夫卡生态系统的皮毛。

你们当中已经熟悉 Kafka 生态系统的人可能会想:“KSQL 怎么样？”，“那卡夫卡溪流呢？”，“那么模式注册中心呢？。嗯，是的，这些都有，我们以后也许可以一窥究竟。

在本教程中，我想向您展示的是 Apache Kafka 的核心部分——主题、生产者和消费者，以及如何安排他们来构建现实世界的流媒体应用程序。

## 流尽万物

如果你已经阅读并和我一起建立了这个欺诈检测系统，那么恭喜你！您刚刚用 Apache Kafka 和 Python 构建了**您的第一个真实世界的流应用程序。**

我们可以为我们取得的成就感到自豪。我们结合了 Apache Kafka、Python 和 Docker 来解决一个 actuel 业务问题。

这只是一个介绍性的例子，但我希望你开始想象**像 Apache Kafka 这样的流媒体平台如何实现一个全新的系统和应用世界**。只要想想用 REST APIs 和老式的 HTTP 调用来构建这个系统会有多难。我的猜测是:不可能。

如果你很想知道接下来会发生什么，我有个好消息:我们才刚刚开始！👊

在下一篇文章中，我们将回到卡夫卡的核心思想。我们将尝试执行**范式转换**。阿帕奇卡夫卡都是关于**流数据**，所以我们需要从在请求中思考转移到在事件中思考**。**

在此之前，您可以在 [GitHub](https://github.com/florimondmanca/kafka-fraud-detector) 上找到完整的代码，所以请继续使用欺诈检测器，并继续关注更多的 Kafka 资料！💻

接下来:[突发新闻:一切都是事件！(溪流、卡夫卡和你)](https://dev.to/florimondmanca/breaking-news-everything-is-an-event-streams-kafka-and-you-2n9j)

## 保持联系！

如果你喜欢这篇文章，你可以在 Twitter 上找到我，获取更新、公告和新闻。🐤**