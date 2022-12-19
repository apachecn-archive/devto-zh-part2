# 基础设施作为代码——用 Terraform 管理 AWS

> 原文：<https://dev.to/frosnerd/infrastructure-as-code---managing-aws-with-terraform-i9o>

这篇博文是我的 AWS 系列的一部分:

*   [**基础设施作为代码管理 AWS 与 Terraform**](#)
*   [使用 Lambda 和 API 网关在 AWS 上部署 HTTP API](https://dev.to/frosnerd/deploying-an-http-api-on-aws-using-lambda-and-api-gateway-g61)
*   [使用 Elastic Beanstalk 在 AWS 上部署 HTTP API](https://dev.to/frosnerd/deploying-an-http-api-on-aws-using-elastic-beanstalk-5dh7)
*   [部署 AWS RDS MySQL 实例并进行基准测试](https://dev.to/frosnerd/deploying-and-benchmarking-an-aws-rds-mysql-instance-2faf)
*   [AWS 中使用社交网络、SQS 和 Lambda 的事件处理](https://dev.to/frosnerd/event-handling-in-aws-using-sns-sqs-and-lambda-2ng)
*   [使用 Terraform 和 Travis CI 在 AWS 上连续交付](https://dev.to/frosnerd/continuous-delivery-on-aws-with-terraform-and-travis-ci-3914)
*   [使用物联网核心、Kinesis 和 ElastiCache 在 AWS 上处理传感器数据](https://dev.to/frosnerd/sensor-data-processing-on-aws-using-iot-core-kinesis-and-elasticache-26j1)
*   [使用 CloudWatch 监控 AWS Lambda 功能](https://dev.to/frosnerd/monitoring-aws-lambda-functions-with-cloudwatch-1nap)

# 简介

如今，公司通常不再需要运行自己的数据中心。公共云和私有云提供商提供不同抽象级别的优秀产品。无论您是需要访问虚拟机管理程序来创建虚拟机，还是只想运行 Python 代码片段，一切皆有可能。这些不同层次的抽象通常被称为 XaaS。从最高抽象开始，通常使用以下分类:

*   软件即服务(SaaS)
*   平台即服务(PaaS)
*   基础设施即服务(IaaS)

尽管使用像亚马逊网络服务(Amazon Web Services)或 T2 谷歌云(Google Cloud)这样的产品可以减少很多复杂性，但管理不同的资源和各个组件之间的依赖关系会变得非常混乱。你如何处理有故障的零件？您如何执行升级和回滚？保养呢？

你可能听说过把你的服务器当成牛而不是猫的比喻。如果一只猫感觉不舒服，你就抚摸它，试着让它开心。如果你的牛发生了这种情况，你就宰了它，换一头新的。你不会投入资源去修复兽群中的一只动物。我是素食主义者，我不赞成屠宰动物，但应用于基础设施的概念对我来说很有意义。

在软件工程中，人们已经开发了软件模块的依赖性管理和版本控制技术。我们能否将这些技术应用到我们的基础设施中？答案是肯定的，我们可以。实现这一目标的重要因素之一是将我们的基础设施视为代码。这就是我们在这篇文章中将要看到的，以及一个使用 Terraform 管理 AWS 资源的例子。

这篇博文的结构如下。在第一部分中，我们想更深入地挖掘基础设施作为代码的概念。第二部分将通过一个简单的例子说明如何使用 Terraform 管理 AWS 上的资源。我们通过总结主要观点来结束这篇文章。

# 基础设施为代码

*infra structure as Code*(IaC)是指通过定义文件而不是交互式配置工具来管理 IT 基础设施的过程。它与“牛不是猫”原则紧密相关，因为重新创建部分基础设施可以基于定义文件完全自动化。

定义文件通常处于版本控制之下。结合持续集成和持续交付管道，可以实现高度自动化。IaC 结合自动化带来三大优势:

*   **再现性。**可以根据定义文件从头开始重建部分或整个基础设施。通过为每个组件使用显式版本，并对定义文件进行版本控制，部署的结果是可重复的。
*   **速度。在执行预先定义的任务时，计算机速度很快。在设置和配置新环境时，尽可能少的人工干预将加快您的部署。**
*   **质量。**由于前两个优势，为您的基础设施实施自动化测试变得可能且相当经济高效。这提高了部署的健壮性和质量。消除人工干预也有助于避免错误。

有许多工具支持 IaC 方法，但都有不同的程度。安塞儿、[木偶](https://puppet.com/)、[厨师](https://www.chef.io/chef/)和[盐场](https://saltstack.com/)就是几个显著的例子。Ansible 是*命令式*，即您声明如何设置基础架构，并按照*推送*原则运行，即它主动转到组件并更改它们。另一方面，Puppet 是*声明性的*，即您描述期望的状态，并按照 *pull* 原则工作，即 Puppet 代理在每个服务器上运行，从 Puppet master 获取最新的配置。

上述所有工具的共同点是都是*配置管理*工具。它们设计用于在不同的服务器上安装软件和管理配置。问题是，如何获得想要管理的服务器？答案是*编排管理*。

编排管理侧重于以正确的顺序创建不同的资源，例如虚拟机，并根据需要将它们连接在一起。与不可变的基础设施相结合，这与我们的“牛不养猫”原则非常相关，问题是您是否真的需要配置管理。每个云提供商都以容器或虚拟机映像的形式提供某种不可变的组件存储。

HashiCorp Terraform 是一款强大的基础设施协调工具。在接下来的部分中，我们想更深入地了解一下 Terraform 的运行情况。

# 地形在行动

## 问题

为了演示 Terraform 的工作流程，我们将在 AWS 上部署一个简单的基础设施。目标是使用 Amazon [弹性计算云](https://aws.amazon.com/ec2/) (EC2)创建一个虚拟机，并使用 SSH 连接它。为此，我们需要配置防火墙以允许端口 22 上的 TCP 流量，并将我们的公钥复制到机器上。

## AWS 设置

在开始之前，我们需要确保可以访问 [AWS 控制台](https://aws.amazon.com/console/)。为此，您首先需要创建一个 AWS 帐户(如果您还没有的话)。然后，您应该创建一个新用户，并分配对 EC2 的完全访问权限，以便我们可以执行所需的操作。

在运行 Terraform 的机器上，比如我们现在的笔记本电脑，我们需要执行一些基本的配置。有多种方法可以做到这一点，但我们将选择基于文件的方法。我们需要在`~/.aws`文件夹中创建一个`credentials`和一个`config`文件，内容如下:

```
# ~/.aws/credentials [default]
aws_access_key_id=<access_key>
aws_secret_access_key=<secret_access_key> 
```

Enter fullscreen mode Exit fullscreen mode

```
# ~/.aws/config [default]
region=eu-central-1
output=json 
```

Enter fullscreen mode Exit fullscreen mode

访问密钥和秘密访问密钥都可以从 AWS 控制台获得。请注意，不建议使用根凭据，而是使用特定用户。您也可以安装 [AWS CLI](https://aws.amazon.com/cli/) ，但这不是 Terraform 工作所必需的。在这里，我们还配置了 AWS CLI 的区域和输出格式，尽管它并不真正影响 Terraform。

请注意，我们正在使用默认的概要文件。如果您已经将 AWS 用于其他目的，您可能希望[创建一个新的概要文件](https://docs.aws.amazon.com/cli/latest/userguide/cli-multiple-profiles.html)。

## 地形部署

### 设置

Terraform 管理的基础设施在`.tf`文件中定义，这些文件是用 [HashiCorp 配置语言](https://github.com/hashicorp/hcl) (HCL)或 JSON 编写的。Terraform 支持基于不同来源(如文件、环境变量、其他资源等)的变量插值。

定义文件是声明性的，描述最终图片应该是什么样子。如果当前状态不满足该定义，则资源将被调整，例如销毁和重新创建，以便匹配期望的状态。

为了初始化一个 Terraform 目录，执行`terraform init`命令。当然你应该已经安装了地形。Terraform 将其状态存储在所谓的*后端*中。对于这个简单的例子，我们将使用*本地*后端，它将状态存储在工作目录中的`terraform.tfstate`文件中。在生产场景中，您可以根据需要选择不同的后端。

初始化还将下载所需的[提供者](https://www.terraform.io/docs/providers/)，在我们的例子中是 AWS。提供者被用作与资源交互的抽象。AWS 提供程序使我们能够管理 AWS 资源。

让我们创建一个名为`example.tf`的新定义文件。

```
# example.tf provider "aws" {
  region     = "eu-central-1"
} 
```

Enter fullscreen mode Exit fullscreen mode

运行`terraform init`将给出以下输出:

```
Initializing provider plugins...
...
* provider.aws: version = "~> 1.21"
...
Terraform has been successfully initialized! 
```

Enter fullscreen mode Exit fullscreen mode

### 基本命令

现在我们需要四个基本命令:`plan`、`apply`、`show`和`destroy`。类似于`init`命令，它们可以使用 terraform CLI ( `terraform <command>`)来执行。

`terraform plan`用于评估您的基础设施的当前状态和期望状态，并告诉您需要进行哪些操作才能使二者相匹配。为了实现完全的可预测性，您可以保存计划，以确保在运行时不会发生意外情况，以防您的当前状态在此期间发生变化。如果在这种情况下，Terraform 检测到任何差异，它将中止申请。

`terraform apply`用于执行所需的操作，以达到所需的基础设施状态。将创建缺失的资源，修改将触发现有资源的变化。

`terraform show`可用于检查基础设施的当前状态。虽然它主要用于调试，但在某些情况下也非常有用。

`terraform destroy`将删除所有已定义的资源。如果您不再需要您的基础架构，或者您的老板告诉您要削减一些成本，这将非常有用。

### 步骤

#### 添加 EC2 实例

首先，我们将基于 Ubuntu 14.04 映像添加一个新的 EC2 实例。注意，我们配置了法兰克福地区(`eu-central-1`)并且标识符`ami-23a48cc8`依赖于地区。您可以使用 [Ubuntu AMI Locator](https://cloud-images.ubuntu.com/locator/ec2/) 来查找您所在地区的正确图像。让我们将这个映像安装在一个`t2.micro`实例上，它包含在[自由层](https://aws.amazon.com/free)中。

```
# example.tf provider "aws" { ... }

resource "aws_instance" "example" {
  ami           = "ami-23a48cc8" # Ubuntu 14.04 LTS AMD64 in eu-central-1
  instance_type = "t2.micro"
} 
```

Enter fullscreen mode Exit fullscreen mode

运行`terraform plan`将告诉我们，一旦我们应用了变更，它将创建新的实例:

```
Terraform will perform the following actions:

  + aws_instance.example
      id:                                    <computed>
      ami:                                   "ami-23a48cc8"
      instance_type:                         "t2.micro" 
```

Enter fullscreen mode Exit fullscreen mode

除了`id`字段之外，还有许多其他的`<computed>`字段，我暂时从粘贴的输出中排除了它们。计算字段意味着一旦执行计划，Terraform 将产生一个值。我们将在后面看到如何直接访问这些计算值，而不是使用`terraform show`。

现在我们已经定义了实例，让我们继续为我们的 SSH 服务器准备公钥认证。

#### 添加公钥

除了在 EC2 中管理虚拟机，AWS 还允许您[管理您的公钥](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)。为了使用我们的公钥/私钥对登录，我们需要在 AWS 中创建一个新的公钥。我们可以向我们的定义文件添加一个新的`aws_key_pair`资源:

```
# example.tf provider "aws" { ... }

resource "aws_instance" "example" { ... }

resource "aws_key_pair" "my-key" {
  key_name   = "my-key"
  public_key = "${file("~/.ssh/id_rsa.pub")}"
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，我们从一个公钥文件中输入了密钥。例如，您也可以将密钥存储在单独的变量文件中，或者直接粘贴它们。

然而，仅仅添加一个公钥是不够的。我们需要将一组防火墙规则与我们的实例相关联，以便将 SSH 流量正确地路由到端口 22。

#### 添加安全组

在 AWS 中，你通过定义[安全规则](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html)来管理进出流量。让我们添加一个新的`aws_security_group`，它在端口 22 上转发任何传入的 TCP 连接。

```
# example.tf provider "aws" { ... }

resource "aws_instance" "example" { ... }

resource "aws_key_pair" "my-key" { ... }

resource "aws_security_group" "allow_ssh" {
  name = "allow_ssh"
  # SSH access
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

看起来不错！现在我们已经有了 EC2 实例，以及公钥和安全规则，我们唯一需要做的就是将它们连接在一起。

#### 更新 EC2 实例

为了声明 Terraform 资源之间的依赖关系，只需使用另一个资源中的变量。通过从`aws_instance.example`资源中访问`aws_key_pair.my-key.key_name`和`aws_security_group.allow_ssh.name`，Terraform 将知道它必须首先创建密钥和安全组。我们现在可以修改我们的示例实例，以使用定义的键和安全组:

```
# example.tf provider "aws" { ... }

resource "aws_instance" "example" {
  ami             = "ami-23a48cc8" # Ubuntu 14.04 LTS AMD64 in eu-central-1
  instance_type   = "t2.micro"
  key_name        = "${aws_key_pair.my-key.key_name}"
  security_groups = ["${aws_security_group.allow_ssh.name}"]
}

resource "aws_key_pair" "my-key" { ... }

resource "aws_security_group" "allow_ssh" { ... } 
```

Enter fullscreen mode Exit fullscreen mode

让我们执行`terraform apply`。首先它将打印计划并要求确认:

```
Terraform will perform the following actions:

  + aws_instance.example
    ...
  + aws_key_pair.my-key
    ...
  + aws_security_group.allow_ssh
    ... 
```

Enter fullscreen mode Exit fullscreen mode

如果我们批准，资源将以正确的顺序创建。Terraform 并行创建独立的资源。

```
aws_key_pair.my-key: Creating...
aws_security_group.allow_ssh: Creating...
aws_key_pair.my-key: Creation complete after 0s (ID: my-key)
aws_security_group.allow_ssh: Creation complete after 2s (ID: sg-0231ae1c04f2f9556)
aws_instance.example: Creating...
aws_instance.example: Still creating... (10s elapsed)
aws_instance.example: Still creating... (20s elapsed)
aws_instance.example: Creation complete after 22s (ID: i-0761f0d410df33154)

Apply complete! Resources: 3 added, 0 changed, 0 destroyed. 
```

Enter fullscreen mode Exit fullscreen mode

太神奇了！但是我们如何知道连接到哪里呢？服务器的名称是什么？我们可以使用`terraform show`或 AWS 控制台来找出答案。但是有一个更好的方法。

#### 结果

我们可以将`output`声明添加到定义文件中，这将使我们在成功执行后就可以访问计算出的值。最后的`example.tf`看起来是这样的:

```
# example.tf provider "aws" {
  region = "eu-central-1"
}

resource "aws_instance" "example" {
  ami             = "ami-23a48cc8" # Ubuntu 14.04 LTS AMD64 in eu-central-1
  instance_type   = "t2.micro"
  key_name        = "${aws_key_pair.my-key.key_name}"
  security_groups = ["${aws_security_group.allow_ssh.name}"]
}

resource "aws_security_group" "allow_ssh" {
  name = "allow_ssh"
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_key_pair" "my-key" {
  key_name   = "my-key"
  public_key = "${file("~/.ssh/id_rsa.pub")}"
}

output "example_public_dns" {
  value = "${aws_instance.example.public_dns}"
} 
```

Enter fullscreen mode Exit fullscreen mode

现在`terraform apply`会给我们服务器名:

```
Outputs:
example_public_dns = ec2-18-184-130-203.eu-central-1.compute.amazonaws.com 
```

Enter fullscreen mode Exit fullscreen mode

执行`ssh ubuntu@<server-name>`如预期成功:

```
Welcome to Ubuntu 14.04.5 LTS (GNU/Linux 3.13.0-149-generic x86_64)
ubuntu@ip-172-31-47-208:~$ 
```

Enter fullscreen mode Exit fullscreen mode

完成后，不要忘记使用`terraform destroy`销毁基础设施，因为不再需要它了。

# 总结和最后的想法

在这篇博文中，我们看到了如何将基础设施视为版本控制代码中定义的不可变组件。基础设施作为代码可以提高部署的可重复性、速度和质量。有许多不同的工具支持这种范式。

使用 Terraform 这样的编排工具以及不可变的容器或虚拟机映像是 Ansible 或 Puppet 这样的传统配置管理工具的有效替代方案。Terraform 支持许多不同的资源提供者，并允许可配置的后端存储其状态。

您使用哪些工具来管理您的基础架构？您以前使用过提到的工具吗？您喜欢配置管理工具、编排管理工具还是两者的组合？请在评论中告诉我你的想法！

* * *

封面图片由 CC BY-SA 3.0、[https://commons.wikimedia.org/w/index.php?curid=1194108](https://commons.wikimedia.org/w/index.php?curid=1194108)授权。