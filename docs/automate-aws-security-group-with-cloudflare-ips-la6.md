# 借助 CloudFlare IPs 实现 AWS 安全组的自动化

> 原文：<https://dev.to/johnmccuk/automate-aws-security-group-with-cloudflare-ips-la6>

我是 [Cloudflare](https://www.cloudflare.com/) 的忠实粉丝。即使是基本的免费服务也提供了大量的缓存和安全功能，这需要你自己花很多时间去实现。至少它提供了一个免费且非常简单的 ssl 证书服务。

安全应始终被视为一个分层过程，而 Cloudflare 作为第一道防线发挥着重要作用。在恶意或可疑请求到达您的服务器之前就将其拦截的能力是一项巨大的资产。显然，只有当 Cloudflare 位于您的服务器之前，并且请求被强制通过 Cloudflare 的代理时，这种方法才有效。

[![Cloudlfare protection](img/d6aee993ebd8091918452dac37220d3e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oP_sHSYl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.john-mccracken.com/static/Screen-Shot-2017-02-26-at-20.13.23-2aa87d5b778e172de89f2ed23927f4b5-6080a.png)

对于任何称职的坏人来说，找出服务器的真实 IP 地址并绕过 Cloudflare 都相对简单。您的服务器现在更容易受到攻击，安全性现在取决于您的服务器技能、最新的软件和编码最佳实践。

[![Bad guys, doing their thing!](img/4345e6b56b066d020be44483512eea83.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w9wFfUGe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.john-mccracken.com/static/Screen-Shot-2017-02-26-at-20.15.56-615186bc63e1d4797da793b4028a0680-e5f4c.png)

因此，通过仅允许来自 Cloudflare IP 地址的 web 访问来阻止对您站点的直接访问非常重要。这篇 blob 文章将介绍在 [Amazon Web Services](https://aws.amazon.com/) 环境中这样做:

*   创建安全组
*   将安全组分配给相关的 EC2 实例。
*   创建一个 Lambda 函数来定期检索 Cloudflare 的 IP 地址列表并更新安全组。

如果你不使用 AWS，这个理论仍然是合理的，通过 cron 服务和 iptables 也不需要做太多的调整。

Lambda 代码使用 Python 2.7，并使用 [Boto3](http://boto3.readthedocs.io/en/latest/reference/services/ec2.html#securitygroup) 库与 AWS 通信。

Lambda Python 代码可从 [github 代码库](https://github.com/johnmccuk/cloudflare-ip-security-group-update)获得。我相对来说是 Python 的新手，所以请随意贡献和改进。

## 创建 EC2 安全组

首先创建一个安全组，并记下组 ID。这可以通过 AWS web 界面完成:

[![](img/42fdba59219ec7df64dee16661d1a567.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lumQbpxX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.john-mccracken.com/static/aws-security-group-2-4fb9c9b721fb138410e9b406eba8ae35-c795e.png)

或者从[命令行](http://docs.aws.amazon.com/cli/latest/userguide/cli-ec2-sg.html):

`aws ec2 create-security-group --group-name cloudflare-access --description "http(s) access from Cloudflare IPs only" --vpc-id VPC-ID-GOES-HERE`

记下组 ID，因为它将作为 Lambda 代码的环境变量。

## 创建一个 Lambda 函数

由于这些 IP 地址可能会改变，我们需要一些代码来更新它们。就像在 AWS 环境中一样，我们可以很容易地从一个 Lambda 函数中做到这一点，我们可以定期运行这个函数，几乎不用花什么钱。

如果你不熟悉 AWS Lambda，它的软件即服务设施，你可以在那里放置一些代码(C#、Java Python 或 Node ),你只需为代码运行的时间付费。不需要维护服务器，只需要代码，纯粹的光荣代码！

这是我之前做的一个[的代码库](https://github.com/johnmccuk/cloudflare-ip-security-group-update)。

所需的代码在文件**cf-security-group-update . py**中。对于那些不熟悉的人来说，`def lambda_handler(event, context):`是 Lambda 被触发时调用的主函数。这里发生的事情从描述性的函数名来看应该是不言自明的。

### 选择蓝图

在 AWS 用户界面，选择 ***新功能*** ，从蓝图列表中选择 ***空白功能*** 。

### 配置触发器

接下来，通过点击空白方块并选择***cloud watch Events-Schedule***来设置触发器。
[![](img/3500c5cb3936ca8cd2145708ee45af7d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kQtxlO-a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.john-mccracken.com/static/aws-triggers-a3e919030adbdf84f0c2fc25abe7b9a0-5da69.png)

您可以在这里设置您需要的 CRON 或定义的速率。现在选择每天运行的速率(1 天)。

[![](img/4934b944e5fed7c8ab3094009281c44f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CCsUFfTH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.john-mccracken.com/static/aws-trigger2-b6addd022ed838ed0607e32228e691d5-02e4b.jpg)

### 配置功能

现在给 Lambda 起一个名字，从运行时列表中选择 Python，然后将来自 [github 代码库](https://github.com/johnmccuk/cloudflare-ip-security-group-update)的**cf-security-group-update . py**的内容粘贴到文本区域。

在 ***环境变量*** 部分，添加以下内容:

```
key: SECURITY_GROUP_ID
value: add your security group id here 
```

Enter fullscreen mode Exit fullscreen mode

```
key: PORTS_LIST
value: 80,443 
```

Enter fullscreen mode Exit fullscreen mode

添加 **80** 或 **80，443** 取决于您是否希望将 http 和 https 添加到安全组。

您可能需要在 ***Lambda 函数处理程序和*角色**部分添加一个角色。如果是这样，唯一需要的规则是:

```
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:AuthorizeSecurityGroupIngress",
                "ec2:DescribeSecurityGroups"
            ],
            "Resource": [
                "*"
            ]
        }
    ] 
```

Enter fullscreen mode Exit fullscreen mode

将 ***超时*** 设置为大约 5-8 秒。

保持其余的默认值，继续并回顾，一旦愉快完成通过点击 ***创建功能*** 。

### 测试

点击 ***测试*** 按钮，接受默认值，Lambda 应该可以正常工作了。输出应该列出添加的 IP 地址。

要检查它是否确实添加了它们，请返回 EC2 部分并检查您的 **cloudflare-access** 安全组，希望它包含 IP 地址。尝试删除一些并再次运行 Lambda，它应该会替换它们。

好了，现在您有了一个 Lambda 函数，它将定期运行，并使用任何新的 CloudFlare IP 地址更新指定的安全组，完成！

### 问题

如果它不起作用或者您收到错误消息:

*   **Cloudflare 响应错误** -由于某种原因，cloudflare API 不可用。
*   **无法检索安全组** -使用的组 ID 不正确。
*   如果λ超时，点击 ***配置*** 选项卡，将 ***超时*** 设置为一个更高的值，10 秒应该足够了。
*   任何其他错误都可能是由于安全角色设置不正确造成的。其[栈溢出](http://stackoverflow.com)时间到了！

### 待办事宜

*   添加 IPv6 地址
*   删除旧的 IP 地址

<sub><sup>Automate AWS security group with cloud flare IPs 于 2017 年 4 月 4 日首次出现在我的[博客网站](https://blog.john-mccracken.com)上。</sup></sub>