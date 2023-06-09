# 如何计算将 EC2 应用迁移到 Lambda 所节省的费用

> 原文：<https://dev.to/paulswail/how-to-calculate-the-billing-savings-of-moving-an-ec2-app-to-lambda-15c>

与运行在 EC2 + ELB 上的更传统架构的应用程序相比，迁移到无服务器架构的最大好处之一是每月 aws 账单中潜在的巨大(有时是数量级的)成本节约。

虽然这种*潜力*是真实的，但对你的应用来说可能不是这样。因此，你需要完成尽职调查，看看你的应用程序当前的使用水平如何适应新的定价模式，然后才能做出评估。

但是 Lambda 和 API Gateway 的定价模型与 EC2 的定价模型有很大的不同，弄清楚你需要寻找什么样的 CloudWatch 指标以及在哪里可以找到它们可能会非常混乱。

本文将逐步指导您如何从现有的 CloudWatch 存储库中提取正确的指标，并将它们插入一个公式中，以给出您可以自信地袖手旁观的估计值。

## 了解新的定价模式

在我们分析你的应用程序的现有使用水平之前，我们首先需要确定我们在寻找什么指标。

基于 EC2 的应用程序的主要收费单位是每个 EC2 实例和弹性负载平衡器的数量和大小，以及它们是否总是打开或根据需要加速/减速。在新的无服务器世界中，这些指标不再适用，取而代之的是与您的应用程序使用水平更紧密相关的指标。

