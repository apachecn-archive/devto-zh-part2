# [已停止]在零配置的情况下运行绿卡本地 HTTPS 服务器

> 原文：<https://dev.to/alshakero/run-a-green-badge-local-https-server-with-zero-configuration-30lh>

[![image](img/629ab26eaef7970946c022e0e1739d5d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3NOTXyMn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/199fub9gos4wks74r312.jpg)

⚠️:显然，这违反了 letsencrypt 的服务条款，我不能以这种方式使用他们的证书。我不得不扔掉这个工具。我本可以发布一个非免费的证书来维护这个工具，但是发布一个正式注册到我名下的私钥听起来并不是一个好主意。

*没有证书创建，没有隧道，没有麻烦⚡️*

### 在当地使用 HTTPS 的重要性

有人可能会认为，当地的发展不需要 HTTPS。但事实并非总是如此。您需要测试混合内容、安全 cookies、服务人员等。@getify 的这个帖子完美地说明了这一点。

> ![](img/03cad1abc208c18e4958e4b0e016bcc2.png)getify@ getify![](img/4d9c44713c216584b3d48ff3455cbb68.png)我如此频繁地从别人那里得到“localhost 不需要本地 https”的再解释。眼球转动。
> 
> 这并不是普遍适用的。这是我最后一次解释了。从今以后，它将只与。2018 年 7 月 28 日下午 13:42[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1023202051902373888)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1023202051902373888)457[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1023202051902373888)1071

运行一个本地的 HTTPS 服务器可能会很麻烦。您必须创建自己的证书，将它们添加为可信证书等。你知道交易。

使用隧道，至少是免费的选择，也不是最好的体验，他们每次都创建一个不同的 URL，他们会变得非常慢，第三方会以纯文本的形式访问你的应用程序。

但是，如果您可以使用一个静态的、简短的、易于记忆的 URL 来访问您的应用程序，而不需要第三方的参与，会怎么样呢？ez-s 正是这么做的。你运行`ez-s`并访问`https://ez-s.io`，你会看到你的应用带有绿色徽章。

### EZ-S 是什么？

这是一个 [zeit/serve](https://github.com/zeit/serve) 的分支，让你能够在本地运行一个绿卡 HTTPS 服务器。你可以在 GitHub [这里](https://github.com/alshakero/ez-s)看到。

### 快步走

```
$ npm i -g @alshakero/ez-s
$ ez-s
# => go to https://ez-s.io to see your app 
```

### 它是如何工作的？

1.  域 ez-s.io 有一个指向 IP 地址 127.0.0.1 的 DNS 记录。
2.  ez-s 包包含 letsencrypt 生成的 SSL 证书。
3.  还有一个小型的 HTTPS 服务器，它使用包括私钥在内的上述证书。因此，当您访问 ez-s.io 时，所提供的证书将与 letsecrypt 的证书相匹配，您将获得一个绿色徽章。主机的 IP 地址无关紧要。只要 letsencrypt 记录与服务器提供的证书匹配，Chrome 就不会反对。

等等什么？公钥私钥？！是的。因为域将永远指向 127.0.0.1，所以模拟它不会将模拟者带到任何地方。除非冒充者可以控制受害者的 DNS 服务器，这使得受害者最不担心 ez-s😁

### 警告

由于 ez-s.io 指向`127.0.0.1`，你的 app 将只能在本地访问。你不能在你的手机上或者用别的机器测试它。

由于这个警告，serve 的`--listen`论证在这种情况下是没有用的。唯一可配置的网络相关参数是端口(`-p`或`--port`)。

### 问题

如果您有任何问题，请在下面评论。