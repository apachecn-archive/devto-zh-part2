# 使用 aws-sam-cli 和 Travis-CI 部署 AWS Lambda 函数

> 原文：<https://dev.to/codevbus/deploy-aws-lambda-functions-with-aws-sam-cli-and-travis-ci-3m9m>

[![sam and travis](img/c76374fb8d1b269124ba720470b5daf7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EFJqaI7---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g0mz9mucfpiun9q8usmv.png)

(本文原载于我的网站: [mikevanbuskirk.io](https://mikevanbuskirk.io/posts/deploy-aws-lambda-functions-with-aws-sam-cli-and-travis-ci-1/) )

随着无服务器计算进入更多工程师和开发人员的工具箱，简化开发和部署选项的需求变得越来越重要。这个由两部分组成的系列将分享我为开发和部署 AWS 的无服务器功能而开发的基本工作流程。

这个工作流将使用 GitHub 和 Travis-CI 分别为版本控制和 CI 提供的普遍存在的免费平台，但它可以适用于更广泛的 VCS 和 CI/CD 平台。

这将是一篇由两部分组成的文章。第一部分将以我们拥有一个带有 aws-sam-cli 工具的本地开发环境以及一个 AWS Lambda 函数的基本工作示例结束。

首先，我们来看看需要的前提条件。

### 先决条件

本系列将假设一些关于 AWS 和无服务器功能等主题的基本知识。理想情况下，读者已经有了一些 AWS Lambda 函数，并且正在寻找一种更加简化和自动化的方式来部署它们。

还假设对版本控制系统有一些基本的了解和经验，特别是 Git/Github。

这个工作流程是在运行 OSX 10.12(Sierra)的 Macbook Pro 上执行的。针对不同平台(如 Linux 或 Windows)的工具安装可能会略有不同。

您需要的工具和服务如下:

*   一个您有权以编程方式向其部署资源的 [AWS](https://aws.amazon.com) 帐户
*   关于[GitHub.com](https://github.com)的报道
*   关于[Travis-CI.org](https://travis-ci.org)的报道
*   最新 [Python 2.7](https://www.python.org/downloads/)
*   [对接员 CE](https://www.docker.com/community-edition#/download)
*   最新安装的 [aws-sam-cli 工具](https://github.com/awslabs/aws-sam-cli)

请注意，aws-sam-cli 工具是工作流的关键部分，它需要本地有效的 aws 用户凭据。这些通常作为 AWS CLI 工具安装的一部分部署到工作站。如果您还没有设置，请按照以下说明进行操作:

*   [安装指南](http://docs.aws.amazon.com/cli/latest/userguide/installing.html)
*   [配置指南](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)

如果您在安装上面提到的任何工具时遇到问题，请查看文档或 GitHub 关于给定项目的问题。你也可以随意发表评论，我会尽力帮忙的。

**免责声明**:您在本教程过程中提供的任何 AWS 资源都可能导致费用计入您的账户。提交人对产生的费用不承担任何法律或其他责任。

### 我们在做什么？为什么？

我在《无服务器计算正在蓬勃发展》这篇文章的引言中简单地提到了它。很容易看出平台所呈现的吸引力。不再需要管理服务器、操作系统更新和防火墙规则。可按需激活的离散、短暂、有限的计算资源块。当一个无服务器的功能可以做同样的事情，而成本和维护开销只占很小一部分时，为什么还要花钱让一个服务器实例每 24 小时运行一次脚本呢？

然而，新的使用模式带来了新的挑战。你如何在本地开发和测试？如何使无服务器开发成为您的 CI/CD 工作流的一部分？

第一个问题很简单:AWS 的工程师开发了一个**伟大的**
工具，带有 [aws-sam-cli](https://github.com/awslabs/aws-sam-cli) 。它为开发、测试和提供 AWS Lambda 函数提供了一种令人难以置信的简化和简单的方法。通过 SAM 和 AWS CloudFormation 提供实时资源。

第二部分呢？如果您想更进一步，自动测试和部署您的功能呢？输入[特拉维斯-CI](https://travis-ci.org) 。如果您不熟悉，Travis-CI 提供了一个免费的持续集成和测试工具，它既强大又易于使用。

正如我之前提到的，这将是一个非常简单的工作流程，但是这里演示的基本概念可以根据需要移植到各种不同的 VCS 和 CI/CD 工具中。

对于无服务器功能，我们将部署一些基本的 Python 代码来生成活动 EC2 实例的列表。如果你想用 GitHub 作为例子，我会通过 GitHub 提供一个完整源代码的链接，但是你也可以把你自己的函数代码移植到本教程中。

让我们开始创建我们的开发环境和功能。

### 创建 GitHub 资源库

首先:我们需要创建一个 GitHub 存储库来存储我们的项目代码，以及与 Travis-CI 的集成。我在这里创建了我的回购[。](https://github.com/codevbus/get-active-ec2-instances)

如果你已经有了一个 GitHub 和 Travis-CI 账户，那太好了！如果没有，不要着急设置它，我们稍后会谈到这一点。

### 创建λ函数

如果你想使用我创建的 Lambda 函数的例子，下载这个文件夹到你的工作目录。

您可以查看下面的内嵌代码。`__init__.py`文件是空的，用于向 Python 表明目录包含包。

```
import boto3
import json
import datetime

client = boto3.client('ec2')

response = client.describe_instances(
    Filters=[
        {
            'Name': 'instance-state-name',
            'Values': [
                'running'
            ]
        }
    ],
    MaxResults=100
)

def get_id(instances):
    """
    returns a list of EC2 instances that are in the 'running'
    state, return object is a list of dicts
    """
    running_instances = []
    for instance in instances["Reservations"][0]["Instances"]:
        running_instances.append({'Instance ID': instance['InstanceId']})
    if not running_instances:
        print("No running instances detected!")
    else:
        return running_instances

def dt_converter(d):
    """
    converts datetime objects to string objects
    """
    if isinstance(d, datetime.datetime):
        return d.__str__()

def encoder(j):
    """
    dumps json objects to strings so any datetime objects
    can be converted to compatible strings before reloading
    as json again
    """
    return json.loads(json.dumps(j, default = dt_converter))

def my_handler(event, context):
    """
    lambda event handler that returns the list of instances
    """
    return encoder(get_id(response)) 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常简单的函数，没有错误处理。这里的主要目的是展示使用 CI/CD 工具来简化无服务器开发的潜力。

### 创建 SAM 模板

sam 本地工具包需要定义一个 SAM 模板。该模板是一个 YAML 文件，用于定义无服务器基础架构运行方式的某些方面。

关于用法的其他文档可在 [aws-sam-cli repo](https://github.com/awslabs/aws-sam-cli#usage) 中找到。

该文件可以在上面提到的 repo 中下载。代码也在下面内联提供:

```
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::Serverless-2016-10-31
Description: Lists active EC2 instances
Resources:
  GetInstances:
    Type: AWS::Serverless::Function
    Properties:
      Handler: getinstances.getinstances.my_handler
      Timeout: 300
      Runtime: python3.6 
```

Enter fullscreen mode Exit fullscreen mode

AWS 在这里提供了关于 SAM 模板
[的各种组件的良好文档。我将在下面介绍模板的相关部分:](https://docs.aws.amazon.com/lambda/latest/dg/serverless_app.html)

*   `Resources:` -我们希望通过模板提供的“资源”的顶级名称。在这种情况下，它只是一个 Lambda 函数。

*   `GetInstances:` -函数名。当使用`sam`工具调用该功能时，会引用该参数。

*   `Type:` -资源的类型。在这种情况下`AWS::Serverless::Function`。

*   `Properties:` -表示当前资源层的后续属性值。

*   这就是奇迹发生的地方。“Handler”属性指向包含函数代码的实际模块。从右向左操作:

    *   `my_handler` -代码本身中事件处理程序的名称。
    *   `getinstances` -指模块/文件`getinstances.py`。
    *   `getinstances` -最左边的索引，是模块代码所在的文件夹。
*   `Timeout:` -一个整数值，以秒为单位，定义函数在超时发生之前可以等待多长时间。

*   `Runtime:` -定义功能代码的运行环境。在本例中，我选择了`python3.6`。

接下来，我们将生成一个示例事件负载来触发 Lambda 函数。

### 生成样本有效载荷

在 AWS 的情况下，Lambda 函数通常通过某种类型的“事件”来触发。各种各样的 AWS 服务生成对触发功能有效的事件。事件负载是 JSON 数据结构，它提供了关于其来源和目的的特定信息。

aws-sam-cli 工具提供了一个[令人惊叹的特性](https://github.com/awslabs/aws-sam-cli#generate-sample-event-source-payloads)，使用户能够为本地开发和测试生成模拟事件有效负载。

出于本文的目的，我们将从 Cloudwatch 预定事件生成一个模拟事件。这些事件的功能类似于 Linux cron 系统，并根据预定义的时间表值触发。

在 repo 的工作目录中运行以下命令:

`sam local generate-event schedule > event.json`

输出保存在`event.json`文件中，这样就可以在需要时重用它来调用函数。

此时，这就是我的项目回购的文件夹结构的样子:

```
get-active-ec2-instances
|-- README.md
|-- event.json
|-- getinstances
|   |-- __init__.py
|   `-- getinstances.py
`-- template.yaml 
```

Enter fullscreen mode Exit fullscreen mode

您的文件名或文件夹名可能略有不同，但至少您应该有模块代码、模板文件和模拟事件。如果一切正常，让我们继续测试 Lambda 函数。

### 测试 Lambda 函数

我最喜欢的 sam 工具特性是本地调用函数的能力。不再上传功能到 AWS 和测试他们的生活，看看他们是否正常工作。

在 repo 的工作目录中运行以下命令:

`sam local invoke "GetInstances" -e event.json`

如果这是您第一次调用这个特定的运行时，该工具将花费一些时间从远程 repo 下载 Docker 映像。在工具报告请求 ID 和调用计费数据之后，将提供函数的输出(如果它生成可读的输出)。

在这种情况下，示例代码生成一个活动 EC2 实例列表，如下所示:

`[{"Instance ID": "i-0123456789abcdef"}, {"Instance ID": "i-1011121314ghijklm"}...]`

如果代码没有检测到任何正在运行的实例，您将获得以下输出:

`No running instances detected!`

如果您遇到任何错误，请查看 AWS 文档以了解常见的失败案例。我倾向于发现我的大多数错误都源于认证/凭证问题。如果你有困难，请在评论中发表。

### 接下来是什么

现在，我们已经为开发和部署 AWS 的无服务器功能建立了一个很好的本地开发环境。但我们还没完。

在第 2 部分中，我们将使用 Travis-CI 工具为 Lambda 函数创建一个集成的测试和部署设置。

敬请期待！