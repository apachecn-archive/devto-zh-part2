# 使用 Lambda 和 API 网关在 AWS 上部署 HTTP API

> 原文：<https://dev.to/frosnerd/deploying-an-http-api-on-aws-using-lambda-and-api-gateway-g61>

这篇博文是我的 AWS 系列的一部分:

*   [基础设施作为使用 Terraform 的代码管理 AWS](https://dev.to/frosnerd/infrastructure-as-code---managing-aws-with-terraform-i9o)
*   [**使用 Lambda 和 API 网关在 AWS 上部署 HTTP API**](#)
*   [使用 Elastic Beanstalk 在 AWS 上部署 HTTP API](https://dev.to/frosnerd/deploying-an-http-api-on-aws-using-elastic-beanstalk-5dh7)
*   [部署 AWS RDS MySQL 实例并进行基准测试](https://dev.to/frosnerd/deploying-and-benchmarking-an-aws-rds-mysql-instance-2faf)
*   [AWS 中使用社交网络、SQS 和 Lambda 的事件处理](https://dev.to/frosnerd/event-handling-in-aws-using-sns-sqs-and-lambda-2ng)
*   [使用 Terraform 和 Travis CI 在 AWS 上连续交付](https://dev.to/frosnerd/continuous-delivery-on-aws-with-terraform-and-travis-ci-3914)
*   [使用物联网核心、Kinesis 和 ElastiCache 在 AWS 上处理传感器数据](https://dev.to/frosnerd/sensor-data-processing-on-aws-using-iot-core-kinesis-and-elasticache-26j1)
*   [使用 CloudWatch 监控 AWS Lambda 功能](https://dev.to/frosnerd/monitoring-aws-lambda-functions-with-cloudwatch-1nap)

# 简介

随着云平台的兴起，如亚马逊网络服务(Amazon Web Services)和谷歌云平台(Google Cloud Platform)的兴起，使用托管基础设施和服务而不是托管自己的基础设施和服务变得越来越流行。在我的[上一篇博文](https://dev.to/frosnerd/infrastructure-as-code---managing-aws-with-terraform-i9o)中，我们讨论了*基础设施即服务* (IaaS)的概念。

虽然在您自己的数据中心管理虚拟机而不是裸机硬件已经提供了很多好处，但这仍然意味着巨大的维护开销。为了运行一个简单的 web 应用程序，你必须关心操作系统的更新、软件包的管理等等。

到目前为止，一种叫做*平台即服务* (PaaS)的更高抽象已经获得了发展势头，因为它允许您部署应用程序，而无需关心它在什么机器上运行。有多个服务通过提供托管应用容器来实现这一点，例如 [AWS ECS](https://eu-central-1.console.aws.amazon.com/ecs/home?region=eu-central-1#/getStarted) 、 [GCP Kubernetes 引擎](https://cloud.google.com/kubernetes-engine/)、[微软 Azure 应用服务](https://azure.microsoft.com/en-gb/services/app-service/)或 [Heroku](https://www.heroku.com/) 。

但是为什么就此打住呢？为什么您需要担心代码运行的环境？如果您可以简单地定义您的功能并将其部署到云中，会怎么样？这就是下一个流行词出现的地方:*无服务器*。

> 无服务器架构是包含第三方“后端即服务”(BaaS)服务的应用程序设计，和/或包含在“功能即服务”(FaaS)平台上的托管、短暂容器中运行的自定义代码。[1]

在 AWS 上工作时，无服务器通常被用作 AWS Lambda 的同义词。在这篇博文中，我们想看看如何使用 Lambda、API Gateway 和 S3 在 AWS 上部署一个简单的“无服务器”web 应用程序。我们将使用 Terraform 来管理我们的资源。

该职位的结构如下。首先，我们介绍目标体系结构，在概念层面上解释不同的组件。下一节讨论不同组件的实现以及它们是如何连接在一起的。我们通过总结和讨论主要发现来结束这篇文章。

# 建筑

## 概述

下图说明了目标体系结构。客户端向 API 网关发送一个 HTTP 请求。网关将丰富该请求并将其转发给 Lambda 函数。函数定义存储在 S3 上并动态加载。Lambda 函数的结果将由 API 网关处理，并向客户端返回相应的响应。

[![architecture](../Images/1d5397f79316be00f007cef69e89f7ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4h0Kd-Xz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0lexbp7wacyq34etri3h.png)

在我们的具体例子中，我们将使用 Scala 开发程序逻辑。汇编后的`jar`文件将被发布到 S3，并用于处理请求。我们现在将在概念层面上简要介绍各个组件。

## AWSλ

[AWS Lambda](https://aws.amazon.com/lambda) 是 AWS 的 FaaS 产品。它运行您预定义的功能来响应某些事件，并自动管理平台和资源。

Lambda 函数可用于处理来自 API 网关的请求，或对数据变化做出反应，例如 DynamoDB 中的更新、S3 桶中的修改或载入 Kinesis 流的数据。

目前支持以下[运行时](https://docs.aws.amazon.com/lambda/latest/dg/current-supported-versions.html):

*   节点. js
*   Java 语言(一种计算机语言，尤用于创建网站)
*   计算机编程语言
*   。网
*   去

在我们的例子中，我们将使用 Java 运行时来执行我们的 Scala 服务。

## AWS API 网关

[AWS API 网关](https://aws.amazon.com/api-gateway)是一个用于管理 API 的 AWS 服务。它可以作为应用程序的安全且可伸缩的入口点，将请求转发到适当的后端服务。API 网关还可以管理授权、访问控制、监控和 API 版本管理。

API 通过集成请求和集成响应与后端接口。它不充当简单的代理，而是需要来自集成后端的某些响应参数。

## AWS S3

[AWS S3](https://aws.amazon.com/s3/) 是 AWS 提供的对象存储。对象本身可以是任何东西，例如 HTML 文件、ZIP 文件或图片。

对象被组织在所谓的*桶*中，充当全局名称空间。在每个桶中，您的对象将由一个层次键寻址。URL `s3.eu-central-1.amazonaws.com/usa-trip/images/feelsbadman.jpg`将用于访问存储在`eu-central-1`区域中的`usa-trip`桶内的对象`/images/feelsbadman.jpg`。

架构够了，再来看实现。

# 实现

## 开发工具栈

为了开发该解决方案，我们使用了以下工具:

*   Terraform v0.11.7
*   SBT 1.0.4
*   Scala 2.12.6
*   IntelliJ + Scala 插件+ Terraform 插件

[源代码](https://github.com/FRosner/lambda-vs-beanstalk)可以在 GitHub 上获得。不过请注意，该项目包含两个模块，一个用于 AWS Lambda 部署，另一个是我正在试验的 [AWS 弹性豆茎](https://aws.amazon.com/elasticbeanstalk/)。

## 香草λ函数

Scala 中实现的普通 AWS Lambda 函数只是一个简单的类或对象方法。由于 AWS Lambda 只了解 Java 而不了解 Scala，我们必须坚持使用 Java 类型。下面是一个返回四个名字列表的函数:

```
package de.frosner.elbvsl.lambda

import scala.collection.JavaConverters._

object Main {
  def getCustomers: java.util.List[String] =
    List("Frank", "Lars", "Ross", "Paul").asJava
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们需要做的是打包该函数，并使其可用于 AWS Lambda。一种方便的方法是使用 [sbt-assembly](https://github.com/sbt/sbt-assembly) 插件构建一个 fat `jar`，并使用 [fm-sbt-s3-resolver](https://github.com/frugalmechanic/fm-sbt-s3-resolver) 将其上传到 S3。

为了让`sbt publish`任务完成我们想要的任务，我们将以下设置添加到我们的`build.sbt`文件中:

```
publishTo := Some("S3" at "s3://s3-eu-central-1.amazonaws.com/lambda-elb-test/lambda")
artifact in (Compile, assembly) := {
  val art = (artifact in (Compile, assembly)).value
  art.withClassifier(Some("assembly"))
}
addArtifact(artifact in (Compile, assembly), assembly) 
```

Enter fullscreen mode Exit fullscreen mode

别忘了提供[有效证件](https://github.com/frugalmechanic/fm-sbt-s3-resolver#s3-credentials)。这是我们运行`sbt publish` :
时得到的结果

```
...
[info] Packaging /Users/frosner/Documents/projects/lambda_vs_beanstalk/lambda/target/scala-2.12/elastic-beanstalk-vs-lambda-assembly-0.1-SNAPSHOT.jar ...
[info] Done packaging.
[info] S3URLHandler - Looking up AWS Credentials for bucket: lambda-elb-test ...
[info] S3URLHandler - Using AWS Access Key Id: <obfuscated> for bucket: lambda-elb-test
[info] S3URLHandler - Created S3 Client for bucket: lambda-elb-test and region: eu-central-1
...
[info]  published elastic-beanstalk-vs-lambda_2.12 to s3://s3-eu-central-1.amazonaws.com/lambda-elb-test/lambda/de/frosner/elastic-beanstalk-vs-lambda_2.12/0.1-SNAPSHOT/elastic-beanstalk-vs-lambda_2.12-0.1-SNAPSHOT-assembly.jar 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以使用 Terraform 创建 Lambda 函数。如果你不熟悉 Terraform 的工作方式，我推荐你看看我之前的博文:[基础设施作为代码——用 Terraform 管理 AWS](https://dev.to/frosnerd/infrastructure-as-code---managing-aws-with-terraform-i9o)。

为了创建一个新的 Lambda 函数，我们需要提供以下信息:

*   `jar`文件的位置，即我们的 S3 对象
*   执行函数的运行时间，即 Java 8
*   要启动的处理程序，即我们的`getCustomers`功能
*   用于执行的 IAM 角色

此外，我们还可以提供内存需求。我们的应用不应该需要超过默认的 128 MB。然而，AWS Lambda 分配的 CPU 资源与配置的内存成比例，对于 128 MB，Lambda 函数超时，因为在 JVM 启动期间加载类需要太多时间🤦。

我们使用的 IAM 角色没有附加任何策略，因为我们的功能不需要访问任何其他 AWS 服务。下面的清单显示了定义 Lambda 函数所需的 Terraform 文件。

```
variable "version" {
  type = "string"
  default = "0.1-SNAPSHOT"
}

resource "aws_lambda_function" "lambda-elb-test-lambda" {
  function_name = "lambda-elb-test"

  s3_bucket = "lambda-elb-test"
  s3_key    = "lambda/de/frosner/elastic-beanstalk-vs-lambda_2.12/${var.version}/elastic-beanstalk-vs-lambda_2.12-${var.version}-assembly.jar"

  handler = "de.frosner.elbvsl.lambda.Main::getCustomers"
  runtime = "java8"

  role = "${aws_iam_role.lambda_exec.arn}"

  memory_size = 1024
}

resource "aws_iam_role" "lambda_exec" {
  name = "lambda-elb-test_lambda"

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

我们现在已经定义了一个 AWS Lambda 函数，它可以被某些触发器调用。在我们的例子中，我们希望通过 API 网关来调用它。然而，为了做到这一点，我们需要修改 Lambda 函数，使其返回与 API 网关的[预期格式](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-create-api-as-simple-proxy-for-lambda.html#api-gateway-proxy-integration-create-lambda-backend)兼容的集成响应。

## API 网关λ函数

在普通的 Scala 函数中，我们的输入和输出是简单的 Java 对象。为了被 API 网关正确调用，我们的处理程序必须返回一个 JSON 字符串。这可能是它的样子:

```
{  "statusCode":  200,  "isBase64Encoded":  false,  "headers":  {  "my-header-key":  "my-header-value"  },  "body":  "my-response-body"  } 
```

Enter fullscreen mode Exit fullscreen mode

除此之外，API Gateway 还将原始请求包装在集成请求 JSON 对象中，用元数据丰富它。这个结构比响应 JSON 复杂得多。涵盖请求解析超出了这篇博文的范围，所以请参考 AWS 文档。

为了生成所需的 JSON 响应，我们需要修改我们的处理程序。此外，我们不再回复我们的客户，而是提供一个更通用的答案，也适用于许多其他问题:`42`。我们使用 [circe](https://github.com/circe/circe) 来生成 JSON 字符串，但是可以随意使用任何其他库。

```
import java.io.{InputStream, OutputStream, PrintStream}
import io.circe.syntax._
import io.circe.generic.auto._
import com.amazonaws.services.lambda.runtime.{Context, RequestStreamHandler}
import scala.io.Source

case class Response(statusCode: Int,
                    isBase64Encoded: Boolean,
                    headers: Map[String, String],
                    body: String)

class Handler extends RequestStreamHandler {
  override def handleRequest(input: InputStream,
                             output: OutputStream,
                             context: Context): Unit = {
    val logger = context.getLogger
    val inputJsonString = Source.fromInputStream(input).mkString
    logger.log(inputJsonString)
    val result = Response(
      statusCode = 200,
      isBase64Encoded = false,
      headers = Map.empty,
      body = "42"
    ).asJson
    val out = new PrintStream(output)
    out.print(result)
    out.close()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

新的处理程序现在将用`42` :
响应任何请求

```
{  "statusCode":  200,  "isBase64Encoded":  false,  "headers":  {},  "body":  "42"  } 
```

Enter fullscreen mode Exit fullscreen mode

我们的 Terraform 文件需要稍微修改，因为我们改变了处理程序的类:

```
resource "aws_lambda_function" "lambda-elb-test-lambda" {
  ...
  handler = "de.frosner.elbvsl.lambda.Handler"
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

下一小节将介绍如何设置 API 网关。

## API 网关配置

在 API 网关中，每个 API 都包括一组通过 HTTP 协议实现的资源和方法。这对应于表征状态转移(REST)的概念[2]。

在我们的例子中，我们只有一个名为`question`的资源，为了方便起见，我们将支持`ANY`方法。在一个设计良好的 REST API 中，应该正确定义发布问题的语义，例如，我们可以使用`POST`。

下面的 Terraform 文件定义了一个新的 API `lambda-elb-test-lambda`以及我们的`question`资源和方法。

```
resource "aws_api_gateway_rest_api" "lambda-elb-test-lambda" {
  name        = "lambda-elb-test"
  description = "Lambda vs Elastic Beanstalk Lambda Example"
}

resource "aws_api_gateway_resource" "question" {
  rest_api_id = "${aws_api_gateway_rest_api.lambda-elb-test-lambda.id}"
  parent_id   = "${aws_api_gateway_rest_api.lambda-elb-test-lambda.root_resource_id}"
  path_part   = "question"
}

resource "aws_api_gateway_method" "question" {
  rest_api_id   = "${aws_api_gateway_rest_api.lambda-elb-test-lambda.id}"
  resource_id   = "${aws_api_gateway_resource.question.id}"
  http_method   = "ANY"
  authorization = "NONE"
} 
```

Enter fullscreen mode Exit fullscreen mode

既然我们已经定义了 REST API，那么我们如何将它连接到我们的 Lambda 函数呢？让我们找出答案。

## 把一切都连接在一起

为了让 API 网关与我们的 Lambda 函数一起工作，我们必须创建一个*集成*。API Gateway 支持不同的[集成类型](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-api-integration-types.html)和集成 HTTP 方法。为了与 Lambda 集成，我们必须为 API 网关和 Lambda 之间的通信选择`AWS_PROXY`集成类型和`POST`方法。下面是地形资源定义:

```
resource "aws_api_gateway_integration" "lambda" {
  rest_api_id = "${aws_api_gateway_rest_api.lambda-elb-test-lambda.id}"
  resource_id = "${aws_api_gateway_method.question.resource_id}"
  http_method = "${aws_api_gateway_method.question.http_method}"

  integration_http_method = "POST"
  type                    = "AWS_PROXY"
  uri                     = "${aws_lambda_function.lambda-elb-test-lambda.invoke_arn}"
} 
```

Enter fullscreen mode Exit fullscreen mode

为了让客户端可以访问 API，我们必须部署它。一个*部署*必须与一个*阶段*相关联。阶段使我们能够释放金丝雀，但我们现在只打算坚持一个叫做`test`的阶段。我们将一个 terraform 依赖项添加到集成中，以确保在:
之前创建集成

```
resource "aws_api_gateway_deployment" "lambda" {
  depends_on = [
    "aws_api_gateway_integration.lambda"
  ]

  rest_api_id = "${aws_api_gateway_rest_api.lambda-elb-test-lambda.id}"
  stage_name  = "test"
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们必须为 API 网关部署创建一个权限来调用 Lambda 函数:

```
resource "aws_lambda_permission" "apigw" {
  statement_id  = "AllowAPIGatewayInvoke"
  action        = "lambda:InvokeFunction"
  function_name = "${aws_lambda_function.lambda-elb-test-lambda.arn}"
  principal     = "apigateway.amazonaws.com"

  source_arn = "${aws_api_gateway_deployment.lambda.execution_arn}/*/*"
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以执行`terraform apply`来启动所有组件。我们正在添加一个输出字段，它将为我们提供最终的 API URL: `${aws_api_gateway_deployment.lambda.invoke_url}/${aws_api_gateway_resource.question.path_part}`。请在下面查找执行结果。我省略了一些细节，使它更具可读性。

```
aws_iam_role.lambda_exec: Creating...
aws_api_gateway_rest_api.lambda-elb-test-lambda: Creating...
aws_api_gateway_rest_api.lambda-elb-test-lambda: Creation complete after 1s (ID: 27xbmjqf24)
aws_api_gateway_resource.question: Creating...
aws_iam_role.lambda_exec: Creation complete after 1s (ID: lambda-elb-test_lambda)
aws_api_gateway_account.lambda-elb-test: Creating...
aws_lambda_function.lambda-elb-test-lambda: Creating...
aws_api_gateway_resource.question: Creation complete after 0s (ID: tce971)
aws_api_gateway_method.question: Creating...
aws_api_gateway_method.question: Creation complete after 0s (ID: agm-27xbmjqf24-tce971-ANY)
aws_api_gateway_account.lambda-elb-test: Still creating... (10s elapsed)
aws_lambda_function.lambda-elb-test-lambda: Still creating... (10s elapsed)
aws_lambda_function.lambda-elb-test-lambda: Creation complete after 14s (ID: lambda-elb-test)
aws_api_gateway_integration.lambda: Creating...
aws_api_gateway_integration.lambda: Creation complete after 0s (ID: agi-27xbmjqf24-tce971-ANY)
aws_api_gateway_deployment.lambda: Creating...
aws_api_gateway_deployment.lambda: Creation complete after 0s (ID: qpzovb)
aws_lambda_permission.apigw: Creating...
aws_lambda_permission.apigw: Creation complete after 1s (ID: AllowAPIGatewayInvoke)

Apply complete!

Outputs:

url = https://27xbmjqf24.execute-api.eu-central-1.amazonaws.com/test/question 
```

Enter fullscreen mode Exit fullscreen mode

```
$ curl https://27xbmjqf24.execute-api.eu-central-1.amazonaws.com/test/question
42 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

我们已经看到，在 AWS 上开发 RESTful 服务而不必处理虚拟机、操作系统或容器是可能的。AWS API Gateway 和 AWS Lambda 是 AWS 上“无服务器”应用程序的两个重要组成部分。

这种架构的优势在于各个功能的分离，使得小型分布式团队能够使用不同的编程语言来开发他们的服务。大部分维护工作被卸载给 AWS，服务只通过定义良好的 API 进行通信。

缺点在于缺乏灵活性，并且有可能变得过于细粒度，从而导致混乱、复杂的设计。当您使用 AWS Lambda 时，您会受到它们所支持的运行时环境的束缚。这与 PaaS 方法不同，在 PaaS 方法中，您可以将您的运行时容器化，并且在可再现性方面有更高的信心。

你在生产中用过 AWS Lambda 吗？您是否遇到了问题，例如，关于可扩展性或可维护性的问题？您对其他云提供商的类似概念有何体验？请在评论中告诉我！

# 参考文献

*   [1] [无服务器架构](https://martinfowler.com/articles/serverless.html)作者[迈克·罗伯特](https://www.symphonia.io/bios/#mike-roberts)
*   [2]菲尔丁、罗伊·t 和理查德·泰勒。架构风格和基于网络的软件架构的设计。第 7 卷。博士论文:加州大学欧文分校，2000 年。
*   封面图片来自山姆·约翰斯顿使用 OmniGroup 的 OmniGraffle 和 Inkscape(包括马赛丽·斯蒂法诺维奇的 Computer.svg)创建的图片，CC BY-SA 3.0，[https://commons.wikimedia.org/w/index.php?curid=6080417](https://commons.wikimedia.org/w/index.php?curid=6080417)