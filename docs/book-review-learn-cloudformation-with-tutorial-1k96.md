# 书评:学习云的形成(带教程)

> 原文：<https://dev.to/kayis/book-review-learn-cloudformation-with-tutorial-1k96>

在获得 AWS 认证后，我觉得使用 AWS 最基本的工具之一是 CloudFormation (CF)。

*   它允许您用代码定义整个基础设施，以便您可以对其应用版本控制。
*   它允许你用一个命令拆掉所有的资源，所以你在玩的时候不用花钱买任何东西。
*   它允许您在一个帐户中定义您的基础架构，并在另一个帐户中轻松复制它。

在我成为全栈开发人员的过程中，我发现了一本书，作者是 [Agus Kurniawan](https://twitter.com/agusk2010) ，书名是 [Learn CloudFormation](https://www.amazon.com/Learn-CloudFormation-deploy-maintain-infrastructure-ebook/dp/B07FDD15KT) 。

## 我喜欢学习云形成

这本书结构简单。

1.  AWS 云形成简介
2.  构建您的首个 AWS CloudFormation 项目
3.  开发 AWS 云信息模板
4.  AWS 云形成堆栈集
5.  使用 AWS CloudFormation 构建 Lambda 函数
6.  AWS 云信息安全

我喜欢这本书不太长，但把最关键的部分写了下来。库尔尼亚万没有浪费太多时间，直接进入正题。

他首先简要解释了为什么有人应该使用 CF，然后用一个简单的 S3 项目来激励他。然后他解释了使用 CF 的不同方法，比如 AWS 控制台和 CF CLI，并且总是使用 JSON 和 YAML 中的代码示例。虽然这些例子没有具体的章节，但是每个章节都有。

对我来说最突出的部分是第 4 章和第 5 章。CF I 阅读的大多数书籍都没有谈到堆栈集或 AWS Lambda。

堆栈集是从一个 CF 模板向不同的 AWS 区域部署资源的一种方式。多区域部署不是许多公司面临的问题，但很高兴看到一本书解决了这个问题。

我学习 AWS Lambda 的主要资源是通过 AWS 创建的特殊的*无服务器框架*，他们总是试图抽象出大多数需要编写的复杂 CF 代码。虽然这本书没有详细介绍配置，但它至少展示了一些关于如何设置 Lambdas 的好例子。它甚至告诉如何使用堆栈集。

## 我不喜欢学习云形成的原因

这只是我的偏好，但是当我为开发人员写一本书时，我可能会完全忽略 AWS 控制台，可能会专注于 JSON 或 YAML，而不是两者。

既然我自己写了一本书，我就能理解这个问题。一方面，你想写得简洁，另一方面，大多数出版商不会卖一本只有 50 页的书，即使它比任何其他书都更快地让读者开始阅读。

我不喜欢的另一点是一些地方缺乏细节。如果多余的部分(控制台+CLI，JSON+YAML)被删除，剩下的用一些细节填充，直到这本书达到 200 页，这将是一本更好的书。

## 教程

承蒙作者的好意，我为你准备了一个教程，所以你可以画出他的风格。

了解如何使用 Agus Kurniawan 的 AWS CLI 实施云形成项目。

### 设置 AWS 命令行界面(CLI)

AWS CLI 是 Amazon 提供的一个工具，用于在终端模式下管理 AWS。您可以从该终端管理所有 AWS 资源。该工具支持 Windows、Linux 和 macOS。如果您使用 Windows 平台，可以通过选择您的 Windows 版本从以下网站下载该工具:

*   Windows 64 位:[https://s3.amazonaws.com/aws-cli/AWSCLI64.msi](https://s3.amazonaws.com/aws-cli/AWSCLI64.msi)
*   Windows 32 位:[https://s3.amazonaws.com/aws-cli/AWSCLI32.msi](https://s3.amazonaws.com/aws-cli/AWSCLI32.msi)

对于 Linux 和 Mac，您可以通过在 Python 运行时键入 pip 命令来安装 AWS CLI:

```
$ pip install awscli 
```

Enter fullscreen mode Exit fullscreen mode

如果您想通过`pip`升级 AWS CLI，请键入以下命令:

```
$ pip install awscli --upgrade --user 
```

Enter fullscreen mode Exit fullscreen mode

如果您已经安装或升级了 CLI，您可以通过检查其版本来验证 AWS CLI。为此，在终端中键入以下命令:

```
$ aws --version 
```

Enter fullscreen mode Exit fullscreen mode

要使用您当前的 AWS 帐户配置 AWS CLI，请键入以下命令:

```
$ aws configure 
```

Enter fullscreen mode Exit fullscreen mode

您应该准备好所有需要的访问和密钥。建议阅读上的[指南。下一步是在 AWS CLI 上配置安全访问，以支持使用 CloudFormation。](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)

### 配置云形成的安全访问

要从 AWS CLI 使用 CloudFormation，您需要配置您的安全访问和权限。在本演示中，您将学习使用亚马逊 S3 构建和部署 CloudFormation，因此您需要在 AWS CLI 上为 CloudFormation 和亚马逊 S3 配置安全设置。

以下步骤向您展示了如何向 CloudFormation 和亚马逊 S3 添加安全访问权限:

以下步骤向您展示了如何向 CloudFormation 和亚马逊 S3 添加安全访问权限:

1.  打开浏览器并导航至[AWS IAM 管理控制台](https://console.aws.amazon.com/iam)。
2.  进入 IAM 管理控制台后，单击左侧菜单中的**策略**选项。您应该会看到如下屏幕:![](../Images/22da8a6d9a383e96e29eeb654e231011.png)
3.  现在，为 CloudFormation 创建一个自定义策略。
4.  点击**创建策略**，如上图箭头所示。然后，您应该有一个策略表单，如下图所示。
5.  您需要添加 **JSON** 格式的策略脚本。所以，点击创建表单上的 JSON 选项卡，如下图所示:![](../Images/57c347d4c0618316d46cf66111e8b92b.png)
6.  在这种情况下，您将完全访问云的形成。你可以编写这些脚本并粘贴到上面显示的 **JSON** 标签中:

    ```
    {  "Version":  "2012-10-17",  "Statement":  [  {  "Sid":  "Stmt1449904348000",  "Effect":  "Allow",  "Action":  ["cloudformation:*"],  "Resource":  ["*"]  }  ]  } 
    ```

7.  粘贴完脚本后，您可以单击 **Review policy** 按钮，您应该会看到下图所示的表单。

8.  填写您的策略的名称和描述。例如，下面截图中的审核策略的名称是**AWSCloudFormationLRW**:[![](../Images/a73f6026d8699fb44bb73637895124a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QPuV3BSk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g0o40jbaq3m9l2o7vxca.png)

9.  完成后，点击**创建策略**开始创建策略。现在，继续将该策略添加到您的帐户中。

10.  点击左侧菜单中的**用户**部分，选择 AWS CLI 中使用的用户帐户。

11.  在您的 IAM 用户的**摘要**部分，您应该会看到一个类似于下面截图的屏幕: [![](../Images/133c778bfad4acf40a44e934c5127050.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YHKHh8iQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hd81icsg72z4f0zeiwwd.png)

12.  要将您自己的策略添加到您的 IAM 帐户，请在如上所示的**权限**选项卡上点击**添加权限**。您应该会看到如下屏幕: [![](../Images/b9e32772cf23e699bbdc393855a5d35f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fNnY0cNX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6w8niiqwp44ag6g7uwcz.png)

13.  在**授予权限**部分选择**直接附加现有策略**选项，并键入您的策略名称，例如 **AWSCloudFormationLRW** 。你应该看看你自己的政策。点开，如上图截图所示:

14.  选择您的政策后，点击**下一步:查看底部的**。现在，您应该看到如下所示的表单。完成后，点击**添加权限**将该策略添加到您的 IAM 帐户: [![](../Images/04d673b7c4b6ea4daa624e21a3d64db6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H7P5mnTa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pie5ibl0lh92i05sefns.png)

15.  现在，您的 IAM 用户拥有完全访问权限的云形成策略。

16.  使用相同的方法，您还需要将 **AmazonS3FullAccess** 策略添加到您的 IAM 帐户中。

17.  用 **AmazonS3FullAccess** 添加一个已有的策略，如下截图: [![](../Images/c1a4b12679600bd8768a439a2b50e7af.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--72d-KPaR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cbt40myc6y801onec15u.png)

18.  选择 **AmazonS3FullAccess** 策略，然后将其添加到您的 IAM 用户。

19.  完成后，您的 IAM 应该有 CloudFormation 和 **AmazonS3FullAccess** 策略，如下面的截图所示: [![](../Images/28f739a8162b4e82e07486d50381aeb1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VHiHBiLb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fcvt3bldta7bmb7y49t7.png)

现在，您可以从 AWS CLI 管理云的形成。下一步是构建 CloudFormation，然后将其部署到 AWS CloudFormation。

### 构建和部署云编队

现在，您可以使用 AWS CLI 来部署 AWS CloudFormation。要在 AWS CLI 中使用 CloudFormation，您需要了解一些 CloudFormation 命令。[在这里你可以找到所有的造云命令](https://docs.aws.amazon.com/cli/latest/reference/cloudformation/index.html)。

使用与第一个演示相同的`hello-cloudformation.json`模板。您将把这个模板上传到 CloudFormation，然后部署它。

现在打开终端，导航到目录，`hello-cloudformation.json`所在的位置。要创建一个栈，可以使用`cloudformation create-stack`命令。键入以下命令上传模板并创建堆栈:

```
$ aws cloudformation create-stack --stack-name mystackcli1 --template-body file://./hello-cloudformation.json --debug 
```

Enter fullscreen mode Exit fullscreen mode

前面的命令解释如下:

*   `--stack-name mystackcli1`:定义堆栈名称。在这种情况下，堆栈名称是 mystackcli1。
*   `--template-body file://./hello-cloudformation.json`:这是一个模板文件。您应该使用 file://和完整的模板文件路径。在这个演示中，终端已经被导航到一个目录。/hello-cloudformation.json 文件的位置。
*   `--debug`:这是一个启用详细度的参数，因此您可以从 CLI 看到所有详细消息。

如果该操作成功执行，您应该在终端上获得 **StackId** 。

要验证您的操作是否完成，您可以使用 CloudFormation CLI 中的`list-stacks`命令。[在这里你可以找到关于`list-stacks`命令](https://docs.aws.amazon.com/cli/latest/reference/cloudformation/list-stacks.html)的更多信息。

现在，键入以下命令:

```
$ aws cloudformation list-stacks 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到堆栈操作的状态，如下面的屏幕截图所示。堆栈状态可以在 **StackStatus** 选项中找到，用矩形表示: [![](../Images/10c1260ece968f8df953413e0c93fc52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1EI8WKNj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xkv254pie2g0kvnrynr0.png)

您还可以使用`describe-stacks`命令通过传入堆栈名称来找出堆栈的详细信息。在这里你可以找到关于`describe-stacks`命令的信息。对于演示，您可能想要查看名为`mystackcli1`的堆栈的详细信息:

```
$ aws cloudformation describe-stacks --stack-name mystackcli1 
```

Enter fullscreen mode Exit fullscreen mode

执行后，应该会在 **StackStatus** 选项中看到堆栈状态，如下截图所示: [![](../Images/a00459b2d855c6e66bf5f5bd88276ecd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kYyDvB6U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cdyogptl01139zswg24n.png)

如果堆栈的状态为`CREATE_COMPLETE`，则堆栈创建成功。您可以在云形成管理控制台上验证这一点。选择 AWS CLI 使用的区域。以下截图显示堆栈是由 AWS CLI 创建的: [![](../Images/e77310bf31dea7a26a2db8034633c76e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wvfl9Sre--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dmjh691altlakqo5le0p.png)

*如果你觉得这篇文章很有趣，你可以看看 Agus Kurniawan 的 [Learn CloudFormation](https://www.amazon.com/Learn-CloudFormation-deploy-maintain-infrastructure-ebook/dp/B07FDD15KT) 来使用 CloudFormation 启动并运行 AWS automation。了解云形成是启动云形成之旅的基本指南。*