# AWS 中使用 SNS、SQS 和 Lambda 的事件处理

> 原文：<https://dev.to/frosnerd/event-handling-in-aws-using-sns-sqs-and-lambda-2ng>

这篇博文是我的 AWS 系列的一部分:

*   [基础设施作为使用 Terraform 的代码管理 AWS](https://dev.to/frosnerd/infrastructure-as-code---managing-aws-with-terraform-i9o)
*   [使用 Lambda 和 API 网关在 AWS 上部署 HTTP API](https://dev.to/frosnerd/deploying-an-http-api-on-aws-using-lambda-and-api-gateway-g61)
*   [使用 Elastic Beanstalk 在 AWS 上部署 HTTP API](https://dev.to/frosnerd/deploying-an-http-api-on-aws-using-elastic-beanstalk-5dh7)
*   [部署 AWS RDS MySQL 实例并进行基准测试](https://dev.to/frosnerd/deploying-and-benchmarking-an-aws-rds-mysql-instance-2faf)
*   [**使用 SNS、SQS 和λ**的 AWS 中的事件处理 T3】](#)
*   [使用 Terraform 和 Travis CI 在 AWS 上连续交付](https://dev.to/frosnerd/continuous-delivery-on-aws-with-terraform-and-travis-ci-3914)
*   [使用物联网核心、Kinesis 和 ElastiCache 在 AWS 上处理传感器数据](https://dev.to/frosnerd/sensor-data-processing-on-aws-using-iot-core-kinesis-and-elasticache-26j1)
*   [使用 CloudWatch 监控 AWS Lambda 功能](https://dev.to/frosnerd/monitoring-aws-lambda-functions-with-cloudwatch-1nap)

# 简介

在反应式的、消息驱动的应用程序中，分离消息的生产者和消费者是至关重要的。结合发布/订阅(pub/sub)和排队组件，我们能够构建弹性的、可伸缩的和容错的应用架构。AWS 提供了各种实现发布/订阅或排队的组件。

在这篇文章中，我们想了解 AWS 上两个简单但功能强大的事件和消息处理组件:简单通知服务(SNS)和简单队列服务(SQS)。

我们的目标是开发一个事件管道，每当有人上传图片到 S3 桶时，它就向 Slack 通道发送一条消息。出于演示目的，我们还将把事件存储在一个队列中进行异步处理。该架构涉及 S3 事件通知、SNS 主题、SQS 队列和向 Slack 通道发送消息的 Lambda 函数。这是最终结果的动画。

[![notification](../Images/d9d4bd4aa28cd498250cbd3bf2c5e17a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w33xX2Ey--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/3427394/41991790-023da6e6-7a47-11e8-957b-9990c3683eed.gif)

该职位的其余部分结构如下。首先将有一个架构概述。然后，像往常一样，我们将一步一步地详细介绍如何使用 Terraform 进行设置。我们通过讨论主要发现来结束这篇文章。

# 建筑

让我们看看高层架构。当一个客户端上传一个图片到配置好的 S3 桶时，一个 S3 事件通知将向 SNS 发出，在各自的主题中发布事件。该主题将有两个订阅者:一个 SQS 队列和一个 Lambda 函数。

SQS 队列存储用于异步处理的事件，例如缩略图生成或图像分类。Lambda 函数解析事件，并向松弛通道发送通知消息。在这篇博文的范围内，我们不打算讨论异步处理部分。由于发布和订阅与 SNS 的分离，我们可以自由地为以后的活动添加更多的消费者。

[![architecture overview](../Images/372e3e13f875acd9f7cebe1928335bcb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7vQx7ZYZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7aspcgbyc4wgp2iykwmp.png)

让我们详细看看各个组件。S3 用桶来组织物品。在一个存储桶中，您可以通过键引用单个对象。向 S3 上传文件可以通过 [AWS 控制台](https://s3.console.aws.amazon.com/s3/)、 [AWS CLI](https://docs.aws.amazon.com/cli/latest/reference/s3/) 完成，也可以直接通过 [S3 API](https://docs.aws.amazon.com/AmazonS3/latest/API/Welcome.html) 完成。

在这篇文章中，我们将使用 CLI 上传。控制台和 CLI 都工作得相当顺利，因为它们为您处理与 S3 的所有低级通信。如果您正在使用 S3 API，并且您的 bucket 不是公开可写的，那么您必须使用 [AWS Signature Version 4](https://docs.aws.amazon.com/AmazonS3/latest/API/sig-v4-authenticating-requests.html) 手动验证您的请求。

S3 允许配置[事件通知](https://docs.aws.amazon.com/AmazonS3/latest/dev/NotificationHowTo.html)。可以基于对象创建或删除来创建事件，以及在冗余减少的对象丢失时发出通知。您可以选择向 SNS 主题、SQS 队列或 Lambda 函数发送事件。

在我们的例子中，我们将把事件发送到 SNS，然后允许感兴趣的应用程序订阅。这被称为消息传递[扇出模式](https://aws.amazon.com/blogs/compute/messaging-fanout-pattern-for-serverless-architectures-using-amazon-sns/)。通过使用 SNS 作为中间代理，我们将发布和订阅分离，而不是直接向各方发送事件。

SNS 是一个简单的发布/订阅服务，围绕*主题*进行组织。主题将一组订阅者可能感兴趣的相同类型的消息组合在一起。如果有新消息发布到某个主题，SNS 会通知所有订阅者。您可以配置传递策略，包括最大接收速率和重试延迟的配置。

目标是在我们的 S3 存储桶中发送一个关于对象创建的 Slack 消息。我们通过订阅 SNS 主题的 Lambda 函数来实现这一点。在调用时，Lambda 函数将解析和检查事件通知，提取相关信息，并将其转发给预先配置的 Slack webhook。

我们还将为该主题订阅一个 SQS 队列，存储事件以便通过另一个 Lambda 函数或长时间运行的轮询服务进行异步处理。下一节将解释如何实现这个架构。

# 实现

## 开发工具栈

为了开发该解决方案，我们使用了以下工具:

*   Terraform v0.11.7
*   SBT 1.0.4
*   Scala 2.12.6
*   IntelliJ + Scala 插件+ Terraform 插件

[源代码](https://github.com/FRosner/sns-sqs-test)可以在 GitHub 上获得。现在让我们来看看每个组件的实现细节。

## S3 斗

[![s3 bucket architecture](../Images/3241f8a5be7bc63897902090c7015181.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--am4U_6C8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mbt8wqcja0cjrzs2oj7n.png)

首先，我们将创建 S3 桶，我们可以上传图片。我们需要提供一个 bucket 名称和一个 ACL。这次 ACL 将会是`public-read`,因为我们希望人们能够公开他们的图片，但上传时需要认证。`force-destroy`选项允许 Terraform 销毁桶，即使它不是空的。

```
variable "aws_s3_bucket_upload_name" {
  default = "sns-sqs-upload-bucket"
}

resource "aws_s3_bucket" "upload" {
  bucket = "${var.aws_s3_bucket_upload_name}"
  acl    = "public-read"
  force_destroy = true
} 
```

Enter fullscreen mode Exit fullscreen mode

## SNS 话题

[![SNS topic architecture](../Images/aad80d87644a8287d67fabbc2f835b83.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KijSeKTz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wpa8wom8vit6rmnoqp4o.png)

接下来，让我们创建 SNS 主题。要创建 SNS 主题，我们只需提供一个名称。

```
resource "aws_sns_topic" "upload" {
  name = "sns-sqs-upload-topic"
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们不允许任何人发布消息，单独的主题是没有用的。为了做到这一点，我们为主题附加了一个策略，允许我们的 bucket 资源对主题执行`SNS:Publish`操作。

```
resource "aws_sns_topic_policy" "upload" {
  arn = "${aws_sns_topic.upload.arn}"

  policy = "${data.aws_iam_policy_document.sns_upload.json}"
}

data "aws_iam_policy_document" "sns_upload" {
  policy_id = "snssqssns"
  statement {
    actions = [
      "SNS:Publish",
    ]
    condition {
      test = "ArnLike"
      variable = "aws:SourceArn"

      values = [
        "arn:aws:s3:::${var.aws_s3_bucket_upload_name}",
      ]
    }
    effect = "Allow"
    principals {
      type = "AWS"
      identifiers = [
        "*"]
    }
    resources = [
      "${aws_sns_topic.upload.arn}",
    ]
    sid = "snssqssnss3upload"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## S3 事件通知

[![S3 event notification architecture](../Images/01b922266c01a9aae44c76af398c0143.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--d0JElOTk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/muj2bboalqwlr7p7tmny.png)

定义了 SNS 主题和 S3 时段资源后，我们可以通过创建将发布到主题的 S3 时段通知来组合它们。我们可以控制我们想要得到通知的[事件](https://docs.aws.amazon.com/AmazonS3/latest/dev/NotificationHowTo.html#notification-how-to-event-types-and-destinations)。在我们的例子中，我们对所有的对象创建事件感兴趣。我们还可以指定可选的过滤器，例如，在这种情况下，只通知`*.jpeg`文件。

```
resource "aws_s3_bucket_notification" "upload" {
  bucket = "${aws_s3_bucket.upload.id}"

  topic {
    topic_arn     = "${aws_sns_topic.upload.arn}"
    events        = ["s3:ObjectCreated:*"]
    filter_suffix = ".jpeg"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## SQS 队列

[![SQS queue architecture](../Images/d6907e283669dcd87711278addc3c066.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wZnYu1d4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lr04pqwsaqmtgkcql05p.png)

SQS 队列的创建方式类似。我们必须提供队列的名称和允许 SNS 向队列发送消息的策略。

```
resource "aws_sqs_queue" "upload" {
  name = "sns-sqs-upload"
} 
```

Enter fullscreen mode Exit fullscreen mode

```
resource "aws_sqs_queue_policy" "test" {
  queue_url = "${aws_sqs_queue.upload.id}"
  policy = "${data.aws_iam_policy_document.sqs_upload.json}"
}

data "aws_iam_policy_document" "sqs_upload" {
  policy_id = "snssqssqs"
  statement {
    actions = [
      "sqs:SendMessage",
    ]
    condition {
      test = "ArnEquals"
      variable = "aws:SourceArn"

      values = [
        "${aws_sns_topic.upload.arn}",
      ]
    }
    effect = "Allow"
    principals {
      type = "AWS"
      identifiers = [
        "*"]
    }
    resources = [
      "${aws_sqs_queue.upload.arn}",
    ]
    sid = "snssqssqssns"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## SQS 订阅

[![SQS subscription architecture](../Images/5358e92ce1ec999cfedc9f39d4f66baf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--otazN0CU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ggqipqjgq460w7j0klsx.png)

接下来，我们需要为队列订阅主题。SNS 话题订阅支持[多协议](https://docs.aws.amazon.com/sns/latest/api/API_Subscribe.html) : `http`、`https`、`email`、`email-json`、`sms`、`sqs`、`application`、`lambda`。在这种情况下，我们将使用`sqs`协议，并提供主题和队列端点。

```
resource "aws_sns_topic_subscription" "sqs" {
  topic_arn = "${aws_sns_topic.upload.arn}"
  protocol  = "sqs"
  endpoint  = "${aws_sqs_queue.upload.arn}"
} 
```

Enter fullscreen mode Exit fullscreen mode

## 松紧网钩

[![slack webhook architecture](../Images/b27b95f6d3d3edc35adbf19b760de05d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bSJ71Aqf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xpxn3s06q1frpcyxaiww.png)

在我们编写 Lambda 函数并订阅 SNS 主题之前，我们将创建 Slack webhook。在松弛状态下使用引入的网钩分四步完成:

1.  创建一个 Slack 应用程序。Slack 应用在你的工作空间中表现得像一个技术用户。
2.  在你的应用中启用传入 webhooks。
3.  创建新的传入 webhook。你会收到网页挂钩网址。
4.  使用 webhook URL 通过 HTTP POST 发送消息。

完成这些步骤后，您将在 [Slack 应用概述页面](https://api.slack.com/apps)中看到您的应用和配置的 webhook。它可能看起来像这样。

[![slack app](../Images/c6c728ff45ce4889d88dca53c0805585.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--P-zCUeDh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/758gv2dii97hq6l2761n.png)

[![slack webhook](../Images/9f319ec477a071c79b2dfaf6f01bbe93.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yBXbJk3A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9ke3j9qdmabujyz65igx.png)

## λ函数

[![lambda architecture](../Images/45e4b2189a991dee471f1cd0d435b9b5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZMzYJjRL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m5fmm2vfx2l5aameupy6.png)

### 报文格式

我们可以使用 webhook URL 来创建 Lambda 函数。该功能将接收包装在 SNS 通知中的 S3 通知。两者都以 JSON 格式发送，但是 S3 通知作为 JSON 字符串存储在`.Records.Sns.Message`字段中，并且也必须被解析。这是一个 SNS 通知包装消息的示例。

```
{  "Records":  [  {  "EventSource":  "aws:sns",  "EventVersion":  "1.0",  "EventSubscriptionArn":  "arn:aws:sns:eu-central-1:195499643157:sns-sqs-upload-topic:c7173bbb-8dda-47f6-9f54-a6aa81f65aac",  "Sns":  {  "Type":  "Notification",  "MessageId":  "10a7c00e-af4b-5d93-9459-93a0604d93f5",  "TopicArn":  "arn:aws:sns:eu-central-1:195499643157:sns-sqs-upload-topic",  "Subject":  "Amazon S3 Notification",  "Message":  "<inner_message>",  "Timestamp":  "2018-06-28T11:55:50.578Z",  "SignatureVersion":  "1",  "Signature":  "sTuBzzioojbez0zGFzdk1DLiCmeby0VuSdBvg0yS6xU+dKOk3U8iFUzbS1ZaNI6oZp+LHhehDziaMkTHQ7qcLBebu9uTI++mGcEhlgz+Ns0Dx3mKXyMTZwEcNtwfHEblJPjHXRsuCQ36RuZjByfI0pc0rsISxdJDr9WElen4U0ltmbzUJVpB22x3ELqciEDRipcpVjZo+V2J8GjdCvKu4uFV6RW3cKDOb91jcPc1vUnv/L6Q1gARIUFTbeUYvLbbIAmOe5PiAT2ZYaAmzHKvGOep/RT+OZOA4F6Ro7pjY0ysFpvvaAp8QKp4Ikj40N9lVKtk24pW+/7OsQMUBGOGoQ==",  "SigningCertUrl":  "https://sns.eu-central-1.amazonaws.com/SimpleNotificationService-ac565b8b1a6c5d002d285f9598aa1d9b.pem",  "UnsubscribeUrl":  "https://sns.eu-central-1.amazonaws.com/?Action=Unsubscribe&SubscriptionArn=arn:aws:sns:eu-central-1:195499643157:sns-sqs-upload-topic:c7173bbb-8dda-47f6-9f54-a6aa81f65aac",  "MessageAttributes":  {}  }  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

在`<inner_message>`部分中，您将找到实际的 S3 通知，它可能看起来像这样:

```
{  "Records":  [  {  "eventVersion":  "2.0",  "eventSource":  "aws:s3",  "awsRegion":  "eu-central-1",  "eventTime":  "2018-06-28T11:55:50.528Z",  "eventName":  "ObjectCreated:Put",  "userIdentity":  {  "principalId":  "AWS:AIDAI3EXAMPLEEXAMP"  },  "requestParameters":  {  "sourceIPAddress":  "xxx.yyy.zzz.qqq"  },  "responseElements":  {  "x-amz-request-id":  "0A8A0DA78EF73966",  "x-amz-id-2":  "/SD3sDpP1mcDc6pC61573e4DAFSCnYoesZxeETb4MV3PpVgT4ud8sw0dMrnWI9whB3RYhwGo+8A="  },  "s3":  {  "s3SchemaVersion":  "1.0",  "configurationId":  "tf-s3-topic-20180628113348955100000002",  "bucket":  {  "name":  "sns-sqs-upload-bucket",  "ownerIdentity":  {  "principalId":  "A2OMJ1OL5PYOLU"  },  "arn":  "arn:aws:s3:::sns-sqs-upload-bucket"  },  "object":  {  "key":  "3427394.jpeg",  "size":  25044,  "eTag":  "a3cf1dabef657a65a63a270e27312ddc",  "sequencer":  "005B34CCC64D9E046E"  }  }  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

最有趣的部分在`s3`对象中，它保存了关于 S3 桶和已经上传的对象的信息。我确信 [AWS Java SDK](https://aws.amazon.com/sdk-for-java/) 有一些代表这些信息的类，但是对于这篇博文，我决定用 [circe](https://circe.github.io/circe/) 手动解码我感兴趣的部分。

### 源代码

Lambda 函数将使用与我们在之前使用的[相同的](https://dev.to/frosnerd/deploying-an-http-api-on-aws-using-lambda-and-api-gateway-g61) [`RequestStreamHandler`](https://github.com/aws/aws-lambda-java-libs/blob/master/aws-lambda-java-core/src/main/java/com/amazonaws/services/lambda/runtime/RequestStreamHandler.java) 。这个类是 [`aws-lambda-java-libs`](https://github.com/aws/aws-lambda-java-libs) 的一部分，只提供原始的输入和输出流，把序列化和反序列化留给我们。下面是源代码:

```
class Handler extends RequestStreamHandler {
  override def handleRequest(input: InputStream,
                             output: OutputStream,
                             context: Context): Unit = {
    val hookUrl = System.getenv("hook_url")
    val inputJsonString = Source.fromInputStream(input).mkString
    val processingResult = for {
      notification <- decodeNotification(inputJsonString)
      message <- decodeMessage(notification)
    } yield {
      implicit val backend = HttpURLConnectionBackend()
      sttp
        .post(Uri(java.net.URI.create(hookUrl)))
        .contentType("application/json")
        .body(SlackMessage(messageText(notification, message)).asJson.noSpaces)
        .send()
    }

    val out = new PrintStream(output)
    processingResult match {
      case Right(response) => out.print(s"Response from hook: ${response.code}")
      case Left(error)     => out.print(s"Failed: $error")
    }
    out.close()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们从`$hook_url`环境变量中提取钩子 URL。出于可读性的原因，这里省略了错误处理和日志记录。然后我们从输入流中读取通知 JSON 字符串，并分两步解析它，因为我懒得提供自定义的反序列化格式。

如果解析成功，我们将向挂钩 URL 发送一个 HTTP POST 请求。Slack 期望主体是一个至少有一个`text`字段的 JSON。`SlackMessage`是一个捕获这一点的 case 类。在我们的例子中，我们将基于 S3 桶和对象键构造一个消息文本。要将我们的消息发送到通道，我们必须使用以下主体:

```
s"""
{
  "text": "Someone uploaded ${s3.`object`.key} to ${s3.bucket.name}."
}
""" 
```

Enter fullscreen mode Exit fullscreen mode

Lambda 函数实际上并不需要返回任何东西，但是我们将返回一个可读的字符串消息，指示钩子是否响应或者 SNS 消息的解析是否失败。

现在我们只需要再次使用 [`sbt-assembly`](https://github.com/sbt/sbt-assembly) 插件将 Lambda 处理程序打包到一个胖`jar`文件中。运行`sbt assembly`之后，工件可以使用 Terraform 上传到 AWS Lambda。

### 地形

在创建 Lambda 函数之前，我们必须为执行创建一个 IAM 角色。然后，我们可以创建 Lambda 函数本身，并为 SNS 通知设置权限，以便能够调用我们的 Lambda 函数。首先是 IAM 角色:

```
resource "aws_iam_role" "lambda_exec" {
  name = "sns-sqs-slack-lambda"

  assume_role_policy = <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": "sts:AssumeRole",
      "Principal": {
        "Service": "lambda.amazonaws.com"
      },
      "Effect": "Allow",
      "Sid": ""
    }
  ]
}
EOF
} 
```

Enter fullscreen mode Exit fullscreen mode

为了让 Terraform 代码更容易重用，我们将引入两个变量:工件版本和 Slack webhook URL。我将通过我的本地 [`terraform.tfvars`](https://www.terraform.io/intro/getting-started/variables.html#from-a-file) 文件传递 webhook URL。

```
variable "slack_lambda_version" {
  type = "string"
  default = "0.1-SNAPSHOT"
}

locals {
  slack_lambda_artifact = "../slack/target/scala-2.12/sns-sqs-chat-assembly-${var.slack_lambda_version}.jar"
}

variable "slack_hook_url" {
  type = "string"
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以定义 Lambda 函数资源了。这一次，我们将不会选择一个 S3 文物，而是直接上传我们组装的档案。如果文件已经改变，尽管文件名没有改变，指定`source_code_hash`也是有用的，以便触发更新。您可能会考虑在文件名中包含提交 SHA 和存储库是否干净，以增加透明度。

我们还将分配 1 GB 的 RAM，因为 AWS Lambda 会分配与内存相对应的 CPU，而 JVM 类加载会为初始请求占用大量 CPU，因此我们需要这种计算能力🔥。此时，我们还将 Slack URL 作为环境变量传递。

```
resource "aws_lambda_function" "slack" {
  function_name = "sns-sqs-upload-slack"
  filename = "${local.slack_lambda_artifact}"
  source_code_hash = "${base64sha256(file(local.slack_lambda_artifact))}"
  handler = "de.frosner.aws.slack.Handler"
  runtime = "java8"
  role = "${aws_iam_role.lambda_exec.arn}"
  memory_size = 1024
  timeout = 5

  environment {
    variables {
      hook_url = "${var.slack_hook_url}"
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们必须创建一个允许 SNS 消息触发 Lambda 函数的权限。

```
resource "aws_lambda_permission" "sns" {
  statement_id  = "AllowExecutionFromSNS"
  action        = "lambda:InvokeFunction"
  function_name = "${aws_lambda_function.slack.function_name}"
  principal     = "sns.amazonaws.com"
  source_arn = "${aws_sns_topic.upload.arn}"
} 
```

Enter fullscreen mode Exit fullscreen mode

## λ订阅

[![lambda sns subscription architecture](../Images/e3bf95703e76bf74479e73344ebe5181.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x-5vppEy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h4p2qrbppnn2232y10t5.png)

要完成我们的管道，唯一缺少的环节是订阅 SNS 主题的 Lambda 函数。这与 SQS 订阅基本相同，但这次使用了`lambda`协议。

```
resource "aws_sns_topic_subscription" "lambda" {
  topic_arn = "${aws_sns_topic.upload.arn}"
  protocol  = "lambda"
  endpoint  = "${aws_lambda_function.slack.arn}"
} 
```

Enter fullscreen mode Exit fullscreen mode

## 部署

现在我们可以运行`terraform apply`。确保您之前执行了`sbt assembly`，以便 Terraform 可以上传工件。

[![terraform deployment](../Images/7d843a4fd47053736d8e452dc8aa688e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BxnvvYbh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/liuf26qdnav048itbmzj.gif)

# 结论

终于，我们完成了！那是相当多的工作。我们可以通过直接向 Lambda 函数发送 S3 通知来简化架构。但是我想演示扇出模式，这也是为什么我们引入了目前没有使用的 SQS 队列。

我们已经看到了如何使用完全托管的构建块(如 SNS、SQS 和 Lambda)来实现一个可能有多个消费者和生产者的事件处理管道。SNS 提供发布/订阅功能来分离生产者和消费者，而 SQS 让我们能够异步处理事件。

你用过社交网站或 SQS 吗？你对亚马逊 MQ 或 Kinesis 有什么体验，在哪些情况下你认为 SQS 是合适的？请在评论中告诉我你的想法。