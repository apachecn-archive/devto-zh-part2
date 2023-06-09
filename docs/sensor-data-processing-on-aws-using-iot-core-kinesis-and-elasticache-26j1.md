# 使用物联网核心、Kinesis 和 ElastiCache 在自动气象站上处理传感器数据

> 原文：<https://dev.to/frosnerd/sensor-data-processing-on-aws-using-iot-core-kinesis-and-elasticache-26j1>

这篇博文是我的 AWS 系列的一部分:

*   [基础设施作为使用 Terraform 的代码管理 AWS](https://dev.to/frosnerd/infrastructure-as-code---managing-aws-with-terraform-i9o)
*   [使用 Lambda 和 API 网关在 AWS 上部署 HTTP API](https://dev.to/frosnerd/deploying-an-http-api-on-aws-using-lambda-and-api-gateway-g61)
*   [使用 Elastic Beanstalk 在 AWS 上部署 HTTP API](https://dev.to/frosnerd/deploying-an-http-api-on-aws-using-elastic-beanstalk-5dh7)
*   [部署 AWS RDS MySQL 实例并进行基准测试](https://dev.to/frosnerd/deploying-and-benchmarking-an-aws-rds-mysql-instance-2faf)
*   [AWS 中使用社交网络、SQS 和 Lambda 的事件处理](https://dev.to/frosnerd/event-handling-in-aws-using-sns-sqs-and-lambda-2ng)
*   [使用 Terraform 和 Travis CI 在 AWS 上连续交付](https://dev.to/frosnerd/continuous-delivery-on-aws-with-terraform-and-travis-ci-3914)
*   [**使用物联网核心、Kinesis 和 ElastiCache 在 AWS 上进行传感器数据处理**](#)
*   [使用 CloudWatch 监控 AWS Lambda 功能](https://dev.to/frosnerd/monitoring-aws-lambda-functions-with-cloudwatch-1nap)

# 简介

近年来，物联网成为一个热门话题。公司预测未来几年将会有数十亿的联网设备。物联网应用具有不同于传统软件项目的特征。应用程序在受限的硬件上运行，网络连接不可靠，来自许多不同传感器的数据需要近乎实时地可用。

随着廉价和可用的微处理器和微控制器的兴起，如 [Rasperry Pi](https://www.raspberrypi.org/) 和 [Arduino](https://www.arduino.cc/) 产品，从事物联网产品的门槛已经大大降低。而且软件和开发工具也成熟了。

2015 年 12 月，AWS 物联网开始全面上市。AWS IoT 是一个产品集合，用于管理物联网设备并将其连接到云。其[物联网核心](https://aws.amazon.com/iot-core/)产品作为切入点。物联网核心通过 [MQTT](https://en.wikipedia.org/wiki/MQTT) 接受数据，然后根据预先配置的规则处理并转发给其他 AWS 服务。

在这篇博文中，我们希望构建一个由物联网核心、Kinesis、Lambda、ElastiCache、Elastic Beanstalk 和 S3 支持的示例性传感器数据后端。目标是接受传感器数据，将其保存在 S3 桶中，同时在 web 上显示一个实时提要。该架构应该是可扩展的，所以我们可以添加更多的功能，如分析或通知后。

该员额的其余部分结构如下。首先，我们将介绍架构概述。之后，我们将深入研究实现。我们将省略关于 VPC 和网络部分以及弹性 Beanstalk 部署的细节，因为这在以前的帖子中已经讨论过了。我们正在结束讨论主要发现的博文。

# 建筑

[![architecture overview](img/0a454a09938bce8426c37bf0c89ee0e8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DQk3izBA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1zm11u6uwpo9vi3gureo.png)

物联网核心充当 MQTT 消息代理。它使用主题将消息从发布者路由到订阅者。每当有消息发布到某个主题时，所有订阅者都会收到关于该消息的通知。物联网核心允许我们使用*规则*高效地向其他 AWS 服务发送消息。一个规则对应于一个 SQL select 语句，该语句定义了何时触发规则，例如，针对某个主题的所有消息。

每个规则可以有多个关联的操作。操作定义了应该对所选邮件执行的操作。有许多[不同的动作](https://docs.aws.amazon.com/iot/latest/developerguide/iot-rule-actions.html)被支持，但是在这篇文章中我们将只使用消防软管和 Kinesis 动作。

消防软管动作将消息转发给 [Kinesis 消防软管传送流](https://aws.amazon.com/kinesis/data-firehose/)。Firehose 在配置的时间内收集消息，或者直到达到特定的批量大小，并将其保存到指定的位置。在我们的例子中，我们希望将消息作为小批量保存在 S3 桶中。

[Kinesis 数据流](https://aws.amazon.com/kinesis/data-streams/)用于将处理逻辑与数据摄取分离。这使我们能够由多个独立的消费者异步地消费来自流的消息。因为消息偏移量可以由每个消费者单独管理，所以我们还可以决定在下游失败的情况下重放某些消息。

主要的数据处理发生在我们的 Lambda 函数中。使用 Lambda 函数处理 Kinesis 数据流的一种便捷方式是将该流配置为[事件源](https://docs.aws.amazon.com/lambda/latest/dg/invocation-options.html)。我们将使用这种基于流的模型，因为 Lambda 会为您轮询流，当它检测到新记录时，会调用您的函数，并将新记录作为参数传递。可以将更多的消费者添加到数据流中，例如， [Akka Streams](https://github.com/StreetContxt/kcl-akka-stream) 应用程序允许对消息消化进行更细粒度的控制。

Lambda 函数将更新一个由[elastic cache](https://aws.amazon.com/elasticache)管理的 Redis 实例。在我们的示例中，我们将为每条记录增加一个消息计数器，并存储收到的最后一条消息。我们使用 Redis' [Pub/Sub](https://redis.io/topics/pubsub) 功能通知我们的 web 应用程序，该应用程序通过 WebSocket 连接更新所有客户端。

我们将使用内置于物联网核心的 MQTT 测试客户端来发布消息到我们的主题。作为一个展望，请找到最终结果的动画如下。让我们在下一节中一步一步地研究实现。

[![demo](img/4ed5f49fe5f5a19e2897238febf62d31.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4H-A2dVi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lr6mq7pw22ol3r7enzhw.gif)

# 实现

## 开发工具栈

为了开发该解决方案，我们使用了以下工具:

*   Terraform v0.11.7
*   SBT 1.0.4
*   Scala 2.12.6
*   IntelliJ + Scala 插件+ Terraform 插件

[源代码](https://github.com/FRosner/aws-iot-test)可以在 GitHub 上获得。现在让我们来看看每个组件的实现细节。

## 物联网核心

[![iot core](img/cbbf75dce9719af250605a187438a589.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PBhPMsH1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m38hfkc6svlxssfrt3yf.png)

使用物联网核心时，不需要设置。每个 AWS 帐户都能够使用 MQTT 代理作为开箱即用的完全托管的服务。所以我们唯一需要做的事情就是配置我们的主题规则和相应的转发消息到 Kinesis 和 Firehose 的动作。

```
resource "aws_iot_topic_rule" "rule" {
  name        = "${local.project_name}Kinesis"
  description = "Kinesis Rule"
  enabled     = true
  sql         = "SELECT * FROM 'topic/${local.iot_topic}'"
  sql_version = "2015-10-08"

  kinesis {
    role_arn    = "${aws_iam_role.iot.arn}"
    stream_name = "${aws_kinesis_stream.sensors.name}"
    partition_key = "$${newuuid()}"
  }

  firehose {
    delivery_stream_name = "${aws_kinesis_firehose_delivery_stream.sensors.name}"
    role_arn = "${aws_iam_role.iot.arn}"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于`topic/sensors`主题中的所有邮件，都将触发该规则。使用`newuuid()`作为 Kinesis 的分区键对我们的演示来说很好，因为我们将只有一个碎片。在一个高效的场景中，您应该考虑以一种符合您需求的方式选择分区键。

所使用的执行角色需要分别允许`kinesis:PutRecord`和`firehose:PutRecord`动作。这里我们两次使用同一个角色，但是我建议设置两个权限尽可能少的角色。

现在我们已经配置了规则，接下来让我们创建 Firehose 交付流和 Kinesis 数据流。

## 消防水带输送水流

[![firehose delivery stream](img/56ff5f27cd84169b689b306f5fef9118.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5IP2TiuH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2ekzesfjh5wmpi0zjqr8.png)

为了设置消防软管输送流，我们指定目的地类型(`s3`)并进行相应配置。由于我们在本系列中多次创建了 S3 存储桶，因此我们将在此省略存储桶资源。两个参数`buffer_size` (MB)和`buffer_interval` (s)控制在将分区持久化到 S3 之前，我们等待新数据到达的时间。

```
resource "aws_kinesis_firehose_delivery_stream" "sensors" {
  name        = "${local.project_name}-s3"
  destination = "s3"

  s3_configuration {
    role_arn        = "${aws_iam_role.firehose.arn}"
    bucket_arn      = "${aws_s3_bucket.sensor_storage.arn}"
    buffer_size     = 5
    buffer_interval = 60
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

执行角色需要拥有访问 S3 存储桶和 Kinesis 流的权限。请查找下面使用的政策文件。

```
{  "Version":  "2012-10-17",  "Statement":  [  {  "Effect":  "Allow",  "Action":  [  "s3:AbortMultipartUpload",  "s3:GetBucketLocation",  "s3:GetObject",  "s3:ListBucket",  "s3:ListBucketMultipartUploads",  "s3:PutObject"  ],  "Resource":  [  "${aws_s3_bucket.sensor_storage.arn}",  "${aws_s3_bucket.sensor_storage.arn}/*"  ]  },  {  "Effect":  "Allow",  "Action":  [  "kinesis:DescribeStream",  "kinesis:GetShardIterator",  "kinesis:GetRecords"  ],  "Resource":  "${aws_kinesis_stream.sensors.arn}"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

这应该包括我们的原始数据持久层。所有传入的消息都将被转储到 S3。接下来让我们关注 Kinesis 数据流，这对于数据处理非常重要。

## Kinesis 数据流

[![kinesis data stream](img/18a6d252fe7d127192630ae3564ae431.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_8oN9JLs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/36arcez7baeptkfyfmn5.png)

Kinesis 数据流将只有一个碎片，保留期为 24 小时。每个碎片支持一定的读写吞吐量，受限于每次请求的数量和大小。您可以通过向流中添加更多碎片并选择适当的分区键来进行扩展。我们将保留数据 24 小时，这包含在基础价格中。

```
resource "aws_kinesis_stream" "sensors" {
  name             = "${local.project_name}"
  shard_count      = 1
  retention_period = 24
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们仔细看看我们的 Lambda 函数。

## λ

[![lambda](img/3a11af95cb20809944817c7e73535b67.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1700QiHi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3mqzq5puoe1uxyqlizcd.png)

到目前为止，您应该已经熟悉如何创建 Lambda 处理程序了。这次我们将使用一个额外的库，它将允许 AWS 自动处理`KinesisEvent`输入的反序列化: [`aws-lambda-java-events`](https://github.com/aws/aws-lambda-java-libs/tree/master/aws-lambda-java-events) 。我们还得包括 [`amazon-kinesis-client`](https://github.com/awslabs/amazon-kinesis-client) 和 [`aws-java-sdk-kinesis`](https://github.com/aws/aws-sdk-java/tree/master/aws-java-sdk-kinesis) 。

到 Redis 的连接是使用 [`net.debasishg.redisclient`](https://github.com/debasishg/scala-redis) 包完成的。让我们先看看代码，然后一步一步来。

```
class Handler extends RequestHandler[KinesisEvent, Void] {

  val port = System.getenv("redis_port").toInt
  val url = System.getenv("redis_url")

  override def handleRequest(input: KinesisEvent, context: Context): Void = {
    val logger = context.getLogger
    val redis = new RedisClient(url, port)
    val recordsWritten = input.getRecords.asScala.map { record =>
      val data = new String(record.getKinesis.getData.array())
      redis.set("sensorLatest", data)
      redis.incr("sensorCount")
    }
    redis.publish(
      channel = "sensors",
      msg = "updated"
    )
    val successAndFailure = recordsWritten.groupBy(_.isDefined).mapValues(_.length)
    logger.log(s"Successfully processed: ${successAndFailure.getOrElse(true, 0)}")
    logger.log(s"Failed: ${successAndFailure.getOrElse(false, 0)}")
    null
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

事情是这样的:

*   读取`$redis_url`和`$redis_port`环境变量(错误处理省略)
*   对于每个输入的`KinesisEvent`，Lambda 函数将被调用。该事件包含自上次调用以来的记录列表。我们将在后面看到如何控制这部分。
*   连接到 ElastiCache Redis 实例。通过在请求处理方法之外设置连接来重用它可能是有意义的。我不确定线程安全以及 AWS Lambda 如何处理对象创建。
*   对于每个消息，更新最新的消息值并增加计数器。在 Lambda 中本地聚合所有记录并用整批结果更新 Redis 会更有效，但我懒得这么做。
*   向`sensors`通道发布新数据已经到达，以便通知所有客户端。

像往常一样，我们必须定义我们的 Lambda 资源。Redis 连接细节将通过环境变量传递。

```
resource "aws_lambda_function" "kinesis" {
  function_name    = "${local.project_name}"
  filename         = "${local.lambda_artifact}"
  source_code_hash = "${base64sha256(file(local.lambda_artifact))}"
  handler          = "de.frosner.aws.iot.Handler"
  runtime          = "java8"
  role             = "${aws_iam_role.lambda_exec.arn}"
  memory_size      = 1024
  timeout          = 5

  environment {
    variables {
      redis_port = "${aws_elasticache_cluster.sensors.port}"
      redis_url  = "${aws_elasticache_cluster.sensors.cache_nodes.0.address}"
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后但同样重要的是，我们告诉 Lambda 监听 Kinesis 事件，让它轮询新消息。我们选择 [`LATEST`](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_GetShardIterator.html#API_GetShardIterator_RequestSyntax) 分片迭代器，这相当于将偏移量总是移动到最新的数据，每条消息只消耗一次。我们也可以决定消费所有记录(`TRIM_HORIZON`)或者从给定的时间戳开始(`AT_TIMESTAMP`)。

批处理大小控制一个 Lambda 调用处理的最大消息数量。增加它对吞吐量有积极的影响，而另一方面，小批量可能会导致更好的延迟。这是我们事件源映射的 Terraform 资源定义。

```
resource "aws_lambda_event_source_mapping" "event_source_mapping" {
  batch_size        = 10
  event_source_arn  = "${aws_kinesis_stream.sensors.arn}"
  enabled           = true
  function_name     = "${aws_lambda_function.kinesis.id}"
  starting_position = "LATEST"
} 
```

Enter fullscreen mode Exit fullscreen mode

为了将数据推送到 web 层，我们将在下一节创建我们的 ElastiCache Redis 实例。

## 弹性缓存重定向

[![redis](img/807fc4a6b57443dcff0f06a162e0cf25.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sGjmNAHu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ucfuhkcnitkw0gvd4d6h.png)

我们将使用运行 Redis 4.0 的单个`cache.t2.micro`实例。与 RDS 类似，我们也可以传递`apply_immediately`标志来强制更新，即使在维护窗口之外。

```
resource "aws_elasticache_cluster" "sensors" {
  cluster_id           = "${local.project_name}"
  engine               = "redis"
  node_type            = "cache.t2.micro"
  num_cache_nodes      = 1
  parameter_group_name = "default.redis4.0"
  port                 = "${var.redis_port}"
  security_group_ids   = ["${aws_security_group.all.id}"]
  subnet_group_name    = "${aws_elasticache_subnet_group.private.name}"
  apply_immediately    = true
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！剩下的只是 UI 的弹性 Beanstalk 应用程序。

## 弹性豆茎 Web UI

[![elastic beanstalk web ui](img/aa861c68235e3b57b2a542f747cd333a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--j2UXLNWX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a7nsvbk85ro58kcy8l1u.png)

弹性 Beanstalk 应用程序由前端和后端组成。前端是一个包含 JavaScript 和 CSS 的 HTML 文件。那里没什么特别的。后端是用 Scala 编写的，利用了我们已经在 Lambda 函数中使用的 Redis 库，以及 Akka HTTP 来服务静态文件和处理 WebSocket 连接。

### 前端

前端将提供三个输出:消息计数、最后一条消息以及它从服务器接收最后一次更新的时间。下面你会发现一个用户界面和 HTML 代码的截图。

[![frontend](img/ddd6a0f707ada265847412bed889e788.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RUmW2XlB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6plgk0br1ii2xacktrbm.png)T3】

```
<html>
<head>
    Sensor Data Example
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="http://fargo.io/code/jquery-1.9.1.min.js"></script>
    <link href="http://fargo.io/code/ubuntuFont.css" rel="stylesheet" type="text/css">
    <script src="main.js"></script>
</head>
<body>
<div>
    <p>Message count: <span id="messageCount"></span></p>
    <p>Last message: <span id="lastMessage"></span></p>
    <p>Last update: <span id="lastUpdate"></span></p>
</div>
<script>
$(document).ready(WebSocketTest);
</script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript 部分也相当简单。我们将使用内置的 WebSocket 支持，并为`onopen`、`onmessage`和`onclose`事件提供处理程序。`onmessage`事件将解析接收到的数据并填充适当的文本字段。注意，我们必须使用`window.location`来构建 WebSocket URL，因为它必须是我所知道的所有浏览器中的绝对 URL。

```
var loc = window.location, protocol;
if (loc.protocol === "https:") {
    protocol = "wss:";
} else {
    protocol = "ws:";
}
var socketUrl = protocol + "//" + loc.host + loc.pathname + "ws";

function WebSocketTest() {
  if ("WebSocket" in window) {
    console.log("Connecting to " + socketUrl);
    var ws = new WebSocket(socketUrl);

    ws.onopen = function() {
      console.log("Connection established");
    };

    ws.onmessage = function (evt) {
      var msg = JSON.parse(evt.data);
      console.log("Message received: " + msg);
      $("#lastUpdate").text(new Date());
      $("#lastMessage").text(msg.latest);
      $("#messageCount").text(msg.count);
    };

    ws.onclose = function() {
      console.log("Connection closed");
    };
  } else {
     console.error("WebSocket not supported by your browser!");
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 后端

后端由一个 Akka HTTP web 服务器和两个 Redis 连接组成。这里我们必须使用两个连接，因为 Redis 不允许同时对发布/订阅和普通键值操作使用同一个连接。

让我们看看代码。和往常一样，如果需要的话，您需要有一个 actor 系统、actor 具体化器和执行上下文。出于简洁的原因，我们将省略这一点。下面的清单说明了如何设置支持 WebSocket 的 HTTP 服务器。我们路由两条路径，一条用于 WebSocket 连接，另一条用于静态 HTML 文件，其中包含所有的 JavaScript 和 CSS 内联。我们忽略所有传入的消息，并将来自 Redis 的消息转发给所有客户端。接口和端口都将通过弹性豆茎。

```
val route =
  path("ws") {
    extractUpgradeToWebSocket { upgrade =>
      complete(upgrade.handleMessagesWithSinkSource(Sink.ignore, redisSource))
    }
  } ~ path("") {
    getFromResource("index.html")
  }

val interface = Option(System.getenv("INTERFACE")).getOrElse("0.0.0.0")
val port = System.getenv("PORT").toInt
val bindingFuture = Http().bindAndHandle(route, interface, port) 
```

Enter fullscreen mode Exit fullscreen mode

下一个任务是定义 Redis 源。Redis 发布/订阅 API 需要一个回调函数，为订阅通道中的每条消息调用该函数。这与开箱即用的 Akka 流不兼容，因此我们必须使用一个小技巧将 Redis 消息转换成一个`Source`对象。下面的清单说明了 Redis 源的创建以及通道订阅。

```
val redis_port = System.getenv("redis_port").toInt
val redis_url = System.getenv("redis_url")
val redis = new RedisClient(redis_url, redis_port)
val redisPubSub = new RedisClient(redis_url, redis_port)

val (redisActor, redisSource) =
  Source.actorRef[String](1000, OverflowStrategy.dropTail)
    .map(s => TextMessage(s))
    .toMat(BroadcastHub.sink[TextMessage])(Keep.both)
    .run()

redisPubSub.subscribe("sensors") {
  case M(channel, message) =>
    val latest = redis.get("sensorLatest")
    val count = redis.get("sensorCount")
    redisActor ! s"""{ "latest": "${latest.getOrElse("0")}", "count": "${count.getOrElse("0")}" }"""
  case S(channel, noSubscribed) => println(s"Successfully subscribed to channel $channel")
  case other => println(s"Ignoring message from redis: $other")
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`Source.actorRef`和`BroadcastHub.sink`的组合来创建 Redis 源。源参与者将把它收到的每条消息发送到流中。我们配置了一个 1000 条消息的缓冲区，并丢弃了最年轻的元素，以便在溢出时为新的元素腾出空间。在订阅回调中，我们可以向 Redis 查询最新数据，然后向 Redis 参与者发送一个 JSON 对象。

广播集线器接收器发出一个源，我们可以将其插入 WebSocket 接收器，以生成处理传入 WebSocket 消息的流。因为我们两者都需要，参与者和源，我们将保留这两个物化的值。

现在我们可以构建我们的 fat jar 并上传到 S3。因为它基本上与[弹性豆茎柱](https://dev.to/frosnerd/deploying-an-http-api-on-aws-using-elastic-beanstalk-5dh7)中的程序相同，所以在这一点上我们不打算详述。接下来我们来看看 Terraform 资源。

### 地形

首先，我们必须参考 S3 桶内的脂肪罐。因为在我们可以使用 SBT 发布 jar 之前，bucket 必须存在，所以 Terraform 部署需要分两个阶段进行。首先，我们只创建工件桶并运行`sbt webui/publish`，然后我们部署剩余的基础设施。

```
resource "aws_s3_bucket" "webui" {
  bucket        = "${local.project_name}-webui-artifacts"
  acl           = "private"
  force_destroy = true
}

data "aws_s3_bucket_object" "application-jar" {
  bucket = "${aws_s3_bucket.webui.id}"
  key    = "de/frosner/${local.webui_project_name}_2.12/${var.webui_version}/${local.webui_project_name}_2.12-${var.webui_version}-assembly.jar"
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们可以定义 Elastic Beanstalk 应用程序、环境和版本。此时，我们将省略所有与网络和执行相关的设置。Redis 连接细节将作为环境变量传递。为了通过负载平衡器启用 WebSocket 通信，我们必须使用`LoadBalancerPortProtocol`设置将协议从 HTTP 切换到 TCP。在正确的设置中，你还必须调整 [nginx 配置](https://github.com/mitchellsimoens/Simple-WebSocket/blob/master/.ebextensions/files.config)，否则连接可能会不规则地终止。

```
resource "aws_elastic_beanstalk_application" "webui" {
  name = "${local.project_name}"
}

resource "aws_elastic_beanstalk_environment" "webui" {
  name                = "${local.project_name}"
  application         = "${aws_elastic_beanstalk_application.webui.id}"
  solution_stack_name = "64bit Amazon Linux 2018.03 v2.7.1 running Java 8"

  setting {
    namespace = "aws:elasticbeanstalk:application:environment"
    name      = "redis_url"
    value     = "${aws_elasticache_cluster.sensors.cache_nodes.0.address}"
  }
  setting {
    namespace = "aws:elasticbeanstalk:application:environment"
    name      = "redis_port"
    value     = "${aws_elasticache_cluster.sensors.port}"
  }
  setting {
    namespace = "aws:elb:loadbalancer"
    name      = "LoadBalancerPortProtocol"
    value     = "TCP"
  }
}

resource "aws_elastic_beanstalk_application_version" "default" {
  name        = "${local.webui_assembly_prefix}"
  application = "${aws_elastic_beanstalk_application.webui.name}"
  description = "application version created by terraform"
  bucket      = "${aws_s3_bucket.webui.id}"
  key         = "${data.aws_s3_bucket_object.application-jar.key}"
}

output "aws_command" {
  value = "aws elasticbeanstalk update-environment --application-name ${aws_elastic_beanstalk_application.webui.name} --version-label ${aws_elastic_beanstalk_application_version.default.name} --environment-name ${aws_elastic_beanstalk_environment.webui.name}"
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！现在我们已经定义了所有需要的资源，除了我们有意跳过的网络部分。注意，不能使用默认的 VPC、子网和安全组，否则 Lambda 和 Elastic Beanstalk EC2 实例都不能连接到 ElastiCache。接下来让我们看看我们的宝宝在行动！

## 部署和使用

如前所述，部署分多个步骤完成。首先，我们只为上传弹性 Beanstalk 工件创建了 S3 桶。然后，我们调配剩余的基础架构。由于 Terraform 目前不支持部署 Elastic Beanstalk 应用程序版本，我们将稍后执行生成的 AWS CLI 命令。

```
cd terraform && terraform apply -auto-approve -target=aws_s3_bucket.webui; cd -
sbt kinesis/assembly && sbt webui/publish && cd terraform && terraform apply -auto-approve; cd -
cd terraform && $(terraform output | grep 'aws_command' | cut -d'=' -f2) && cd - 
```

Enter fullscreen mode Exit fullscreen mode

完成后，我们可以打开 Elastic Beanstalk 环境 URL 来查看 UI。如果我们分配了一个 DNS 名称，我们可以使用那个名称。然后，我们在另一个浏览器选项卡中打开 AWS 物联网控制台，导航到*测试*页面。在这里，我们滚动到*发布*部分，输入`topic/sensors`作为主题，就可以开始发布 MQTT 消息了。

[![demo](img/4ed5f49fe5f5a19e2897238febf62d31.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4H-A2dVi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lr6mq7pw22ol3r7enzhw.gif)

# 结论

在这篇博文中，我们看到了如何使用物联网核心规则将 MQTT 消息路由到 Kinesis 流。Kinesis Firehose 传送流是一种自动批量保存数据流的便捷方式。作为 Lambda 事件源的 Kinesis 数据流对如何处理数据提供了更细粒度的控制。使用 ElastiCache Redis 作为中间存储层和通知服务，我们使客户能够获得传感器的近实时更新。

看一下我们构建的示例解决方案，有一些事情我们可以做得不同。我们不必同时为 Firehose 交付流和 Kinesis 数据流付费，我们可以只使用数据流并添加一个自定义轮询消费者，该消费者批量保存数据，可能会执行一些基本的格式转换，如以压缩的列存储格式写入。

虽然将 Kinesis 配置为 Lambda 的事件源效果很好，但如果 Lambda 函数持续运行，成本可能会变得有点高。例如，在这种情况下，使用部署在 ECS EC2 中的自定义消费者是值得的。

使用 Redis 作为中间存储层只是众多选择之一。为您的问题选择正确的数据存储并不简单。Redis 很快，因为它在内存中。如果您需要一个更加持久和可伸缩的数据库，DynamoDB 也是一个选择。客户端可以通过 [DynamoDB 流](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html)订阅 DynamoDB 表中的更改。也许你还想添加 [ElasticSearch](https://www.elastic.co/products/elasticsearch) 或 [Graphite](https://github.com/graphite-project/whisper) 作为消费者。

你怎么想呢?您是否已经在某个项目中使用了 AWS IoT？您是否也使用 Terraform 实现了设备管理的自动化？请在下面评论！

* * *

由 Wilgengebroed 在 Flickr 上制作的封面图片-已裁剪，并从物联网中删除了 author.jpg 的签名，CC 2.0，[https://commons.wikimedia.org/w/index.php?curid=32745645](https://commons.wikimedia.org/w/index.php?curid=32745645)