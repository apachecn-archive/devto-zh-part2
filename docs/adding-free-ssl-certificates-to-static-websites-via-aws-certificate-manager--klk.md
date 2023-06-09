# 通过 AWS 证书管理器向静态网站添加免费 SSL 证书

> 原文：<https://dev.to/kylegalbraith/adding-free-ssl-certificates-to-static-websites-via-aws-certificate-manager--klk>

为我们的网站配置 HTTPS 访问从未像今天这样简单。曾经很痛苦任务现在只需点击几下按钮就能完成，而且通常是免费的。

在这篇文章中，我将解释我们如何配置 AWS 中的静态网站来利用 100%免费的 SSL。

### 先决条件

*   你的静态网站必须托管在一个 S3 桶。你可以在这里学习如何做。
*   要利用 AWS 证书管理器(ACM ),您必须通过 CloudFront 为您的静态网站提供服务。这很容易通过我的指南[这里](https://medium.freecodecamp.org/how-to-make-use-of-cloudfront-for-secure-delivery-of-static-websites-to-the-world-d2f54e8b096)得到设置。

### AWS 证书管理器(ACM)

说到 Amazon Web Services，ACM 是很少有人知道他们可以利用的服务之一。它不像 Lambda、S3 或 Rekognition 那样热门。但是，它功能强大，非常容易上手。它集成了其他服务，如弹性负载平衡、API 网关和 CloudFront。

使用 ACM，您可以请求、续订和部署 SSL/TLS 证书。这些证书随后被用于 ELB、API 网关或 CloudFront。它们很容易获得，100%免费，由 AWS 管理。这意味着在使用 ACM 时，您永远不必担心证书过期。

在这篇文章中，我们将利用 ACM 为我们的静态网站域获取一个 SSL 证书。然后我们将能够把这个证书附加到我们的 CloudFront 发行版中。这样，用户将能够通过 HTTPS 访问我们的网站。

### 请求并批准新的 SSL 证书

在开始之前，我们需要申请并批准一个新的 SSL 证书。此证书适用于我们静态网站的域名。要通过 AWS 控制台请求证书，请按照下列步骤操作。

1.  在 AWS 控制台中切换到 N.Virginia 地区。
2.  导航到 AWS 控制台中的证书管理器。
3.  单击申请证书。
4.  在域名输入中输入*[【www.your-site.com】](http://www.your-site.com)*
5.  单击向该证书添加另一个名称。
6.  在第二个域名输入中输入*your-site.com*
7.  单击下一步。
8.  选择电子邮件验证。
9.  单击查看。
10.  单击确认并请求。

我们也可以通过一个 AWS CLI 调用来完成所有 10 个步骤。

```
$ aws acm request-certificate --domain-name www.my-awesome-site.com 
--validation-method EMAIL --subject-alternative-names my-awesome-site.com --region us-east-1 
```

Enter fullscreen mode Exit fullscreen mode

然后，ACM 向域所有者(在本例中应该是您)发送一封电子邮件来批准新证书。这封电子邮件通常有一个主题行，如“证书批准...”。单击电子邮件中的批准链接。

*重要提示*
要在 CloudFront 中利用我们的新证书，必须在 us-east-1 region (N. Virginia)创建并批准该证书。

### 将 SSL 证书附加到 CloudFront 发行版

随着我们的新证书获得批准和颁发，我们现在可以把它挂接到我们的静态网站分发。

1.  在 AWS 控制台中导航到 CloudFront。
2.  点击我们的静态网站分布的 ID。
3.  单击编辑。
4.  选择自定义 SSL 证书。
5.  从下拉列表中选择我们创建的新证书。
6.  单击是，编辑。

如果在第五步中没有看到证书，则该证书可能不是在 us-east-1 中创建的。

对 CloudFront 发行版所做的大多数更改可能需要一两分钟来传播。一旦更改完成，我们就可以通过 https://www.your-site.com 的 HTTPS 访问我们的静态网站。

### 结论

因为我们的静态网站生活在一个 S3 桶中，我们可以利用其他 AWS 服务来增强它。CloudFront 为我们提供了一个很好的扩展点。使用它，我们可以向最终用户提供低延迟的资源交付。我们还可以将 ACM 证书添加到我们的发行版中，以获得免费的 SSL 连接。

这就是 AWS 提供的力量。个别服务往往很棒，值得学习。但是强大之处在于通过简单的集成产生更好的解决方案的能力。

值得注意的是，AWS 证书管理器允许您引入自己的 SSL 证书。因此，如果您已经拥有付费购买的证书，您可以将其导入 ACM。请记住，导入的证书不由 ACM 管理，因此它们不会自动续订。

### 饿着肚子学亚马逊 Web 服务？

有很多人渴望学习亚马逊网络服务。受到这个事实的启发，我创建了一个课程，专注于通过使用它来学习 Amazon Web Services。关注静态网站的托管、保护和交付问题。通过构建问题的解决方案，您可以学习 S3、API Gateway、CloudFront、Lambda 和 WAF 等服务。

AWS 周围有大量的信息。很容易迷失方向，在学习上没有任何进步。通过解决这个问题，我们可以简化信息，加快你的学习。我用这本书和视频课程的目的是与你分享我所学到的。

听起来有趣吗？查看登录页面了解更多信息，并选择适合您的套餐，[此处](https://www.kylegalbraith.com/learn-aws/)。