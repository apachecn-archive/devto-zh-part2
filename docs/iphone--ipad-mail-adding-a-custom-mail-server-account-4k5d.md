# iPhone / iPad Mail:添加自定邮件服务器帐户

> 原文：<https://dev.to/nabbisen/iphone--ipad-mail-adding-a-custom-mail-server-account-4k5d>

[![](img/3aa6638d15a87df67aab269dd0471314.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--k47rvVPq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ziw91yjl45kpblp5fr1k.jpg)

## [T1】简介](#intro)

这个帖子是一个关于如何使用程序而不是如何写程序的小教程。

我将向你展示如何为你的 iPhone / iPad 添加一个由自定义邮件服务器支持的电子邮件帐户，这样你就可以通过苹果的“邮件”应用程序接收/发送他们的电子邮件。
(另外，之后我会演示如何删除。)

这是基于我的个人故事:我帮助我的一个客户在他的 iPhone 上建立了一个这样的电子邮件帐户。他感到非常高兴，因为他能够在办公室之外查看工作邮件。

## 总结

所有截图都是我 iPhone 6S 上的 iOS 12 拍摄的。(还是很好用的！)

邮箱:左边为前的*，右边为*后的*。*

[![](img/eefc21f7b3e645b439cb2c2906a2a02e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SslmpLAW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dmtvge7y6yvk6cz83zfo.jpg)

* *注*:苹果官方添加邮箱账号的页面是[这里是](https://support.apple.com/HT201320)。

<center>✿ ✿ ✿</center>

## 教程:向 iPhone / iPad 添加电子邮件帐户

首先，打开**设置**，点击**密码&账号**:

[![](img/a5b85a861908b36e7fbeeca4befe44c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KISm4Fj2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nt224c011eji9z23rskt.jpg)

接下来，点击**添加账户**，然后选择**其他**:

[![](img/e490352589b0dc21526e0a2514661cb0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FxuLEHsX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7slr0ug2bq195kpsrhi8.jpg)

* *注*:当然可以通过选择添加其他种类的账号如 iCloud、Gmail 等等。

然后，点击**“添加邮件账户”**:

[![](img/53e4d06a03d9f65c91af7146dc9d52c4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--U1OfcHLe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hgt0ail00954i1q6gtwc.jpg)

至少填写**邮箱**(地址)和**密码**，点击**下一步**:

[![](img/36d2560fe9d10e718bf86305ee7bf2d4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_VNMolz3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4wkkocof3vx7pjl94rmw.jpg)

好的。现在你准备好了。

您希望使用哪种协议来接收电子邮件？
选择“ **IMAP** 或“ **POP** ”。
然后填写“**接收邮件服务器**”和“**发送邮件服务器**”的“**主机名**”的所有字段。

* *注*:只有当用户或密码与“接收邮件服务器”不同时，您才需要填写“发送邮件服务器”的所有字段。前者是 SMTP 服务器，后者是 IMAP 或 POP3 服务器。

#### 案例 IMAP

[![](img/8bfbcce4b2fed1ecf0403db0c6ff7e47.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7DBVoUlz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x1yxckda3n31oslyu3yf.jpg)

#### 案例流行

[![](img/b84b58916b59734246795cd891b4d75c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kj6VFsjZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rg9lbtktkh8sl5tlw5n8.jpg)

* * *

#### 温馨提示:应该给每个字段设置什么？

您必须知道它们，或者从您的邮件提供商那里获得它们。

##### 主机名

它是一个类似于“*mail . your-mail-server . domain*”的服务器名称。

##### 用户名

注意这不是一个电子邮件地址。用户名有时等于电子邮件地址，有时不等于。

##### 帕斯沃

请正确输入，尤其是小写/大写字母。

##### (可选)端口

有时您可能需要指定端口号。如果你有直接在屏幕上设置的表格，输入数字，如“995”。
否则，您可能需要在主机名的最后加上冒号，比如“*mail . your-mail-server . domain:995*”。
如果你试了几次都解决不了，你可能不得不寻求帮助。

* * *

完成表格后，点击右上角的“**下一个**”/**保存**。
验证将开始。需要一会儿。

系统有时会发出确认信息:“无法验证服务器身份”。
据我所知，这通常是因为你的服务器使用了 iOS 无法验证的 [SSL 证书。
如果您信任您的服务器，请选择“继续”。
否则，你应该询问邮件提供商。](https://support.apple.com/HT204132/)

* *注意*:当在此选择“取消”时，将出现确认:“无法使用 SSL 连接:您是否要尝试设置没有 SSL 的帐户？”。为了您的安全和隐私，不建议使用 SSL。

[![](img/a8e460375936f2abfbdbbebc8b3b3a75.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1TZG69N1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8ok45kgpesc9qh5590k4.jpg)

完了！
添加的账户将出现:

[![](img/f039f1ed20f697496252ce206a22e554.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EU33dZai--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ijxyc3q4jcblcof8emoz.jpg)

* * *

回到家里，运行邮件，你会看到一个新账户的新邮箱。

[![](img/a7f3663bbfc31713cf77a59c04669561.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mlwWzZjb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xz0vkwbct2yh62ea0m7c.jpg)

<center>✿ ✿ ✿</center>

## 可选教程:删除账户

和添加一样，打开**设置**，点击**密码&账号**:

[![](img/a5b85a861908b36e7fbeeca4befe44c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KISm4Fj2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nt224c011eji9z23rskt.jpg)

选择您要删除的帐户:

[![](img/f039f1ed20f697496252ce206a22e554.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EU33dZai--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ijxyc3q4jcblcof8emoz.jpg)

您会在底部找到“**删除账户**”按钮:

[![](img/abf989b296a38fbd57ffacb54b1990c1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--a4AOBaVu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wjkr49f59goexbpw6syi.jpg)

当您点击它时，将出现确认信息:

[![](img/87e6a40927a08de53e3c2a39e996cbb8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EDrk4dBq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/td7za8jozw0t7ial8bt5.jpg)

如果您继续，您的帐户将被删除。

<center>✿ ✿ ✿</center>

## 其他

还是那句话，那些操作和截图都是基于 iPhone 6S 上的 iOS 12。请注意，版本的差异可能会导致部分错误或信息不足。

如果这能让你的电子邮件交流更舒服一点，我会很高兴。非常感谢您的阅读😃