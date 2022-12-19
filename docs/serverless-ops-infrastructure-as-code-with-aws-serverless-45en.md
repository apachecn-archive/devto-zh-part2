# 无服务器运营:AWS 无服务器基础架构代码

> 原文：<https://dev.to/tmclaughbos/serverless-ops-infrastructure-as-code-with-aws-serverless-45en>

*这是我们的[“无服务器运营:当服务器消失时我们该怎么办？”](https://dev.to/tmclaughbos/serverless-ops-what-do-we-do-when-the-server-goes-away-1mln)关于定义运营和开发工程师在使用无服务器基础架构时的角色的系列。*

如果你喜欢你所读到的，在[无服务器运营博客](https://www.serverlessops.io/blog)上还有更多。

*[![infra-as-code](../Images/aba08ce0eb3a7734f8b3a7405b12d542.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YnXTvsia--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.serverlessops.io/hs-fs/hubfs/blog/infra-as-code.png%3Ft%3D1525144819454%26width%3D600%26name%3Dinfra-as-code.png)T4】*

多年来，作为 DevOps 的一部分，我们一直将基础设施视为代码。作为运营，我们从手工构建系统转向用代码自动完成工作。在某些情况下，我们甚至允许软件开发者构建他们自己的系统。它已经成为 DevOps 不可或缺的一部分，但是您是否想过作为代码的基础设施意味着什么，它可能如何改变，以及在无服务器环境中实现意味着什么？

## 什么是基础设施即代码？

在最高和最通用的级别上，代码形式的基础结构可以定义如下:

> 通常由 IT 运营团队提供计算机和相关资源，作为代码，而不是由 IT 运营团队进行人工提供。

它是让我们设置服务器的代码。它是让我们定义和提供自动扩展组的代码。这是部署 Kubernetes 集群或保险库的代码。

这种定义当然是正确的，但它导致运营工程师对自己的价值得出错误的结论；它提倡我们最大的价值是执行工程工作。它提倡这样一种观点，即运营的价值主要在于执行工作。

我不喜欢这种运营贬值。

太多的运营工程师陷入了这个陷阱，认为他们的价值是建立基础设施并保持其运行。当我们看到更多的基础设施工作由公共云提供商承担时，这就是一个问题。当 AWS 能够提供一个容器管理平台或机密管理，并且比您做得更好、更快时，您会怎么做？

相反，下面是我想要使用的最新定义:

> 将运营知识和专业技能提炼到代码中，允许某人(可能不是代码作者)安全地供应和管理基础设施。

我认为这个定义有两个方面与之前的有所不同。

一、*运营知识和专长的提炼*。它不是部署或设置某些东西的能力。这不是自动部署 Kubernetes 或 Vault 的能力(不是反对 Kubernetes 或 Vault，这些只是我知道人们喜欢设置的东西)。如果愿意，一般的工程师可能在一个周末就能完成。操作工程师的价值在于能够以可管理、可靠和可维护的方式建立服务。这是我们通过经验、实践和不断学习获得的专业知识。这是我们和那些在我们领域没有经验的人的区别。

这个定义的第二个重要部分是“*某人(可能不是你)”*。设置或配置某物的能力是无差别劳动。设置计算机系统的能力没什么特别的。这项工作大多数工程师可以在一个周末完成。当我第一次开始将基础设施作为代码和基础设施自动化来学习时，对我来说最令人兴奋的前景是能够让别人做这种无差别的工作。如果您已经实现了基础设施的自动化，但是当其他人请求资源时，您仍然需要点击一个按钮，那么您仍然依赖这种无差别的工作作为您的价值。您刚刚从服务交付中的手动瓶颈变成了自动化瓶颈。

任何人只要有足够的时间和精力，都可以阅读文档并找出如何自动部署某些东西。作为运营工程师，我们需要关注文档中没有提到的东西以及我们从经验中学到的东西。

这就是我们需要转化为代码的东西。

## 基础设施作为今天的代号

当今天讨论作为代码的基础设施时，我们经常会想到木偶、厨师等。管理我们的主机基础设施。在我管理的 Puppet 基础设施中，我们使用了 [Puppet roles 和 profiles](https://www.craigdunn.org/2012/05/239/) 模式。(如果你是一个厨师用户，模式是[厨师包装食谱](https://blog.chef.io/2017/02/14/writing-wrapper-cookbooks/)。)我们将配置管理分为三层:

1.  角色类别
2.  配置文件类别
3.  服务级别

处于最底层的是服务阶层，他们认为劳动是无差别的。几乎所有我们需要的东西，在[木偶锻造](https://forge.puppet.com/)上都有一个木偶模块。这段代码对我们的组织来说并不特殊或独特，我们认为，当一个合适的模块已经存在时，用这个 Nginx 类重新创建下面的模式不值得我们浪费时间。

```
class nginx {
  package { 'nginx':
    ensure => present
  }

  file { '/etc/nginx/nginx.conf':
    ensure => present,
    owner => 'root',
    Group => 'root',
    Mode => '0644',
    source => 'puppet:///modules/nginx/nginx.conf',
    require => Package['nginx'],
    notify => Service['nginx']
  }

  service { 'nginx':
    ensure => running,
    enabled => true,
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

更不用说，通常编写 Puppet Forge 模块的人对服务的理解水平超过了我们所拥有的。当然，有时，如果我们对模块的工作方式有异议或者发现了错误，我们会分叉这个模块。但这最终是我们不想编写或拥有的代码。

在这些类中间的是 profile 类；这是我们操作课上最重要的一课。profile 类是我们将操作知识转化为基础设施代码的地方。这要求我理解 Nginx 和它在我的组织中的上下文。编写下面的 *my::nginx* 类是对我们时间的更好利用。

```
class my::nginx {

  # From Puppet Forge module
  class { '::nginx': }

  # Status endpoint for Sensu Check
  nginx::resource::server { "${name}.${::domain} ${name}":
    ensure => present,
    listen_port => 443,
    www_root => '/var/www/',
    ssl => true,
    ssl_cert => '/path/to/wildcard_mydomain.crt',
    ssl_key => '/path/to/wildcard_mydomain.key',
  }

  nginx::resource::location { "${name}_status":
    ensure => present,
    ssl => true,
    ssl_only => true,
    server => "${name}.${::domain} ${name}",
    status_stub => true
  }

  # Sensu checks
  package { 'sensu-plugins-nginx':
    provider => sensu_gem
  }

  sensu::check { 'nginx-status':
    handlers => 'default',
    command => 'check-nginx-status.rb -p 443',
    custom => {
      refresh => 60,
      occurrences => 2,
    },
  }

  # Log rotation
  logrotate::rule { 'nginx_access':
    path => '/var/log/nginx/access.log',
    rotate => 5,
    rotate_every => 'day',
    postrotate => 'service nginx reload',
  }

  logrotate::rule { 'nginx_error':
    path => '/var/log/nginx/error.log',
    rotate => 5,
    rotate_every => 'day',
    postrotate => 'service nginx reload',
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的 Puppet 类将包、配置和服务模式工作留给了 Puppet Forge 的一个模块。相反，我花时间构建了一个 Puppet 类，它使用了从 Puppet Forge 获得的内容，并添加了我运行可靠服务和 Nginx 的知识。

Nginx 服务器强制使用 SSL，并指向我们的 SSL 证书在主机上的位置，因为我们不允许基础设施中存在未加密的 web 流量。my::nginx 类使用本地 nginx 功能添加了一个状态检查端点，并安装了一个 Sensu 插件来检查端点，因为我们使用 Sensu 进行监控。最后，它配置日志循环，以帮助防止主机磁盘变满。作为标准操作系统设置的一部分，所有主机上都已经配置了磁盘空间检查。

最后，在顶部的是角色类。这是一个配置文件类的集合。一个角色类代表一个业务功能。我们没有部署 nginx，我们部署了一个由 Nginx 提供服务的 web 应用程序。我们使用角色类的目标是提供快速组合由概要文件类组成的角色并部署新服务的能力。与此同时，我们可以对成功保持高度的信心，因为我们在概要文件层投入了大量的工作来创建一个可管理的、可靠的和可维护的服务。与其他组织不同，我们不希望运营成为彻底的把关者。

作为一名操作工程师，您的工作是将您的专业知识提炼到概要文件(木偶)、包装器(主厨)级别的代码中，或者工具中存在的任何中间抽象层中。

[![infra-as-code-you-are-here](../Images/d801d7e39b5e15d069f5100a0f80e165.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KHUMVdyT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.serverlessops.io/hs-fs/hubfs/blog/infra-as-code-you-are-here.png%3Ft%3D1525144819454%26width%3D600%26height%3D291%26name%3Dinfra-as-code-you-are-here.png)

这就是我们在剖面层所做的。我们理解围绕 Nginx 和 web 服务器的操作问题，并用 Puppet 代码处理它们。我们通过设置合理的默认值、确保适当的监控以及添加配置来防止可预见的问题，从而做到了这一点。这是有价值的工作。这是一个工作的组合，由于您的独特要求，在互联网上很难找到这种工作，并且这种工作结合了您作为运营工程师的特殊宝贵技能。

## 基础设施作为 AWS 中的代码

现在，让我们看看 AWS 管理领域中作为代码的基础设施。让我们先确定两件事:

首先，AWS 提供了一个名为 CloudFormation 的配置管理系统，它提供了配置任何 AWS 资源的能力。你不需要做任何工作就可以让别人有能力创建一个有云结构的 S3 桶。这大致相当于 AWS 拥有服务级别的工作。

第二，由于基础设施定义与应用程序代码一起存在，开发人员不需要您来创建对他们的应用程序的更改。这大致相当于现在开发者完全拥有了角色层。

当你进入 AWS 时，基础设施作为代码的许多无差别的工作甚至不作为你执行的选项而存在，你作为看门人的能力变得非常有限。我们需要定义云形成时剖面层的工作情况。当管理 AWS 资源和走向无服务器化时，将我们的运营专业知识转化为代码意味着什么？

我们可以从基本的 SQS 资源开始。

```
Resources:
  MyQueue:
    Type: "AWS::SQS::Queue" 
```

Enter fullscreen mode Exit fullscreen mode

这是调配 SQS 队列所需的最少量的云。现在，让我们开始添加我们的操作知识来创建可管理、可靠和可维护的东西。如果你熟悉云的形成，你可能会在前面的例子中看到一些问题。我们之后会拿到的。

### 合理配置

我们将从提供 SQS 队列的合理配置开始。几乎在每个组织中，都存在关于如何实现服务的规则。您有理由在组织中跨队列启用服务器端加密吗？如果您这样做了，您可能不应该让每个开发人员都记住启用它。设置带有服务器端加密的 SQS 队列的云结构应该大致如下所示:

```
Mappings:
  KmsMasterKeyId:
    us-east-1:
      Value: "arn:aws:kms:us-east-1:123456789012:alias/aws/sqs"
    us-east-2:
      Value: "arn:aws:kms:us-east-2:123456789012:alias/aws/sqs"

Resources:
  MyQueue:
    Type: "AWS::SQS::Queue"
    Properties:
      KmsMasterKeyId:
        Fn::FindInMap:
          - KmsMasterKeyId,
          - Ref: "AWS::Region"
          - "Value" 
```

Enter fullscreen mode Exit fullscreen mode

设置 KMS 键值现在是通过基于创建 SQS 队列的区域的查找来自动处理的。软件工程师和我都不需要担心包含这个配置。

现在移动到队列上的 *MessageRetentionPeriod* 设置；默认值是四天。想一想。这种暂停对你真的有用吗？您是否应该更快地失败并将消息放入死信队列，以便尝试更快地恢复？一刀切的价值观有可能吗？让我们添加一个具有合理默认值的可配置参数。

```
Parameters:
  MessageRetentionPeriod:
    Type: Number
    Description: "SQS message retention period"
    General: 1800

Mappings:
  KmsMasterKeyId:
    us-east-1:
      Value: "arn:aws:kms:us-east-1:123456789012:alias/aws/sqs"
    us-east-2:
      Value: "arn:aws:kms:us-east-2:123456789012:alias/aws/sqs"

Resources:
  MyQueue:
    Type: "AWS::SQS::Queue"
    Properties:
      KmsMasterKeyId:
        Fn::FindInMap:
          - KmsMasterKeyId,
          - Ref: "AWS::Region"
          - "Value"
      MessageRetentionPeriod:
        Ref: MessageRetentionPeriod 
```

Enter fullscreen mode Exit fullscreen mode

我们添加了默认为 30 分钟的 *MessageRetentionPeriod* 参数。但是，如果系统需要更长的处理时间或更快的故障，对于离群系统，该值仍然是可配置的。

我们已经在我们的组织内提供了我们认为合理的 SQS 队列配置。我们已经以自动设置这些值的方式做到了这一点，因此我们在创建新队列时不需要考虑这一点。

### 可靠性

我们现在可以继续确保 SQS 队列的可靠性。我们不负责运营 SQS；我们负责确保消息通过 SQS 队列在我们的应用程序中自由和正常地流动。我们将检查队列中产生的消息和队列中使用的消息。

```
Parameters:
  MessageRetentionPeriod:
    Type: Number
    Description: "SQS message retention period"
    Default: 60
  AlarmTopic:
    Type: String
    Description: "CloudWatch Alarms tpic name"
    Default: "CloudWatchAlarmsTopic"
  QueueLowMessagesReceivedThreshold:
    Type: String
    Description: "Low message alarm threshold"
  QueueDepthAlarmThreshold:
    Type: String
    Description: "SQS queue depth alarm value"

Mappings:
  KmsMasterKeyId:
    us-east-1:
      Value: "arn:aws:kms:us-east-1:123456789012:alias/aws/sqs"
    us-east-2:
      Value: "arn:aws:kms:us-east-2:123456789012:alias/aws/sqs"

Resources:
  MyQueue:
    Type: "AWS::SQS::Queue"
    Properties:
      KmsMasterKeyId:
        Fn::FindInMap:
          - KmsMasterKeyId,
          - Ref: "AWS::Region"
          - "Value"
      MessageRetentionPeriod:
        Ref: MessageRetentionPeriod

  QueueLowMessagesReceived:
    Type: "AWS::CloudWatch::Alarm"
    Properties:
      AlarmDescription: "Alarm on messages received lower than normal."
      Namespace: "AWS/SQS"
      MetricName: "NumberOfMessagesSent"
      Dimensions:
        - Name: "QueueName"
          Value:
            Fn::GetAtt:
              - "MyQueue"
              - "QueueName"
      Statistic: "Sum"
      Period: "300" # This is as granular as we get from AWS.
      EvaluationPeriods: "3"
      Threshold:
        Ref: "QueueLowMessagesReceivedThreshold"
      ComparisonOperator: "LessThanThreshold"
      AlarmActions:
        - Ref: "AlarmTopic"

  QueueDepth:
    Type: "AWS::CloudWatch::Alarm"
    Properties:
      AlarmDescription: "Alarm on high queue depth."
      Namespace: "AWS/SQS"
      MetricName: "ApproximateNumberOfMessagesVisible"
      Dimensions:
        - Name: "QueueName"
          Value:
            Fn::GetAtt:
              - "MyQueue"
              - "QueueName"
      Statistic: "Sum"
      Period: "300" # This is as granular as we get from AWS.
      EvaluationPeriods: "1"
      Threshold:
        Ref: "QueueDepthAlarmThreshold"
      ComparisonOperator: "GreaterThanThreshold"
      AlarmActions:
        - Ref: "AlarmTopic" 
```

Enter fullscreen mode Exit fullscreen mode

添加了两个 CloudWatch 警报以及一些参数。CloudWatch 警报 *QueueLowMessagesReceived* 检查以确保消息被生成到队列中。新的*QueueLowMessagesReceivedThreshold*参数没有默认值，它要求您花一些时间考虑系统的流量模式。

如果队列中的近似消息数量超过给定阈值，则 *QueueDepth* CloudWatch 警报将发出警报，表明消费者无法跟上消息的速度。此外，*queuedepthallarmthreshold*参数没有默认值，这是为了迫使您考虑预期的消息速率。

两个警报还使用一个参数值将警报状态更改传递给 SNS 主题。该 SNS 主题会路由到您的监控和警报系统，让您知道该 SQS 队列何时出现问题。作为一名运营人员，您应该拥有您环境中的警报管道。

我们现在不再仅仅部署一个 SQS 队列，我们正在部署一个队列以及 CloudWatch 资源，以便帮助我们维护这个队列的可靠性。这就像在前面的 Puppet 示例中为 Nginx 服务添加 Sensu 检查一样。最重要的是，这是在应用我们的运营专业知识，了解这项服务在我们的应用中可能会失败。

### 系统模式

最后，提供资源模式并解释何时使用该模式是操作工作的一部分。当你只有一把锤子时，一切都成了钉子。防止设计模式的误用是我们应该注意的事情。

你什么时候使用 SQS 队列？SNS 呢？两个一起？我们甚至还没讲到动作。有时，要使用的准确的 AWS 资源模式没有经过适当的考虑。工程师通常会重复使用他们上次所做的事情，因为那是他们所知道的。

现在，这方面的选择有点有限，但是我们将很快讨论无服务器工具生态系统中即将出现的东西，以帮助我们提供一个模式库来帮助工程师做出正确的选择。

## AWS 工具的状态

我上面展示的可能看起来很棒，但它不是今天就可以立即使用的。记住:运营应该将他们的知识转化为可供他人使用的代码。您可以使用 CloudFormation 嵌套堆栈来实现这一点，但是您必须构建自己的 CloudFormation 片段注册表和发现机制。如果你熟悉云的形成，你可能已经注意到这个问题。与前面的 Puppet 示例不同，没有现成的方法为 AWS 服务提供作为代码概要层的基础设施。让我们谈谈今天我们能做什么和将要发生什么。

### 使用无服务器，您今天能做什么

如果你正在寻找今天要做的工作，那么看看[无服务器框架](https://www.serverless.com)。无服务器框架的工具提供了充分利用您的操作知识的能力。无服务器框架是基于 CloudFormation 之上的，所以语法没有太大的不同，并且它添加了几个使其更容易使用的功能。

从[无服务器框架的插件](https://github.com/serverless/plugins)功能开始。这个功能本身就是我喜欢这个管理无服务器应用程序的工具的主要原因之一。如果你的用例足够常见，那么很有可能一个插件已经存在了。如果一个插件不存在，那就学习插件 API，写点 JavaScript 让它存在。

事实上， *QueueDepth* 的示例 CloudWatch alarm 受到了我经常使用的[无服务器-sqs-alarms-plugin](https://github.com/sbstjn/serverless-sqs-alarms-plugin) 插件的启发。这个插件也可以被扩展来处理 *QueueLowMessagesReceived* 警报，在某个时候我应该提交一个 PR 来扩展它。

这种方法的缺点是需要有人记得添加插件并进行配置。您仍然需要执行代码审查，以确保警报到位。然而，该插件确实大大减少了需要编写的配置量。只需对比以下 CloudFormation 和 Serverless 框架片段。

云的形成:

```
QueueDepth:
    Type: "AWS::CloudWatch::Alarm"
    Properties:
      AlarmDescription: "Alarm on high queue depth."
      Namespace: "AWS/SQS"
      MetricName: "ApproximateNumberOfMessagesVisible"
      Dimensions:
        - Name: "QueueName"
          Value:
            Fn::GetAtt:
              - "MyQueue"
              - "QueueName"
      Statistic: "Sum"
      Period: "300" # This is as granular as we get from AWS.
      EvaluationPeriods: "1"
      Threshold:
        Ref: "QueueDepthAlarmThreshold"
      ComparisonOperator: "GreaterThanThreshold"
      AlarmActions:
        - Ref: "AlarmTopic" 
```

Enter fullscreen mode Exit fullscreen mode

无服务器框架:

```
custom:
  sqs-alarms:
    - queue: MyQueue
      topic: AlarmTopic
      thresholds:
        - 100 
```

Enter fullscreen mode Exit fullscreen mode

无服务器框架模板是一个我认为没有得到足够重视的特性。我厌倦了以我创建服务的方式初始化一个新的 AWS Lambda Python 3 项目，我创建了自己的模板项目。现在我可以用下面的代码初始化一个新项目:

```
serverless create -u https://github.com/ServerlessOpsIO/sls-aws-python-36 -p |PATH| -n |NAME| 
```

Enter fullscreen mode Exit fullscreen mode

我的 [AWS Lambda Python 3 模板](https://github.com/ServerlessOpsIO/sls-aws-python-36/)处理标准设置，如日志记录、项目阶段和 AWS 配置文件处理，基于我如何分离阶段和帐户，一个处理 Python 依赖关系的插件，以及所需的一系列标准依赖关系。最终，我计划添加一个单元、集成和负载测试框架配置。所有这些的目的是提供快速建立一个具有已经建立的最佳实践的项目的能力。我想把重点放在应用程序上，而不是所有这些样板设置。

此外，我也有一个用于创建 AWS S3 托管网站的[模板。我创建了这个高阶应用程序模式，因为我发现它足够有用，而且我可能希望重复使用。](https://github.com/ServerlessOpsIO/sls-aws-s3-website)

### 明天你能做什么

我对我们明天能够做的事情非常感兴趣。现在，有 [AWS 无服务器应用程序库](https://aws.amazon.com/serverless/serverlessrepo/) (SAR)和[无服务器框架的组件](https://github.com/serverless/components)。这两个都很棒，几乎提供了我正在寻找的东西。

我之前谈过 [AWS 无服务器应用程序库和纳米服务](https://dev.to/tmclaughbos/using-nanoservices-to-build-serverless-applications-17ci)。现在，大多数人都在发布小型的完全可用的应用程序，但是我们中的一些人已经走在前面，开始看看我们能创造什么。我们已经创建了纳米服务，比如我们的[服务来解析 AWS 成本和使用报告](https://serverlessrepo.aws.amazon.com/applications/arn:aws:serverlessrepo:us-east-1:641494176294:applications~ApplicationCostMonitoring)，或者 AWS 的服务来[从 Twitter 流](https://serverlessrepo.aws.amazon.com/applications/arn:aws:serverlessrepo:us-east-1:077246666028:applications~aws-serverless-twitter-event-source)中获取带有特定短语或单词的推文。最终，您将能够使用 SAR 构建应用程序。AWS SAR 还不具备嵌套纳米服务的能力，而这正是下一个工具的优势所在。

就在最近，无服务器框架发布了组件。我为此激动！虽然类似于 AWS SAR，但是它允许您嵌套组件，这是重新创建配置管理的角色、概要文件和服务模式的关键。GitHub 知识库中的大多数当前示例都是类似于角色的高阶服务。注册表中的[当前组件](https://github.com/serverless/components/tree/master/registry)是有限的，但我知道他们正在努力增加更多。随着组件注册中心的增长，我可以开始将它们结合起来创建可重用的资源模式，这种模式只需要最少的配置，但也提供了满足工程师需求的灵活性。

有了无服务器框架组件，我们将*最终*能够授权开发人员创建一个具有合理配置和适当监控的 SQS 队列，而不需要我们的定期监督！作为操作工程师，我们的工作将继续构建模式库。它不仅仅是记录不同模式的输入和输出；相反，它将记录何时应该使用模式来满足技术问题，以及它对业务问题的影响。

如果你习惯于写系统文档，那么写架构模式文档也是类似的。

[![infra-as-code-sqs-pattern](../Images/8a6b22eab29cfc6069ea8e6f94f6ef03.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hwtsbBew--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.serverlessops.io/hs-fs/hubfs/blog/infra-as-code-sqs-pattern.png%3Ft%3D1525144819454%26width%3D720%26height%3D482%26name%3Dinfra-as-code-sqs-pattern.png)

## 结论

你的价值不在于基础设施的设置。您的价值在于将您的操作知识和专业技能转化为可重用的代码。

对此有想法吗？在 twitter 上通过 [@tmclaughbos](https://twitter.com/tmclaughbos) 找到我，或者访问[无服务器商店主页](https://www.serverlessops.io/)并通过网站聊天。在[的无服务器运营博客](https://www.serverlessops.io/blog)上还有更多像这样的博客。

[![](../Images/8f8a489164a086d48b103f5bf1de3cbb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eTzPgQeA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://track.hubspot.com/__ptq.gif%3Fa%3D277116%26k%3D14%26r%3Dhttps%253A%252F%252Fwww.serverlessops.io%252Fblog%252Fserverless-ops-infrastructure-as-code-with-aws-serverless%26bu%3Dhttps%25253A%25252F%25252Fwww.serverlessops.io%25252Fblog%26bvt%3Drss)