先看[λ](https://aws.amazon.com/lambda/pricing/)，其电荷单位如下:

*   **每月请求(功能执行)次数**——每 1M 请求 0.20 美元，每月前 1M 免费。
*   **请求持续时间(秒)**
*   **请求内存分配(千兆字节)**

最后两个指标相乘得到一个计算数字，以每月 GB 秒为单位。截至发稿时，每月前 400，000 GB 秒是免费的，此后每使用一 GB 秒收费 0.00001667 美元。需要注意的重要一点是，为 Lambda 函数选择的内存分配也会影响 AWS 将分配给它的 CPU 和网络资源。

如果你的 Lambda 函数将在 web 上公开(作为前端或 API)，那么将需要 [API 网关](https://aws.amazon.com/api-gateway/pricing/)来触发函数调用。它的收费如下:

*   **每月请求数**——收到的每百万 API 调用 3.50 美元
*   **下载消耗的带宽(千兆字节)** —这是发送回客户端的数据大小。前 10TB 0.09 美元/GB

因此，根据上述两项服务的收费单位，我们需要从您的应用程序的当前使用情况中提取以下数据:

*   **每月请求数量**
*   **平均请求持续时间**
*   **最大请求内存分配**
*   **下载带宽**

## 评估 CloudWatch 中的当前使用水平

既然我们已经知道了*需要寻找什么样的*指标，我们将看看*如何使用 CloudWatch 为*得出估算值。
我们将使用 CloudWatch CLI 的 [`get-metric-statistics`](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/get-metric-statistics.html) 命令来获取相关的统计数据。您需要在您的机器上安装 [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/installing.html) 来运行下面的命令。

> #### 不想使用 CLI 还是更喜欢更直观的东西？
> 
> 前往[我的网站](https://winterwindsoftware.com/ec2-to-serverless-migration-calculator/#download-dashboard)通过**现成的 CloudWatch 仪表板**接收电子邮件，其中包含我们下面介绍的所有指标统计数据，只需点击几下就可以安装到你的 AWS 控制台中。你还会得到一个 **Excel 计算器电子表格**，你可以很容易地插入你的指标，以运行你的数字。

#### 请求数

在您的终端中运行以下命令，以获取在过去 30 天内所有 elb 处理的请求总数。
请注意，您需要将下面的`--start-time`和`--end-time`日期值更改为您希望评估的周期的开始和结束日期。

```
aws cloudwatch get-metric-statistics \
    --namespace AWS/ELB \
    --metric-name RequestCount --statistics "Sum" \
    --period 2592000 \
    --start-time 2018-06-01T00:00:00 \
    --end-time 2018-06-30T23:59:59 
```

Enter fullscreen mode Exit fullscreen mode

这将返回 JSON 数据，该数据应该返回一个数据点，看起来像这样:

```
{
    "Datapoints": [
        {
            "Timestamp": "2018-06-01T00:00:00Z",
            "Sum": 4213041.0,
            "Unit": "Count"
        }
    ],
    "Label": "RequestCount"
} 
```

Enter fullscreen mode Exit fullscreen mode

`Sum`字段的值(本例中为 4213041)是我们感兴趣的。

#### 请求持续时间

为了获得平均请求持续时间，我们将通过以下命令使用`Latency`指标(再次更新日期范围):

```
aws cloudwatch get-metric-statistics \
    --namespace AWS/ELB \
    --metric-name Latency --statistics "Average" \
    --period 2592000 \
    --start-time 2018-06-01T00:00:00 \
    --end-time 2018-06-30T23:59:59 
```

Enter fullscreen mode Exit fullscreen mode

响应应该如下所示:

```
{
    "Datapoints": [
        {
            "Timestamp": "2018-06-01T00:00:00Z",
            "Average": 0.10936070027143541,
            "Unit": "Seconds"
        }
    ],
    "Label": "Latency"
} 
```

Enter fullscreen mode Exit fullscreen mode

`Average`字段的值(0.109...在这个例子中)，是我们感兴趣的。

#### 内存分配

这个指标是最难衡量的，因为如前所述，内存不是唯一的考虑因素，CPU 也需要考虑在内。
来自[的拉姆达文件](https://docs.aws.amazon.com/lambda/latest/dg/resource-model.html):

> AWS Lambda 通过使用与通用 Amazon EC2 实例类型(如 M3 类型)相同的比率来分配与内存成比例的 CPU 功率。例如，如果您分配 256 MB 内存，您的 Lambda 函数将获得两倍于您仅分配 128 MB(最小值)的 CPU 份额。
> 您可以更新配置，并以 64 MB 为增量请求从 128MB 到 3008 MB 的额外内存。

不幸的是，没有内置的 CloudWatch 指标来帮助我们基于您现有的 EC2 应用程序，因此您需要检查您现有的 EC2 实例大小，并对分配多少做出最佳猜测。出于成本考虑，最好采用更大、更保守的估计值。分配额外的内存也有助于函数更快地完成，从而降低持续时间方面的成本。

为了便于计算，我们假设内存分配为 1024MB。

#### 下载带宽

该指标是您的应用程序在 1 个月内将发送给客户端的数据量。
虽然不能完全替代，`EstimatedProcessedBytes` ELB 指标是这方面的最佳指标，因为它测量负载均衡器处理的接收请求和发送响应中的数据总量。所以这个估计应该偏高。

要从 Cloudwatch 获取这个指标，在您的终端中运行以下命令，将`YOUR_ELB_NAME`替换为您的负载平衡器的名称:

```
aws cloudwatch get-metric-statistics \
    --namespace AWS/ELB \
    --metric-name EstimatedProcessedBytes --statistics "Sum" \
    --dimensions Name=LoadBalancerName,Value=YOUR_ELB_NAME \
    --period 2592000 \
    --start-time 2018-06-01T00:00:00 \
    --end-time 2018-06-30T23:59:59 
```

Enter fullscreen mode Exit fullscreen mode

响应应该是这样的:

```
{
    "Datapoints": [
        {
            "Timestamp": "2018-06-01T00:00:00Z",
            "Sum": 104507690620.0,
            "Unit": "Bytes"
        }
    ],
    "Label": "EstimatedProcessedBytes"
} 
```

Enter fullscreen mode Exit fullscreen mode

`Sum`字段的值(本例中为 104507690620)是我们感兴趣的。

*如果你有多个 elb，你需要为每个 elb 运行这个命令(改变`LoadBalancerName`值)并把`Sum`值加起来得到一个总数。*

## 得出最后的数字

让我们来看看我们获得的所有指标值:

| 公制的 | 价值 |
| --- | --- |
| 请求数量 | Four million two hundred and thirteen thousand and forty-one |
| 平均请求持续时间(秒) | Zero point two |
| 内存分配(GB) | One point zero two four |
| 下载带宽(GB) | One hundred and four point five two |

我已经将数据值转换为千兆字节单位，并将平均请求持续时间从 0.11 四舍五入为 0.2，因为它以 100 毫秒为间隔充电。
我们现在可以将这些值代入下面的公式，得出每项服务的估计总数:

#### λ估计成本

```
lambda_request_cost = (number_of_requests - free_requests) / 1000000 * 0.20
                    = (4213041 - 1000000) / 1000000 * 0.20
                    = $0.64

lambda_compute_cost = MAX(((number_of_requests * memory_allocation
                * average_request_duration) - free_compute, 0) * 0.00001667
                    = MAX(((4213041 * 1.024 * 0.2) - 400000, 0) * 0.00001667
                    = $7.72

lambda_total_cost   = lambda_request_cost + lambda_compute_cost
                    = $8.36 
```

Enter fullscreen mode Exit fullscreen mode

#### API 网关预计成本

```
api_request_cost    = number_of_requests / 1000000 * 3.50
                    = 4213041 / 1000000 * 3.50
                    = $14.75

api_bandwith_cost   = download_bandwidth * 0.09
                    = 104.52 * 0.09
                    = $9.41

api_total_cost      = api_request_cost + api_bandwith_cost
                    = $24.15 
```

Enter fullscreen mode Exit fullscreen mode

这给出了估计的每月总费用为 **$32.51** 。

## 接下来是什么？

现在你有了一个在 Lambda 和 API Gateway 中运行你的应用时的估计月账单的数字。不幸的是，AWS 账单并不是将应用迁移到无服务器架构的总拥有成本的唯一因素。

下一步是分析你的代码库，看看在 Lambda 函数中重新构建代码库需要做些什么。您还需要考虑开发人员和运营工程师在这个新的无服务器世界中工作所需的升级成本。

这两项任务都需要大量的时间来研究。但是现在你有了账单上节省的具体数字，你可以把它带给你的老板，以证明要求更深入调查的时间是合理的。

💌 ***如果你喜欢这篇文章，你可以注册[到我关于在 AWS](https://winterwindsoftware.com/newsletter/) 中构建无服务器应用的每周时事通讯。***