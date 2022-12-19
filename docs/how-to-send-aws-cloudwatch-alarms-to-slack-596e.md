# 如何向 Slack 发送 AWS CloudWatch 警报

> 原文：<https://dev.to/alex_barashkov/how-to-send-aws-cloudwatch-alarms-to-slack-596e>

报警和监控系统是成熟产品和应用的关键部分。如果你担心你的客户，当出了问题时，最好通知他们，而不是蒙在鼓里。如果您将基础设施托管在 AWS 上，获得所用服务的完整指标的唯一方法是使用 CloudWatch。然而，AWS CloudWatch 不仅可以让您访问指标，还可以为特定情况创建警报。但是，如果你以前使用过 AWS，你肯定会注意到界面和某些功能与“用户友好”相去甚远，这意味着向 Slack 发送带有警报的通知不是一项简单的任务。

这篇文章揭示了你应该知道的在五分钟内设置闹钟的秘密。如果你想看所有的行动，请观看我们的视频教程。

要做到这一点，我们需要完成几个步骤:

*   创建 AWS 访问密钥和密码
*   创建一个角色
*   部署 Lambda 函数
*   创建 SNS 主题和订阅
*   创建云监控警报

### 1。创建 AWS 访问密钥和密码

如果您已经有了 AWS 访问密钥和密码，可以跳过这一步。否则，跟随这个[链接](https://console.aws.amazon.com/iam/home#/users)。选择您的用户，然后转到 Security Credentials 选项卡并单击 Create Access Key。

[![](../Images/ea82e77c166a179bae776099e79f1356.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7ftzHZdz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a42inrdah7nv1t3pwcqu.png)

复制 AWS 访问密钥和密码；我们将在配置 Lambda 函数的部署过程中使用它们。

### 创建角色

该角色将由您的 Lambda 函数使用，并且需要权限才能执行某些操作。
点击此[链接](https://console.aws.amazon.com/iam/home?region=eu-west-1#/roles)，然后点击创建角色。

[![](../Images/bea84e8715e7db27ac81401cb55c7efa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qrnY1t94--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ygc5m6qz6cpoue3ttp1a.png)

从列表中选择 Lambda。

[![](../Images/a06afc467acfda2269dded417867c5d1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--f_H0XdrA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6non3yw3cnmml6o64zvs.png)

找到 AWSLambdaBasicExecutionRole 并选择它。

[![](../Images/19f7ffbbf488f44a2fceeda101cb22b0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_Yoc18EG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fh4c9wz6faj23nggmcln.png)

最后，键入一个角色名，比如“cloudwatch-to-slack-role”，单击列表中新创建的角色，并复制 ARN 名称。我们将在部署 Lambda 函数的第 3 步中使用它。

[![](../Images/9c292ff24dacd631c4f079096fd63461.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--G37sakC_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q2kh70h2y9xvdcuy8iel.png)

[![](../Images/c38667a7e1bacfe594b5dc2d42e00c6d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--URj0eaQL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jsw9m2w0k6j1h77jad8o.png)

### 3。部署 Lambda 函数

要部署 AWS Lambda 函数，您需要克隆[存储库](https://github.com/assertible/lambda-cloudwatch-slack)，并在本地机器上安装 Node.js。特别是对于该指南，我们在存储库中做出了重大贡献，以获得更好的配置过程。

```
git clone git@github.com:assertible/lambda-cloudwatch-slack.git
cd lambda-cloudwatch-slack
cp .env.example .env 
```

Enter fullscreen mode Exit fullscreen mode

打开你的。env 文件并填入环境变量。

**UNENCRYPTED_HOOK_URL**
那个变量你应该用 Slack 传入的 Webhook 来填充；在[页面](https://slack.com/apps/A0F7XDUAZ-incoming-webhooks)为您的组织创建一个新的 CloudWatch

[![](../Images/e9b2b243dfd490175cb4129f6bfaa838.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--otpO-0fy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n2xmbedlr7i2bgy4ee21.png)

遵循一个钩子用于一个特定服务的惯例。一旦你创建了一个网页挂钩，选择一个频道，然后发布通知，创建一个用户名(消息的标题)，当然，还要添加一张漂亮的图片。点击此处的链接[下载 Cloudwatch 标志](https://user-images.githubusercontent.com/2697570/46758481-30917080-cccd-11e8-966b-9a2813ff1e8a.png)

[![](../Images/bc0abd7322226ffaf76fd5d54120cca3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oShp5-hn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b6bswo4dmcoz1w2mu3k5.png)

**AWS_FUNCTION_NAME**
将在 AWS Lambda 函数页面中声明的函数的名称。让我们称之为“云监控到松弛”

**AWS_REGION=eu-west-1**
选择了 Lambda 函数的区域。

**AWS _ ROLE = " arn:AWS:iam::123456789123:ROLE/lambda _ exec _ ROLE "**
将第二步中的 arn id 复制到这里。

**AWS_ACCESS_KEY_ID**

现在我们准备好部署了；只需在您的终端中从我们克隆的存储库所在的文件夹中执行以下命令:

```
npm install
npm run deploy 
```

Enter fullscreen mode Exit fullscreen mode

如果一切顺利，你将能够在 AWS 的 Lambda 页面上找到你新安装的函数。

[![](../Images/4c0077127552ca85575ee775009c1a76.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xfEsFNCs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z4g0u1f15ll90srep3y2.png)

### 4。创建 SNS 主题和订阅

在 AWS 中浏览到简单通知服务。具有讽刺意味的是，这项服务一点也不简单，尤其是他们使用的术语和你必须经历的步骤。

转到主题，然后单击创建新主题。我为将要发送的通知选择了名称“cloudwatch-alarms”。

[![](../Images/82d1e8e91b6b33cc84d172db02d01a6e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Yjaq4bJG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qnj419hg97l59tb6uy80.png)

[![](../Images/26a2f08dd47db4f3e754419c4458906e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RE5g8aZB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ua2hg01t0xlacvvlr2yb.png)

打开创建的主题并创建订阅。

[![](../Images/c9501cec7fda97a5c0ce3f4a3aabff92.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RO7_Tsbz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wbfzo3thbffp3heyy8bj.png)

选择 AWS Lambda 作为您的协议，并选择带有您的函数名的端点。

[![](../Images/06a66c28b59f0ce9588f7a88991ca1b9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--akPD5AEz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yu809zug288qf1xi7m30.png)

就是这样；现在我们准备创建一个警报。

### 5。创建云监控警报

通过 Cloudwatch 导航到您的 AWS 帐户，然后单击创建警报。

[![](../Images/acbdcb6057f1bdd37b0b853da6c46a86.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VmrHU9Qo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5tbhwhchg3qplnclwp8j.png)

选择您要监控的指标，定义警报配置，并在发送通知至部分选择您的 SNS 主题名称。

[![](../Images/4a80637d749ff2c1e3d508ad7a488413.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Aa1SM5n3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8kj09dzuw4v5xy6zp0z3.png)

我建议您专门定义一些条件，这些条件会立即抛出警报，以便测试您的功能。如果一切都配置正确，您将在您的 Slack 通道中看到该消息。

[![](../Images/8e0f01027b1495fd826343db487d04fd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6lEuswZd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qdnkeamgg2pq72ow7rlh.png)

我希望这个指南能帮助你节省时间，为你现在或未来的项目设置闹钟。

**补充说明** :
默认情况下，AWS Lambda 的蓝图允许你通过简单的配置做不同的事情，在这些蓝图中，你甚至可以找到 CloudWatch-to-Slack 功能。不幸的是，这向通道发送了非常差的、非结构化的数据。