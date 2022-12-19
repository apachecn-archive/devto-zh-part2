# 使用 CloudWatch 监控 AWS Lambda 函数

> 原文：<https://dev.to/frosnerd/monitoring-aws-lambda-functions-with-cloudwatch-1nap>

这篇博文是我的 AWS 系列的一部分:

*   [基础设施作为使用 Terraform 的代码管理 AWS](https://dev.to/frosnerd/infrastructure-as-code---managing-aws-with-terraform-i9o)
*   [使用 Lambda 和 API 网关在 AWS 上部署 HTTP API](https://dev.to/frosnerd/deploying-an-http-api-on-aws-using-lambda-and-api-gateway-g61)
*   [使用 Elastic Beanstalk 在 AWS 上部署 HTTP API](https://dev.to/frosnerd/deploying-an-http-api-on-aws-using-elastic-beanstalk-5dh7)
*   [部署 AWS RDS MySQL 实例并进行基准测试](https://dev.to/frosnerd/deploying-and-benchmarking-an-aws-rds-mysql-instance-2faf)
*   [AWS 中使用社交网络、SQS 和 Lambda 的事件处理](https://dev.to/frosnerd/event-handling-in-aws-using-sns-sqs-and-lambda-2ng)
*   [使用 Terraform 和 Travis CI 在 AWS 上连续交付](https://dev.to/frosnerd/continuous-delivery-on-aws-with-terraform-and-travis-ci-3914)
*   [使用物联网核心、Kinesis 和 ElastiCache 在 AWS 上处理传感器数据](https://dev.to/frosnerd/sensor-data-processing-on-aws-using-iot-core-kinesis-and-elasticache-26j1)
*   [**用 CloudWatch** 监控 AWS Lambda 功能](#)

# 简介

与裸机、虚拟机以及容器化部署相比，像 AWS Lambda 这样的功能即服务产品提供了极大的便利。您只需管理您想要运行的实际代码，其余的由云提供商负责。但是它们操作起来也方便吗？

在这篇博文中，我们想看看如何使用 Amazon CloudWatch 通过监控和警报来帮助 Lambda 操作。我们将使用现有的指标，但也会创建一个自定义指标过滤器来解析 CloudWatch 日志中的内存消耗。

这些指标在 CloudWatch 仪表板中可视化，警报被配置为在阈值被突破的情况下向 AWS SNS 主题推送通知。像往常一样，一切都将与 HashiCorp Terraform 一起部署。下面你会看到我们将在文章结尾提供的结果面板的截图。

[![dashboard overview](../Images/6ebc200c4354f163081e07b35617493e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JmvMBtxG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gzreve3r515g5ogpgfsw.png)

[源代码](https://github.com/FRosner/aws-lambda-monitoring-alerting-example)可以在 GitHub 上获得。请注意，我们不会讨论[在单个存储库中管理多个 Lambda 函数](https://dev.to/frosnerd/yarnception-starting-yarn-within-yarn-through-gulp-and-when-it-is-useful-og3)的主题，也不会讨论如何在一个空闲通道中显示[警报通知，因为这已经在之前的博客文章中讨论过了。](https://dev.to/frosnerd/event-handling-in-aws-using-sns-sqs-and-lambda-2ng)

# 度量标准

在 CloudWatch 中，指标被组织在所谓的*名称空间*中。名称空间类似于度量的文件夹，可以用来将同一应用程序的度量分组在一起。一个*指标*是一组按时间排序的数据点，也称为时间序列。例如，EC2 实例的 CPU 使用率，或者向 API 发出的请求数量。

大多数 AWS 服务将预定义的指标发送到 CloudWatch，但也有可能发送自定义指标。截至目前，AWS Lambda 向 CloudWatch 公开了以下现成的指标:

*   **祈愿。**调用次数指标衡量一个函数被调用的次数。调用可以通过事件或调用 API 调用发生。它包括失败和成功的调用，但不包括失败的调用请求，例如，如果发生节流。
*   **错误。**此度量测量由于函数错误、函数超时、内存不足错误或权限错误而导致调用失败的次数。它不包括由于超出并发限制或内部服务错误而导致的故障。
*   **死信错误。**如果您配置了死信队列，AWS Lambda 会将失败调用的事件有效负载写入该队列。死信错误度量捕获死信的失败传递。
*   **持续时间。**持续时间测量从功能代码开始执行到停止执行所经过的挂钟时间。小心，因为时钟不是单调的，你可能会得到负值。
*   **节流。**每当调用尝试因超出并发限制而失败时，都会对节流调用进行计数。
*   **迭代器年龄(仅限流)。**迭代器年龄指标仅在 Lambda 函数被 AWS 流服务(如 Kinesis)调用时可用。它表示事件被写入流和被 Lambda 函数获取的时间之间的时间差。
*   **并发执行。**该指标是一个帐户范围的聚合指标，表示给定功能的并发执行总数。它适用于具有自定义并发限制的函数。
*   **无保留的并发执行。**与并发执行指标类似，未预留并发执行指标也是一个帐户范围的指标。它表示没有指定自定义并发限制的所有函数的并发总数。

每个指标最多可以分配十个维度。一个*维度*是一个描述一个指标的键值对，除了指标名称之外，还可以用来唯一地标识一个指标。AWS Lambda 发出的指标有以下维度:

*   **功能名称。**函数名可用于选择基于 Lambda 函数名的指标。
*   **资源。**资源维度有助于根据功能版本或别名进行过滤。
*   **执行版本。**使用别名调用时，可以使用执行版本维度根据函数版本进行过滤。

可以通过所谓的*统计数据*来聚集指标。CloudWatch 提供了以下统计数据:最小值、最大值、总和、平均值、计数和百分位数。统计是在指定的*周期*内计算的。据我所知，它使用一个滚动窗口来做到这一点，但我不完全确定。请参考我之前关于流分析中的[窗口函数的帖子，了解更多关于滚动窗口的信息。](https://dev.to/frosnerd/window-functions-in-stream-analytics-1m6c)

# 度量过滤器

如前所述，除了 AWS 服务提供的现成指标之外，还可以生成自定义指标。当查看 AWS Lambda 时，一个普遍感兴趣的指标是最大内存消耗。

自定义指标可以直接通过 CloudWatch API 编写，或者使用 AWS SDK。然而，Lambda 函数本身没有任何关于其内存消耗的信息。怎么才能解决这个问题？幸运的是公制过滤器是救星！每次函数执行后，AWS 都会在 CloudWatch 日志中写入一份报告，如下所示:

```
REPORT RequestId: f420d819-d07e-11e8-9ef2-4d8f649fd167  Duration: 158.15 ms Billed Duration: 200 ms Memory Size: 128 MB Max Memory Used: 38 MB 
```

Enter fullscreen mode Exit fullscreen mode

该报告包含我们正在寻找的信息:`Max Memory Used: 38 MB`。CloudWatch 提供了一个方便的功能，可以将日志转换成称为*指标过滤器*的指标。

一个过滤器由一个*模式*、一个*名称*、一个*名称空间*、一个*值*和一个可选的*默认值*组成。它将模式应用于每个日志行，如果匹配，则在给定名称空间中给定名称的度量内发出指定的值。如果没有日志事件发生，将发出默认值。

为了从日志行中提取最大内存，我们可以使用下面的模式并发出值`$max_memory_used_value`。有关模式语法的更多信息，请参考[官方文档](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/FilterAndPatternSyntax.html)。

```
[
  report_label=\"REPORT\",
  request_id_label=\"RequestId:\", request_id_value,
  duration_label=\"Duration:\", duration_value, duration_unit=\"ms\",
  billed_duration_label1=\"Billed\", bill_duration_label2=\"Duration:\", billed_duration_value, billed_duration_unit=\"ms\",
  memory_size_label1=\"Memory\", memory_size_label2=\"Size:\", memory_size_value, memory_size_unit=\"MB\",
  max_memory_used_label1=\"Max\", max_memory_used_label2=\"Memory\", max_memory_used_label3=\"Used:\", max_memory_used_value, max_memory_used_unit=\"MB\"
] 
```

Enter fullscreen mode Exit fullscreen mode

我注意到，写入日志的最大内存已经在之前的一些调用中进行了汇总。我怀疑它指的是在后台运行的一个实例的最大内存，每当函数再次启动时，例如在中断或重新部署后，它就会被重置。如果你有更多关于此事的信息，请留下评论！

# 报警

指标是支持您运营的重要组成部分。为了使它们真正有用，我们需要定义一个过程，包括基于度量值如何随时间变化的自动或手动操作。

CloudWatch 允许您定义*警报*，这些警报是触发*动作*的规则，这些动作基于一个指标在多个时间段内的阈值。每个警报都与一个或多个操作相关联。这可以是 EC2 操作、EC2 自动缩放操作或 SNS 通知。如果你发送一个 SNS 事件，你可以实现许多不同的消费者，比如 Lambda 函数发送一个 [Slack 消息](https://dev.to/frosnerd/event-handling-in-aws-using-sns-sqs-and-lambda-2ng)。

对于我们的 Lambda 函数，我们将实现以下三个基本警报:

*   **执行时间。**每个 Lambda 函数都有一个可配置的超时。如果代码运行时间超过指定的超时时间，调用将被中止。如果执行时间超过所配置超时的某个百分比，您可以创建一个警报。这样，如果您可能需要调整阈值或提高代码的性能，CloudWatch 会通知您。
*   **最大内存。**与执行时间类似，也有一个可配置的最大可用内存量。由于我们之前为最大已用内存定义的度量过滤器，如果超过某个阈值，我们可以触发警报。
*   **执行错误。**有时候代码会断掉。这可能是因为某些下游服务不可用、输入格式改变或者您的代码包含 bug。通过触发执行错误警报，您可以接收通知并采取相应的行动。但是请注意，这将包括所有错误，即使重试后调用成功。如果您只对重试后仍无法处理的事件感兴趣，则需要配置死信队列。

如果您使用 Terraform，您可以直接插入执行超时和最大内存，并为阈值选择一个百分比。下面的清单说明了如何使用 Terraform 创建一个警报资源，如果您的执行时间超过超时的 75%,就会触发该资源。

```
resource "aws_cloudwatch_metric_alarm" "calculator-time" {
  alarm_name          = "${local.project-name}-calculator-execution-time"
  comparison_operator = "GreaterThanOrEqualToThreshold"
  evaluation_periods  = "1"
  metric_name         = "Duration"
  namespace           = "AWS/Lambda"
  period              = "60"
  statistic           = "Maximum"
  threshold           = "${aws_lambda_function.calculator.timeout * 1000 * 0.75}"
  alarm_description   = "Calculator Execution Time"
  treat_missing_data  = "ignore"

  insufficient_data_actions = [
    "${aws_sns_topic.alarms.arn}",
  ]

  alarm_actions = [
    "${aws_sns_topic.alarms.arn}",
  ]

  ok_actions = [
    "${aws_sns_topic.alarms.arn}",
  ]

  dimensions {
    FunctionName = "${aws_lambda_function.calculator.function_name}"
    Resource     = "${aws_lambda_function.calculator.function_name}"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您登录到 AWS 控制台，CloudWatch 会显示您当前所有警报的概览。下表说明了我们的示例函数的三个警报。我生成了一些测试事件，其中一个事件在函数内部生成了一个错误，触发了相应的警报。

[![failing alarm table](../Images/ca70aad701381aa7d7b6ed2b9079c734.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aDsmKOIR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ubsrxru4ekgtf5l9tq72.png)

除了表格之外，每个警报还有一个非常简单的图形视图，它独立于 CloudWatch 仪表盘。下图描述了我们的警报的三个图表。

[![failing alarm chart](../Images/73e2d6caf286156504520c07c5efd70d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6e2Tq61C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9dnwrytamnh7h8cxsgwu.png)

# 仪表盘

我是自动化的忠实粉丝。我认为，没有人应该一天 24 小时都盯着仪表盘，试图找出错误。然而，仪表板对于快速了解系统非常有用。它们还允许人类发现新的模式，从而实现新类型的警报。

在 CloudWatch 中，一个*仪表盘*由多个*小部件*组成。小部件可以是指标的图形或 Markdown 语法中的文本。对于我们的示例函数，我们想要绘制四个指标:执行时间、使用的最大内存、执行错误和调用。

仪表板在内部存储为 JSON 对象，也可以由 Terraform 管理。仪表板对象由一组小部件对象组成。完整仪表板的源代码( [`cloudwatch_dashboard.tf`](https://github.com/FRosner/aws-lambda-monitoring-alerting-example/blob/master/terraform/cloudwatch_dashboard.tf) )太大了，无法在这里显示，所以我们只看两个小部件来说明这一点。下面的清单显示了调用求和小部件。

```
{  "type":  "metric",  "x":  12,  "y":  7,  "width":  12,  "height":  6,  "properties":  {  "metrics":  [  [  "AWS/Lambda",  "Invocations",  "FunctionName",  "${aws_lambda_function.calculator.function_name}",  "Resource",  "${aws_lambda_function.calculator.function_name}",  {  "color":  "${local.dashboard-calculator-invocation-color}",  "stat":  "Sum",  "period":  10  }  ]  ],  "view":  "timeSeries",  "stacked":  false,  "region":  "${data.aws_region.current.name}",  "title":  "Invocations"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这是它在浏览器中的样子:

[![invocations detail view](../Images/3da7705447d4e5981af0bb3a454204b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x7N1c3qq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2b8ar1al6bd352g3kt2y.png)

我们还可以添加水平注释来指示我们的警报阈值。此外，显示不同的统计数据也很有用。对于执行时间小部件，我们添加了一个水平注释以及两个统计数据:最大和平均执行时间。请找到下面的代码和结果截图。

```
{  "type":  "metric",  "x":  0,  "y":  1,  "width":  12,  "height":  6,  "properties":  {  "metrics":  [  [  "AWS/Lambda",  "Duration",  "FunctionName",  "${aws_lambda_function.calculator.function_name}",  "Resource",  "${aws_lambda_function.calculator.function_name}",  {  "stat":  "Maximum",  "yAxis":  "left",  "label":  "Maximum Execution Time",  "color":  "${local.dashboard-calculator-max-time-color}",  "period":  10  }  ],  [  "AWS/Lambda",  "Duration",  "FunctionName",  "${aws_lambda_function.calculator.function_name}",  "Resource",  "${aws_lambda_function.calculator.function_name}",  {  "stat":  "Average",  "yAxis":  "left",  "label":  "Average Execution Time",  "color":  "${local.dashboard-calculator-avg-time-color}",  "period":  10  }  ]  ],  "view":  "timeSeries",  "stacked":  false,  "region":  "${data.aws_region.current.name}",  "yAxis":  {  "left":  {  "min":  0,  "max":  ${aws_lambda_function.calculator.timeout}000,  "label":  "ms",  "showUnits":  false  }  },  "title":  "Execution Time",  "period":  300,  "annotations":  {  "horizontal":  [{  "color":  "${local.dashboard-calculator-max-time-color}",  "label":  "Alarm Threshold",  "value":  ${aws_cloudwatch_metric_alarm.calculator-time.threshold}  }  ]  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

[![execution time detail view](../Images/11573a37a9f8cf316c1f4631163349ae.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rRDWrTSB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/961wxj6tvnx1tk22nxpj.png)

# 结论

在这篇文章中，我们看到了如何创建 CloudWatch 指标过滤器、警报和仪表板。我们研究了为 Lambda 函数提供的现成的不同指标，以及如何使用指标过滤器从 CloudWatch 日志中解析最大内存消耗。

您可以添加自动警报，甚至是基于警报的预防措施，以便对系统中的危险情况做出反应。仪表板帮助人们不时地对正在发生的事情有所了解。

您是否使用过 CloudWatch 来管理指标和警报？我个人觉得在处理 Lambda 函数时，使用它比使用像 ElasticSearch 这样的外部解决方案要方便得多。你有什么看法？请在下面的评论中告诉我。

* * *

封面图片由[罗杰·舒尔茨](https://flic.kr/p/bEEdn3)