# 蔚蓝的 CORS 是什么？

> 原文：<https://dev.to/flippedcoding/what-is-cors-in-azure-88b>

我最近在做一个涉及 Azure 中 blob 存储的项目。有什么东西阻止我上传我的文件，我花了很长时间才弄明白是什么阻止了它。这是天蓝色的怪异的 CORS 背景。所以像一个优秀的开发人员一样，我去查阅文档，找出如何修复它。

在我让我的项目 blob 存储工作之后，我意识到即使我已经解决了问题，我仍然不知道 CORS 到底是什么。现在我大概知道它是什么，有什么用了，我会试着给你简单解释一下。

CORS(跨来源资源共享)允许您使用来自不同来源的 API 和数据。这方面的一个例子就是使用 Google Maps API。您可以访问这些数据，因为它们允许您的 web 域使用这些数据。这也意味着你可以上传图片、文档或其他数据到 blob 存储器，而不需要先接触数据库，这真的很好。

这里有一个关于为什么需要 CORS 的背景。大多数网络浏览器，如 Chrome 和 Firefox，都使用一种被称为同源策略的安全限制。这会阻止网页调用不同域中的 API。我们不想允许来自不同域的调用的原因是因为跨站点脚本攻击的可能性。这可能会关闭您的网站并危及您的数据库。

这就是为什么了解 CORS 很重要。让我们看一个例子。假设您正在您的网站【https://corsexample.com[上调用来自](https://corsexample.com)[https://google.com](https://google.com)的 API。浏览器将发送带有原始 HTTP 请求头的请求。根据 https://google.com[的 CORS 设置](https://google.com)，你可能会被允许通过。

如果他们有他们的访问控制-允许-起源 CORS 设置配置给你的域访问，那么你就可以了。如果他们没有为您配置访问权限，那么您将会得到一个类似 403 服务器错误的 HTTP 错误。

这是一张简单的图片，展示了当你不是一个被允许的起源时会发生什么。

[![without origin](img/f1e3651d1747a2a2fc055ad22629c602.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C89ZXxFO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mx142mca3h9giyz6m9er.JPG)

这里有一个当你是一个允许的起源:

[![with origin](img/41afb8def8fdde6fd92a26698a7b40b0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mwJaFtWQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/guevk5k50uxelx1evjbc.JPG)

实际上，使用起来非常简单。你所要做的就是在 Azure 里面更新几行代码，然后你就可以开始了。你可以在他们精彩的文档中看到更多:[【1】](https://docs.microsoft.com/en-us/azure/cdn/cdn-cors)[【2】](https://docs.microsoft.com/en-us/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)

这就是 CORS。这是对脚本攻击的防御，您可以对其进行例外处理。希望这能对你未来的项目有所帮助。最起码，希望你能把 CORS 总结给其他人。就像在面试中…

* * *

嘿！你应该在推特上关注我，因为原因:[https://twitter.com/FlippedCoding](https://twitter.com/FlippedCoding)