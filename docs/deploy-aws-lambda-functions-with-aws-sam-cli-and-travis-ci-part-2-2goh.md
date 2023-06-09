# 使用 aws-sam-cli 和 Travis-CI 部署 AWS Lambda 函数:第 2 部分

> 原文：<https://dev.to/codevbus/deploy-aws-lambda-functions-with-aws-sam-cli-and-travis-ci-part-2-2goh>

[![sam and travis](img/c76374fb8d1b269124ba720470b5daf7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EFJqaI7---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g0mz9mucfpiun9q8usmv.png)

(本文原载于我的网站: [mikevanbuskirk.io](https://mikevanbuskirk.io/posts/deploy-aws-lambda-functions-with-aws-sam-cli-and-travis-ci-2/) )

在本系列的第 1 部分中，我们用 AWS Lambda 创建了一个基本的无服务器函数。在第 2 部分中，我们将使用 GitHub 和 Travis-CI 来建立一个持续集成工作流。

如果你成功地完成了[第 1 部分](https://dev.to/codevbus/deploy-aws-lambda-functions-with-aws-sam-cli-and-travis-ci-3m9m)，你应该有一个工作的 AWS Lambda 函数。然而，它只存在于开发它的机器上。如果我们想将其部署到 AWS 帐户，该怎么办？有了可用的 S3 存储桶，我们可以简单地使用`sam package`和`sam deploy`为创建一个部署包，并将我们的 Lambda 函数部署到 AWS。

嘣。完成了。

但是，这对于现代软件/基础设施部署来说还不够，不是吗？测试和验证是(或者至少应该是)任何部署的关键部分。*一个系统如何在真实环境中运行*应该在它进入真实环境之前*就决定了。也许不可能 100%模拟每一个场景，或者验证每一个逻辑，但是现代工具可以帮助我们接近。*

### 先决条件

假设我们已经具备了第 1 部分中的所有先决条件，以及可以部署的工作功能。

快速阅读 Travis-CI 介绍性文档可能会有所帮助:

*   [初学者的核心概念](https://docs.travis-ci.com/user/for-beginners) -关于 CI 和 Travis 核心概念的精彩介绍。
*   [入门指南](https://docs.travis-ci.com/user/getting-started)-Travis 使用入门。

安装 Travis gem 可能也会有所帮助。如果你没有红宝石，从这里开始:[https://rubygems.org/](https://rubygems.org/)。然后，在使用 Travis-CI 时，您可以根据需要使用本地 cli 实用程序。

让我们开始吧。

### 我们在做什么？为什么？(续)

继续第 1 部分的主题，我们正在使用 AWS Lambda、Github 和 Travis-CI 为无服务器功能设置 CI 测试和部署工作流。

在第 2 部分中，我们将把代码签入已经设置好的 Github repo。然后，我们将为 Travis-CI 和 sam 设置一些 AWS 资源，用于部署。最后，我们将把所有配置和集成在一起，这样我们就有了一个自动化的 CI 部署系统！

### 将代码提交给 Github

如果你还没有，你应该把你的代码提交给 Github。您应该已经配置了远程 repo。

如果你需要指导，Github 提供了一个[优秀的
教程](https://help.github.com/articles/adding-an-existing-project-to-github-using-the-command-line/)来帮助你进行设置。

要记住的一个关键概念(在 Travis-CI 的核心概念文档中讨论过)是，持续集成(CI)是基于小的代码变更和频繁的代码签入。事实上，Travis-CI 将在每次代码检入时在*运行构建/部署。对于习惯于更传统的开发和部署方法的人来说，这可能是陌生的，甚至是可怕的，但是不用担心！一旦习惯了，频繁部署和频繁签入的范式将成为第二天性，并最终允许对代码更改进行更快的反馈和迭代。*

现在，我们可以继续准备我们的 AWS 资源，以便 Travis-CI 可以成功运行部署。

### 部署您的 AWS 资源

在利用 Travis-CI 推进我们的部署之前，我们需要设置三个 AWS 基础设施。

具体来说，我们将部署一个 S3 存储桶、一个 IAM 用户和一个 IAM 角色。S3 存储桶将用于通过 aws-sam-cli 将代码工件上传到。Travis-CI 将使用 IAM 用户来与 AWS 资源交互并创建 AWS 资源，Lambda 将使用 IAM 角色来查询 EC2 资源。

我非常支持将基础设施作为代码，我通常会使用 Terraform 或 Cloudformation 来部署这些资源。*然而*，我想保持简单并专注于 CI 集成，所以我们将通过 AWS 控制台手动部署这些资源。

登录您的 AWS 帐户。首先创建一个带有默认选项的基本 S3 存储桶。选择一个唯一的名称并保存它以备后用。

接下来，我们需要设置一个 IAM 角色。IAM 角色类似于 IAM 用户，*除了*它们通常被设计为由服务和非人类实体使用/承担。它们是一种非常有用的方式，可以在不存储传统凭证的情况下向您的应用程序和服务*委派权限/访问权限。*

在这种特定情况下，IAM 角色将由 AWS Lambda 服务承担，这样您的函数就有必要的权限来执行它需要的任何操作。您附加到角色的权限将根据功能的用途而有所不同。如果您使用我提供的示例函数，那么对 EC2 资源的一些基本“读取”权限就足够了。

转到 IAM 控制台，创建一个新角色。为服务选择“λ”。对于策略，AWS 实际上提供了一个我们可以使用的现有策略。选择“AmazonEC2ReadOnlyAccess”。在“审查”屏幕上，确保一切正常。给你的函数起一个至少暗示其主要功能的名字。我给我的取名`ReadEC2StatusLambda`。请务必保存角色 ARN 以备后用，因为 IAM 用户策略需要它。

最后，我们需要创建一个 IAM 用户，Travis-CI 可以利用它来与 AWS 集成。从 IAM 控制台，您需要导航到“用户”，在这里您可以查看现有用户，并创建一个新用户。继续点击“添加用户”按钮。首先给用户起一个有意义的名字，这样可以很容易识别它的用途，比如:`TravisServerlessDeploymentUser`。这将是一个
“服务帐户”，因此对于“访问类型”，我们将只选择“编程访问”。

对于权限，选择“直接附加现有策略”，然后点击“创建策略”按钮。选择“JSON”选项卡，并在策略编辑器中输入以下内容:

```
{  "Version":  "2012-10-17",  "Statement":  [  {  "Sid":  "AllowListArtifactBucket",  "Effect":  "Allow",  "Action":  "s3:ListBucket",  "Resource":  "arn:aws:s3:::<your-bucket-name-here>"  },  {  "Sid":  "AllowArtifactBucketUse",  "Effect":  "Allow",  "Action":  [  "s3:PutObjectAcl",  "s3:PutObject",  "s3:GetObjectAcl",  "s3:GetObject",  "s3:DeleteObject"  ],  "Resource":  "arn:aws:s3:::<your-bucket-name-here>/*"  },  {  "Sid":  "AllowLambdaAll",  "Effect":  "Allow",  "Action":  "lambda:*",  "Resource":  "*"  },  {  "Sid":  "AllowCloudFormationAll",  "Effect":  "Allow",  "Action":  "cloudformation:*",  "Resource":  "*"  },  {  "Sid":  "AllowIAMListPolicies",  "Effect":  "Allow",  "Action":  "iam:ListPolicies",  "Resource":  "*"  },  {  "Sid":  "AllowPassLambdaRole",  "Effect":  "Allow",  "Action":  "iam:PassRole",  "Resource":  "<your-role-arn-here>"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

为该策略取一个有意义的名称，类似于您之前创建的 IAM 用户。确保用您之前创建的 bucket 的名称替换两个实例`<your-bucket-name-here>`。您还需要将`<your-role-arn-here>`替换为您在上一步中生成的角色的 ARN。

查看策略并保存它。之后，您将返回到用户
创建过程。点击策略列表上的“刷新”,搜索您刚刚创建的策略。选择策略，然后继续“审查”。如果一切正常，点击“创建用户”。

在下一个屏幕上，您将看到访问密钥和秘密密钥。**确保**你将这些保存在一个安全的地方:这将是**唯一一次**向你展示它们，并且我们将需要它们用于 Travis-CI 集成。

现在，我们可以继续配置 Travis-CI 集成了。

### 配置 Travis-CI 集成

在本系列的第 1 部分中，列出了一些基本的先决条件，包括对 GitHub 和 Travis-CI 的介绍。

通过 OAuth 使用 GitHub 帐户登录 Travis-CI，应该可以让 Travis 集成所有的存储库。

如果需要帮助，请查阅[入门](https://docs.travis-ci.com/user/getting-started/)文档。

登录 Travis-CI 后，您需要为您尝试集成的特定存储库启用它。在您的“个人资料”下，它将列出您所有的公共存储库。如果您想与私有存储库集成，需要在他们的上进行付费订阅。com 网站。完成后，您现在就可以进行 Travis-CI 集成了！

Travis-CI 所需的核心组件是配置文件:`.travis-ci.yml`。这是一个 YAML 点文件，提供了各种配置选项，告诉特拉维斯“如何”构建您的软件。

我将提供我在这个项目中使用的基本文件，并解释相关的部分，而不是对每一个可能的选项(其中有*许多*)进行详尽的分析。我强烈建议您花时间阅读 Travis-CI 的官方文档，以熟悉可用的选项，以及它如何操作的高级概念。

我的配置文件如下:

```
language: python
python:
- '2.7'
branches:
  only: master
install:
- pip install awscli
- pip install aws-sam-cli
script:
- sam validate
- sam package --template-file template.yaml --s3-bucket <your-bucket-name> --output-template-file packaged.yaml
deploy:
  provider: script
  script: sam deploy --template-file packaged.yaml --stack-name travis-serverless-test
  skip_cleanup: true
  on:
    branch: master
notifications:
  email:
    on_failure: always
env:
  global:
  - AWS_DEFAULT_REGION=us-east-2
  - secure: <obfuscated AWS Access Key>
  - secure: <obfuscated AWS Secret Key> 
```

Enter fullscreen mode Exit fullscreen mode

下面我们来走一走配置:

```
language: python 
```

Enter fullscreen mode Exit fullscreen mode

语言设置指定了 Travis 为
项目使用的构建环境。由于我们需要通过 pip 安装`awscli`和`aws-sam-cli`，Python 是自然的选择。

```
python:
- '2.7' 
```

Enter fullscreen mode Exit fullscreen mode

定义项目使用的 Python 版本。在这种情况下，由于对`aws-sam-cli`的依赖，我们需要 Python 2。

```
branches:
  only: master 
```

Enter fullscreen mode Exit fullscreen mode

此设置确保构建仅在提交到回购的“主”分支时触发。

```
install:
- pip install awscli
- pip install aws-sam-cli 
```

Enter fullscreen mode Exit fullscreen mode

“安装”是我们安装构建所需的包/依赖项的步骤。在这个例子中，我们使用 Pip 工具来安装`awscli`和`aws-sam-cli`。

```
script:
- sam validate
- sam package --template-file template.yaml --s3-bucket <your-bucket-name> --output-template-file packaged.yaml 
```

Enter fullscreen mode Exit fullscreen mode

“脚本”块是可以覆盖默认构建步骤的地方。在这种情况下，我们从`aws-sam-cli`包中调用`sam validate`命令来验证我们的 SAM 模板，然后通过`sam package`将我们的功能构建成一个可部署的工件。注意`<your-bucket-name>`应该替换为之前在教程中创建的 S3 铲斗的名称。这是存储打包的部署工件的地方。还要注意一下`--output-template-file packaged.yaml`。这是部署步骤将使用的 YAML 文件。

```
deploy:
  provider: script
  script: sam deploy --template-file packaged.yaml --stack-name travis-serverless-test
  skip_cleanup: true
  on:
    branch: master 
```

Enter fullscreen mode Exit fullscreen mode

“部署”步骤是我们实际将代码部署到我们定义的任何提供者的地方。Travis-CI 直接支持许多不同的提供者:[https://docs . Travis-CI . com/user/deployment/# Supported-Providers](https://docs.travis-ci.com/user/deployment/#Supported-Providers)。

在这种情况下，我们使用通用的`script`提供者，它将我们在构建步骤中生成的任何构建工件传递给定制脚本或命令。我们的命令是`sam deploy`，它引用了构建步骤中的`packaged.yaml`工件，并定义了“堆栈”的名称。我选择了`travis-serverless-test`，但是您可以随意使用您选择的任何命名方案。这实际上是 sam 工具为部署 Lambda 函数而创建的 CloudFormation 堆栈的名称。`skip_cleanup: true`防止 Travis-CI 删除构建过程中所做的任何更改。`on: branch: master`确保仅在主分支机构发生变化时进行部署。一种可能的配置是允许在所有分支上构建，但是只在“主”上部署。

```
notifications:
  email:
    on_failure: always 
```

Enter fullscreen mode Exit fullscreen mode

通知定义了在构建/部署过程中发生事情时如何通知您。在本例中，我将它配置为每当出现故障时通过电子邮件通知。

```
env:
  global:
  - AWS_DEFAULT_REGION=<your-aws-region>
  - secure: <obfuscated AWS Access Key>
  - secure: <obfuscated AWS Secret Key> 
```

Enter fullscreen mode Exit fullscreen mode

“env”块是定义环境变量的地方。这些变量可用于流程的任何阶段。我强烈推荐阅读官方文件:[https://docs.travis-ci.com/user/environment-variables/](https://docs.travis-ci.com/user/environment-variables/)。环境变量是大多数 CI 系统的关键组成部分，熟悉它们将有利于将来的发展。

对于这个特定的用例，我们需要定义 3 个特定的环境变量:

*   `- AWS_DEFAULT_REGION` -定义我们将资源部署到哪个区域，并且是`aws-sam-cli`工具所需要的。我用`us-east-2`。您需要选择最近的区域，该区域提供 Lambda 和 CloudFormation 服务。

*   这些是安全的(加密的)变量。在这个特殊的例子中，我们使用它们来存储我们之前创建的 IAM 用户的凭证。同样，Travis-CI 文档:[https://docs . Travis-CI . com/user/environment-variables/# Defining-encrypted-variables-in-. Travis . yml](https://docs.travis-ci.com/user/environment-variables/#Defining-encrypted-variables-in-.travis.yml)提供了一个很好的资源来解释如何做。

在本例中，我使用了`travis` gem 将加密变量添加到我的`.travis-ci.yml`文件中。如果您担心敏感凭证存储在 shell 历史记录中，如果您在它们前面加上一个空格，大多数 shell 不会向 hist 文件写入命令。

您还可以通过 Travis-CI 站点添加变量，在那里它们将被混淆。但是它们会显示在部署日志中。我强烈推荐使用上面描述的 gem 方法。

现在 Travis-CI 已经配置好了，我们可以开始部署代码了。

### 部署您的功能

您的项目目录看起来应该与此非常相似(不包括`.git*`文件):

```
get-active-ec2-instances
|-- .travis.yml
|-- README.md
|-- event.json
|-- getinstances
|   |-- __init__.py
|   `-- getinstances.py
`-- template.yaml 
```

Enter fullscreen mode Exit fullscreen mode

如果看起来略有不同，那也没关系。只要你至少有一个
`template.yaml`和一个`.travis-ci.yml`文件，以及你的函数，你就可以开始了。

此时，如果您将代码提交并推送到 GitHub，它应该会在 Travis-CI 中触发一个作业。如果您导航到 Travis-CI 站点，UI 应该显示您的存储库和作业状态。请注意“作业日志”，因为它会显示作业的任何错误或相关输出。

如果一切顺利:恭喜你！您刚刚完成了无服务器功能的 CI 部署！

如果出现任何错误，请检查日志的输出，寻找根本原因的线索。Travis-CI 还提供了一些关于[常见故障]([https://docs.travis-ci.com/user/common-build-problems/](https://docs.travis-ci.com/user/common-build-problems/))的文档。

我们要做的最后一步是通过 AWS 控制台验证我们的 Lambda 函数是否部署正确。

### 验证其工作正常

现在是时候确保一切正常运行了。登录您的 AWS 帐户控制台，并导航到“计算”下的“Lambda”服务。确保您处于正确的区域(右上角)。你应该看看你的功能。如果名称后面附加了额外的字母数字字符，那是完全正常的。sam 工具向它部署的每个函数/堆栈附加一个唯一的 ID。

点击你的功能。在界面顶部，您会看到一个“测试”按钮，旁边是一个下拉菜单，上面写着*选择一个测试事件..*

测试事件提供了来自 AWS 中各种事件的模拟 JSON 有效负载，用于触发 Lambda 函数。继续从“事件模板”下拉列表中选择“预定事件”。给你的事件起一个有意义的名字，比如“TestServerlessEvent”。

保存活动后，单击“测试”。根据您的函数，您将看到在日志中生成的输出，或者发送到某个地方。在本教程提供的例子中，它应该显示一个列出活动 EC2 实例的 JSON 块。

### 总结起来

我希望本系列文章至少为每个人开始思考简化无服务器开发和部署的方法提供了一个基础。

这个项目的一些潜在想法:

*   使用内置的 Travis-CI Lambda 提供程序。
*   将 API 网关添加到项目中。
*   为构建创建更复杂的提交/合并工作流。
*   向构建中添加单元测试和代码覆盖率。
*   添加集成和功能测试来验证部署。

评论？有问题吗？问题？请在评论中发表。

下次见！