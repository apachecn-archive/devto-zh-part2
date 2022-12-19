# 使用 Terraform 的 AWS 无服务器数据管道-第 2 部分

> 原文：<https://dev.to/diogoaurelio/aws-server-less-data-pipelines-with-terraform---part-2-3fhg>

好了，现在是我们系列的第二篇文章的时候了，重点是 AWS 选项，以无服务器的方式设置管道。我们在本系列中涵盖的主题有:

*   [第 1 部分:Python Lambda 将数据加载到 AWS 红移数据仓库](https://dev.to/diogoaurelio/aws-server-less-data-pipelines-with-terraform---part-1-9jd)
*   [第 2 部分:自动触发λ功能的地形设置](https://dev.to/diogoaurelio/aws-server-less-data-pipelines-with-terraform---part-2-3fhg)
*   第 3 部分:用 AWS Lambda 调度 cron 管道的示例 AWS 步骤函数

在这篇文章中，我们补充了上一篇文章，提供了带有 [Terraform](https://www.terraform.io/) 的基础设施代码，用于部署目的。我们坚信 DevOps 方法也适用于数据工程，也称为“数据操作”。因此，我们认为共享一个样本 Terraform 模块和 Python 代码是非常有意义的。

概括地说，到目前为止，我们有 Python 代码，如果被新 S3 对象上的 AWS 事件触发，它将连接到 Redshift，并发出 SQL Copy 命令语句将数据加载到给定的表中。接下来，我们将展示如何使用 Terraform 代码进行配置。

像往常一样，这篇文章的所有代码都可以在 github 库中公开获得。如果你还没有安装，你需要安装 terraform 来完成这篇文章。

**地形设置**

首先让我们从我们提议的结构开始。我们发现根据需要部署的环境来区分和隔离专用的 Terraform 代码是有用的。一个环境对不同的公司有不同的含义。最常见的环境是开发/试运行/生产，它允许人们在受控的设置中安全地测试代码。然而，还存在其他用例，比如不同技术团队的不同 VPC 或 AWS 客户。

出于这个原因，我们实际上需要在开始时导出环境变量，该变量被用作 Makefile 找到要部署的正确目录的关键字。

```
export ENVIRONMENT=dev
# create a file for terraform secret variables hosting: "$(ENVIRONMENT).tfvars", which our Makefile expects
touch terraform/environments/dev/dev.tfvars
# Verify what the terraform code plans to deploy
make plan 
```

Enter fullscreen mode Exit fullscreen mode

make plan 命令将允许您开始。请注意，Terraform 将使用您在“~/”中配置的默认凭据。AWS/凭证”。如果您想使用不同的方法，一个简单的方法是在运行命令之前导出 aws 访问密钥和秘密值。

无论何时运行“制作计划”，都会运行三个 terraform 命令:“terraform init”、“terraform update”和“terraform plan”。terraform 的“init”和“update”在您第一次初始化 terraform 时特别有用，可以下载您需要的所有特定提供者包，并在您添加新需求时进行更新。

在写这篇博文的时候，这是我第一次运行 make plan 时打印出来的内容:

```
Initializing modules...
- module.redshift_loader_lambda
  Getting source "github.com/diogoaurelio/terraform-aws-lambda-module"

Initializing provider plugins...
- Checking for available provider plugins on https://releases.hashicorp.com...
- Downloading plugin for provider "aws" (1.36.0)...
- Downloading plugin for provider "archive" (1.1.0)...

The following providers do not have any version constraints in configuration,
so the latest version was installed.
To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.archive: version = "~> 1.1"
* provider.aws: version = "~> 1.36"

Terraform has been successfully initialized! 
```

Enter fullscreen mode Exit fullscreen mode

现在，由于我们还没有填充我们的“dev.tfvars ”,您将会注意到以下错误后的 short】

```
##### 1) VPC details: VPC ID and private subnets to use (where the lambda will run)
Error: Required variable not set: private_subnet_ids
Error: Required variable not set: vpc_id

##### 2) Redshift DB details
# The IAM Redshift Role used to issue COPY commands and load data into Redshift
Error: Required variable not set: redshift_data_loader_lambda_iam_role
# The intended Redshift dabase name
Error: Required variable not set: redshift_data_loader_lambda_db_name
# Redshift endpoint or Route53 record to use
Error: Required variable not set: redshift_data_loader_lambda_db_host
# Redshift DB user to use
Error: Required variable not set: redshift_data_loader_lambda_db_user
# Redshift User password to use
Error: Required variable not set: redshift_data_loader_lambda_db_password

# The ARN and name of the bucket where data is being stored (which should be loaded into Redshift)
Error: Required variable not set: s3_bucket_arn
Error: Required variable not set: s3_bucket_name 
```

Enter fullscreen mode Exit fullscreen mode

“*。tfvars”被忽略。您现在应该将您自己的设置的细节添加到您的“dev.tfvars”中，以防您想要测试这段代码。完成此操作后，再次运行“make plan”来查看 terraform 计划部署什么。

**核心 AWS Lambda 功能模块**

好的，在我们部署之前，让我们看一下 terraform 代码，从 Lambda 函数开始。这里我们使用的是我们之前为 Lambda 函数创建的通用模块的特定版本。这个模块的美妙之处在于它的灵活性。例如，您可以在 VPC 内部或外部启动 Lambda，您可以附加您想要的附加 IAM 策略，并为死信队列(DLQ)指定 SNS 或 SQS ARN 以进行故障处理。