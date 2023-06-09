# 如何在忘记之前更好地查看 AWS 成本

> 原文：<https://dev.to/kylegalbraith/how-to-better-watch-your-aws-costs-before-you-forget-566g>

[https://api.parler.io/ss/player?url=https://www.parler.io/audio/5020694/aa53daeccdac0999bf78cbc7cbd1868dcf1dfd61.c904eac5-9e84-457f-8c7d-2a8f91a0b4c4.mp3](https://api.parler.io/ss/player?url=https://www.parler.io/audio/5020694/aa53daeccdac0999bf78cbc7cbd1868dcf1dfd61.c904eac5-9e84-457f-8c7d-2a8f91a0b4c4.mp3)

跟踪 AWS 成本仍然是云世界中一个非常流行的话题。原因通常是人们在月底会对他们的账单感到惊讶。或者，他们可能认为一项服务的成本是 X，但到了月底，成本变成了 2 倍。

这不仅仅是我们这些 AWS 新手的问题。这是一个即使是最有经验的 AWS 开发者也会遇到的问题。

以我为例，今天早上我醒来时发现 AWS 账单比我通常每月支付的费用多了 20 美元。我为我的整个账户设置了一个[账单警报，但是总账单仍然在那个警报下，所以它没有被违反。](https://blog.kylegalbraith.com/2017/10/18/3-things-you-need-to-do-when-setting-up-your-first-aws-account/)

所以我四处挖掘，发现额外的花费是因为我让一个负载平衡器在我的帐户中运行，我用它来进行测试。

这种情况对人们来说很常见，因为人们很容易忘记自己创建的特定资源。有时这是因为我们在 AWS 控制台中手动创建了资源。或者我们通过代码创建了一些基础设施，但是当我们用完后却忘记拆除它。

对于我的一个产品，我的 RDS 和 EC2 支出的每月成本非常稳定且可预测。但是在这种情况下，因为我让一个负载平衡器运行，所以我的 EC2 成本比平时多了 20 美元。所以我着手解决不知道我离开负载平衡器运行的问题。

这篇文章的目的是展示我们如何利用 AWS 预算来创建每个服务级别的计费警报。我们将使用 [Terraform](https://www.terraform.io/) 来创建我们的预算，因此请确保您已经安装了该工具，并且位于您的路径中。

### 我们要跟踪什么

[终极幻想超级越野赛](https://www.ultimatefantasysupercross.com/)是我大约三年前推出的一个幻想赛车运动平台。它使用了几个 EC2 实例、一个 RDS 数据库和一个位于 S3 的静态网站。此时，我的基础架构的成本是非常可预测的。

| 服务 | 每月成本(美元) |
| --- | --- |
| 弹性计算云(EC2) | Nineteen point nine nine |
| 关系数据库服务 | Thirty-five point zero four |
| 简单存储服务(S3) | One point five |

如前所述，我已经配置了一个帐户级别的账单警报，当我的账单接近 60 美元时，它会通知我。但是我还想知道我的预测支出何时会超出我对 EC2 和 RDS 的每月预期。

为了实现这一目标，我们将创建两个 AWS 预算，一个用于 EC2，一个用于 RDS，当我对每项预算的预测支出超出我的预期时，我们会向我发送电子邮件。

### 创建预算

我们将在 Terraform 模板中创建三个资源。第一个将是一个帐户级别的计费警报，以防万一你还没有一个。然后，我们将为我们的 EC2 和 RDS 预测支出创建两个预算资源。

让我们从我们的帐户级别计费警报开始。

```
provider "aws" {
  region = "us-east-1"
}

resource "aws_cloudwatch_metric_alarm" "account-billing-alarm" {
  alarm_name          = "account-billing-alarm"
  comparison_operator = "GreaterThanOrEqualToThreshold"
  evaluation_periods  = "1"
  metric_name         = "EstimatedCharges"
  namespace           = "AWS/Billing"
  period              = "21600"
  statistic           = "Average"
  threshold           = "60"
  alarm_description   = "Billing alarm by account"
  alarm_actions       = ["<your-sns-topic-arn-for-notification>"]

  dimensions {
    Currency      = "USD"
    LinkedAccount = "<your-aws-account-id>"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里是我们的帐户级别计费警报。它正在跟踪个人账户的`EstimatedCharges`。当该帐户的预计费用达到 60 美元时，它将向我们在`alarm_actions`中指定的 SNS 主题发送警报通知。你应该用一个 SNS 话题的 ARN 来代替`alarm_actions`，这个话题有你的**邮件订阅**。还需要用自己的 AWS 账号 id 替换`LinkedAccount`。

我们可以通过首先初始化模板来创建我们的帐户级别警报。

```
$ terraform init
Initializing provider plugins...
- Checking for available provider plugins on https://releases.hashicorp.com...
- Downloading plugin for provider "aws" (1.39.0)... 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们的模板被初始化，我们就可以将我们的模板应用到我们的 AWS 帐户。

```
$ terraform apply
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + aws_cloudwatch_metric_alarm.account-billing-alarm
    ....
    ....

aws_cloudwatch_metric_alarm.account-billing-alarm: Creating...
  actions_enabled:                       "" => "true"
  alarm_actions.#:                       "" => "1"
  alarm_actions.321893454:               "" => "<your-sns-topic-arn-for-notification>"
  alarm_description:                     "" => "Billing alarm by account"
  alarm_name:                            "" => "account-billing-alarm"
  arn:                                   "" => "<computed>"
  comparison_operator:                   "" => "GreaterThanOrEqualToThreshold"
  dimensions.%:                          "" => "2"
  dimensions.Currency:                   "" => "USD"
  dimensions.LinkedAccount:              "" => "<your-aws-account-id>"
  evaluate_low_sample_count_percentiles: "" => "<computed>"
  evaluation_periods:                    "" => "1"
  metric_name:                           "" => "EstimatedCharges"
  namespace:                             "" => "AWS/Billing"
  period:                                "" => "21600"
  statistic:                             "" => "Average"
  threshold:                             "" => "60"
  treat_missing_data:                    "" => "missing"
aws_cloudwatch_metric_alarm.account-billing-alarm: Creation complete after 1s (ID: account-billing-alarm)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed. 
```

Enter fullscreen mode Exit fullscreen mode

太好了！现在，当我们的预计账单将超过 60 美元时，我们会收到账户级别的账单警报通知。让我们创建预算，以便在 EC2 或 RDS 的预测支出超出预期时发出警报。

继续将以下资源添加到您的 Terraform 模板中。

```
resource "aws_budgets_budget" "ec2-forecast-alarm" {
  name                  = "budget-ec2-monthly"
  budget_type           = "COST"
  limit_amount          = "20"
  limit_unit            = "USD"
  time_period_start     = "2018-01-01_00:00"
  time_unit             = "MONTHLY"

  cost_filters {
    service = "Amazon Elastic Compute Cloud - Compute"
  }
}

resource "aws_budgets_budget" "rds-forecast-alarm" {
  name                  = "budget-rds-monthly"
  budget_type           = "COST"
  limit_amount          = "36"
  limit_unit            = "USD"
  time_period_start     = "2018-01-01_00:00"
  time_unit             = "MONTHLY"

  cost_filters {
    service = "Amazon Relational Database Service"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是我们对 EC2 和 RDS 的预算。注意`cost_filters`为每个服务使用了完全限定的名称，这是 AWS 通过 Terraform 的预算所期望的。我们可以继续运行我们的`apply`命令来创建新的预算。

```
$ terraform apply
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + aws_budgets_budget.ec2-forecast-alarm
      id:                   <computed>
      account_id:           <computed>
      budget_type:          "COST"
      cost_filters.%:       "1"
      cost_filters.Service: "ec2"
      cost_types.#:         <computed>
      limit_amount:         "20"
      limit_unit:           "USD"
      name:                 "budget-ec2-monthly"
      name_prefix:          <computed>
      time_period_end:      "2087-06-15_00:00"
      time_period_start:    "2018-01-01_00:00"
      time_unit:            "MONTHLY"

  + aws_budgets_budget.rds-forecast-alarm
      id:                   <computed>
      account_id:           <computed>
      budget_type:          "COST"
      cost_filters.%:       "1"
      cost_filters.Service: "rds"
      cost_types.#:         <computed>
      limit_amount:         "36"
      limit_unit:           "USD"
      name:                 "budget-rds-monthly"
      name_prefix:          <computed>
      time_period_end:      "2087-06-15_00:00"
      time_period_start:    "2018-01-01_00:00"
      time_unit:            "MONTHLY"

Apply complete! Resources: 2 added, 0 changed, 0 destroyed. 
```

Enter fullscreen mode Exit fullscreen mode

就这样，我们现在在 AWS 帐户中配置了两个预算。前者记录我们在 EC2 上的花费，后者记录我们在 RDS 上的花费。**但是，我们还没有为这些设置通知。**这是一个[未解决的问题](https://github.com/terraform-providers/terraform-provider-aws/issues/4548)在 Terraform 中添加了对`aws_budgets`资源的支持，但是在这一点上，我们必须自己完成。

### 向我们的预算添加通知

由于目前 Terraform 的限制，我们需要将通知添加到模板之外的预算中。我们可以通过使用 [AWS CLI](https://aws.amazon.com/cli/) 快速向我们的每个预算添加通知。要开始，您需要再次使用您的 AWS 帐户 id。

明白了吗？很好，现在我们可以在命令行上运行以下命令了。

```
$ aws budgets create-notification --account-id <your-aws-account-id> --budget-name budget-rds-monthly --notification NotificationType=FORECASTED,ComparisonOperator=GREATER_THAN,Threshold=100,ThresholdType=PERCENTAGE --subscribers SubscriptionType=EMAIL,Address=<your-email-address>

$ aws budgets create-notification --account-id <your-aws-account-id> --budget-name budget-ec2-monthly --notification NotificationType=FORECASTED,ComparisonOperator=GREATER_THAN,Threshold=100,ThresholdType=PERCENTAGE --subscribers SubscriptionType=EMAIL,Address=<your-email-address> 
```

Enter fullscreen mode Exit fullscreen mode

瞧啊。现在，我们的帐户中配置了两个 AWS 预算，当我们在 EC2 或 RDS 上的支出超出我们的预期时，它会通知我们。

### 结论

对 AWS 资源的成本感到惊讶不是什么好感觉。当成本是不必要的，比如让负载均衡器运行，这种感觉就更不好了。因此，为自己安装护栏是一个好主意，即使你的 AWS 帐户只是供个人使用。

首先要做的是设置一个账户级别的账单警报，这样你就能知道你每月的账单何时会超出你的预期。如果您的工作需要，下一步是做我们在这里做的事情，并添加服务级别警报。

### 您是否渴望了解更多关于亚马逊网络服务的信息？

如果你想开始你的 AWS 之旅，但却不知道从哪里开始，可以考虑查看我的课程。我们专注于在 AWS 上托管、保护和部署静态网站。让我们在使用时能够了解超过 6 种不同的 AWS 服务。在你掌握了基础知识之后，我们可以进入**的两个额外章节**来讨论更高级的主题，比如基础设施代码和持续部署。