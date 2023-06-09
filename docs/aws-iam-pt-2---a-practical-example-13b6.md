# AWS IAM 角。2 -一个实际例子

> 原文：<https://dev.to/david_ojeda/aws-iam-pt-2---a-practical-example-13b6>

本系列的第一部分涵盖了 IAM 基础知识和一个通用用例:

[![david_ojeda](img/3982752e278540bbb2ea6a3d4a365f5b.png)](/david_ojeda) [## AWS IAM 角。1 -基础知识

### 大卫奥赫达 7 月 30 日 183 分钟阅读

#aws #iam](/david_ojeda/aws-iam-pt-1---the-basics-139h)

第二部分是第一部分中用例的实现，即创建用户、组和策略来限制对某些 AWS 服务的访问。

* * *

在前一篇文章中，我解释了 AMI 的基础知识，并描述了它的一个用例。现在，我们将实现该问题的实际解决方案。

简单回顾一下，我们有四个开发人员需要共享和不共享 AWS 服务的访问权限，还有一个会计的访问权限非常有限。具体来说:

*   **DevOps 开发者**:控制台和编程访问 ElasticBeanstalk、EC2、S3 和 SQS
*   **Web 开发人员**:对 ElasticBeanstalk 和 S3 的控制台和编程访问
*   **移动开发者**:程序化访问 S3 和 AWS 移动服务，如 Cognito
*   **会计** : S3 对 AWS 计费报告时段的只读访问

让我们开始吧。

* * *

## 创建组

我们需要做的第一件事是创建组——让我们从 **DevOps** 组开始。

1.  转到 IAM 控制台，选择边栏上的 groups 部分
2.  按下**创建新组**按钮

[![](img/b5944777f85cf91d59a5130f949f5457.png "IAM Dashboard with group section selected")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QvCnJt6a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davidojeda.mx/assets/iam/pt2/pt2-1.png)

### 为您的群组命名

我们的小组将被称为 *DevOps* :

[![](img/29cb31d9a502c4caf59906bdf3221dcc.png "IAM Group creation - Group name")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FwWtG0OD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davidojeda.mx/assets/iam/pt2/pt2-2.png)

### 附上保单

这是开始变得有趣的地方。记住，策略是一组“规则”,它将规定允许哪些服务和动作——在这种情况下是针对组的。

在下一个屏幕上，您将看到一个似乎无穷无尽的已经创建的策略列表。这些策略由 AWS 管理，也就是说，它们已经包含了您可能需要的特定权限的规则。幸运的是，有些政策完全符合我们的需求。根据问题的详细信息，DevOps 组需要的策略有:

*   亚马逊 3 完全访问权限:完全访问 S3
*   **awselastbianstalkfullaccess**:对 ElasticBeanstalk 的完全访问
*   **AmazonEC2FullAccess** :对 EC2 的完全访问
*   对 SQS 的完全访问权

选择您所说的这些策略:*该组中的所有用户将拥有对 S3、EC2、ElasticBeanstalk 和 SQS 的完全访问权限。*去争取吧。

**注意事项**:

*   您可以在不附加任何策略的情况下继续下一步。这意味着该组将没有任何权限。
*   如果策略的名称不够详细，您可以随时转到 IAM 控制面板的“策略”部分查看详细信息:

[![](img/6833bd490c5b5aa2e7e0d6a34b407fa0.png "IAM policy details - AWS Cognito Power User")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AC8dG4UE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davidojeda.mx/assets/iam/pt2/pt2-3.png)

### 评审组

创建组的最后一步是检查并确保名称和策略是我们想要设置的。因此，请确保名称和策略是我们想要设置的😛

现在，您已经创建了 **DevOps 组**。继续创建剩下的两个开发人员组。一般过程是相同的，但是您需要一个不同的名称和一组不同的策略。

* * *

## 创建用户

是时候创造我们的每一个用户了。

1.  转到 IAM 控制台，选择侧边栏上的 users 部分
2.  按下**添加用户**按钮

[![](img/279d597434d58f2ecb78f5855d93a921.png "IAM Dashboard with user section selected")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vPBEVm3P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davidojeda.mx/assets/iam/pt2/pt2-4.png)

### 命名您的用户并选择访问类型

我们的第一个用户将是*莎莉*——一名**开发人员**工程师——这些是根据之前陈述的问题细节所需的配置:

[![](img/1e309fa9e8634bedb02a0d459e4c67ed.png "IAM User creation- step one")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EkMENGk6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davidojeda.mx/assets/iam/pt2/pt2-5.png)

因此用户将拥有编程和控制台访问权限。用户将首先输入自动生成的密码，然后被要求重置密码。

### 设置权限

此时，您需要为这个新用户定义权限。您可以:

*   将用户添加到组
*   从现有用户复制权限
*   直接附加现有策略

因为您已经完成了创建具有所需策略的组的繁重工作，所以我们将选择第一个选项:将用户添加到组中。继续并选择您在前面步骤中创建的组:

[![](img/17758ce1387e0243ee9e41d2c8842c64.png "IAM User creation- step two")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AEYoFpPu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davidojeda.mx/assets/iam/pt2/pt2-6.png)

### 回顾

与创建组时一样，检查所有配置是否符合预期，然后继续创建用户。

此时，您将获得用户的凭证及其密码。我建议您下载并通过电子邮件发送给 AWS 用户的目标用户。

现在，您已经根据工作要求将第一个用户分配到了合适的组。现在，您可以继续创建剩余的用户，并将他们添加到所需的组中。

* * *

## 会计组和用户

会计只需要对特定存储桶的读访问权限——这是一个非常有限的权限范围。没有 AWS 托管策略来管理这种情况。**是时候创建自己的政策了**。

## 创建自定义策略

要创建 IAM 策略，请执行以下操作:

1.  转到 IAM 控制台，选择侧边栏上的*策略*部分
2.  按下**创建策略**按钮

[![](img/8d0f24f92df3f7fa4542a0214abf24ad.png "IAM Dashboard with policies section selected")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZnNLQcHX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davidojeda.mx/assets/iam/pt2/pt2-7.png)

现在，您将被重定向到策略创建页面。在此页面上创建策略有两种方式:

*   可视化编辑器
*   JSON 文档

我们将使用可视化编辑器选项，因为我认为它更友好。在这里，他需要选择我们想要访问哪个(些)服务，什么特定动作，以及我们可以访问这个/这些服务的什么特定资源。

我们希望 S3 只读访问计费报告桶。

**注意**:我假设你至少有一个 S3 水桶，可以用来做这个练习。如果你没有，请到 S3 用默认设置创建一个。

您需要添加的第一个权限是:

[![](img/c6eaf0e08d83a101ef352d54f93b84eb.png "IAM Policy creation - Permission one")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AZWQnJkc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davidojeda.mx/assets/iam/pt2/pt2-8.png)

**listallmybutes**和 **GetBucketLocation** 动作与 **All resources** 选项相结合，让用户可以看到你账户上存在的每一个桶。如果您希望用户能够通过 AWS 控制台访问 bucket，那么您实际上需要这个权限。不要担心，除了存储桶名称，他们将不能访问任何内容。

现在，用右下角的按钮添加另一个权限。这一次你只需要 **ListBucket** 动作，并定义你需要访问的 bucket ARN。我的桶叫做*我的公司计费*。因此，选项看起来像这样:

[![](img/9284dbe9b357252dd3d0422856a18cf6.png "IAM Policy creation - Permission two")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GRfD2cCc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davidojeda.mx/assets/iam/pt2/pt2-9.png)

你可以通过在 S3 选择你的桶来得到你的桶 ARN。ARN 是**arn:AWS:S3:::{ YOUR _ BUCKET _ NAME }**。

该权限允许用户列出存储桶的内容，但还不能访问它们。我们缺少最后一个权限，所以让我们添加它。

最后但同样重要的是，该权限将允许用户实际访问所需存储桶上的对象:

[![](img/8eaf70c475c3a0b7d6f700a1bc582935.png "IAM Policy creation - Permission three")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DN3EZXHg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davidojeda.mx/assets/iam/pt2/pt2-10.png)

我们在 bucket 名称后面使用通配符“ ***** ”作为我们的资源 ARN，表示我们希望访问 bucket 中的每个对象。

转到下一步，命名您的策略，添加有用的描述并完成策略创建！我建议您为您创建的每个资源都有一个命名约定。你选择哪种约定没多大关系，但是**坚持**。我使用*camel caps*(thisiskamecaps)，因为 AWS 管理的策略就是这样命名的。

**提示**:您可以使用 [AWS 策略模拟器](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_testing-policies.html)来测试您的策略，并验证它们只提供对所需资源的访问。

### 使用自定义策略

现在，您拥有了具有 S3 只读访问权限的会计自定义策略。要解决上述问题，您需要做的最后一件事是创建您的会计组和用户，并将它们绑定在一起。你可以用目前所学的知识做到这一点😉

# 总结

到目前为止，您已经创建了四个组——三个为开发人员，一个为您的会计。此外，您为每位员工创建了一个用户，并根据他们的工作需要将他们添加到适当的组中。

您的所有用户现在都应该有权限执行他们的工作，每个人都应该感到高兴👏🏼

感谢你阅读我的文章！ ❤️