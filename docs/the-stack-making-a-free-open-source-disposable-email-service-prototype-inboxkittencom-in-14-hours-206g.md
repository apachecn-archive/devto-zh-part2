# 堆栈:在 14 小时内创建一个免费的开源一次性电子邮件服务(inboxkitten.com)😼

> 原文：<https://dev.to/uilicious/the-stack-making-a-free-open-source-disposable-email-service-prototype-inboxkittencom-in-14-hours-206g>

(由无服务器小猫提供支持🐈)

一次性电子邮件服务，是非常有用的访问互联网的一部分，坚持有你的电子邮件地址，你不希望给出去。或者用于 web 开发中日常测试的自动化。

你所需要做的就是发一封电子邮件给`unique-name@inboxkitten.com`，去 inboxkitten.com，搜索一下。噗，它应该在那里。不需要注册或密码。

所以我们中的两个人，叶甫盖尼·谢(Eugene Cheah)和[贾明忠(jamingtiong)](https://dev.to/jmtiong)，原本打算在一辆哈克顿车里做一个来取乐。

然而，当我们通常的测试收件箱开始随机故障(太多邮件？).它从“为了好玩”变成了“真的，我们现在就需要它工作(或者昨天，如果可能的话)”。从零到原型的 14 小时疯狂冲刺。

> PS:9 月 22 日[在这里](https://www.producthunt.com/posts/inboxkitten)发布产品搜索，网站在 inboxkitten.com[上](https://inboxkitten.com)

* * *

<figure>

[![mailgun](../Images/097d27136290b4016ae02485a6d04323.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZjzCVvqn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4jmizyczm4gcotsxm1by.png)

<figcaption>(mailgun official announcement on free inbound mail)</figcaption>

</figure>

# 使其自由，用射枪

由于一次性收件箱不需要保存电子邮件超过几个小时，我们不需要完整的`SMTP + FileSystem + Database`设置，这将是一个巨大的痛苦和费用。因此，Mailgun 提供了一个免费的邮件接收服务，附带一个小小的附带条件，那就是最多 3 天的存储空间，比我们实际需要的多 3 倍。呜哇！

* * *

<figure>

[![firebase](../Images/9177431a0556a9cd131af39e7cb559bd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RMySPqYJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wzs5uevgy103nk598n98.png)

<figcaption>(firebase, and its free spark tier + blaze plan)</figcaption>

</figure>

# 通过无服务器 API 使其免费

虽然我们可以创建一个静态站点，用 api 键直接对 Mailgun 进行 javascript 调用，但是拥有私有的 API 键访问互联网不是一个好主意(对于钱包来说)。

用[谷歌永远免费云功能](https://cloud.google.com/functions/pricing)进入 [firebase](https://medium.com/r/?url=https%3A%2F%2Ffirebase.google.com%2Fpricing%2F) ！只要我们每月阅读的电子邮件少于 50，000 封(+/-病毒性邮件)，不管实际发送的电子邮件数量有多少，这都属于免费级别，太好了！

<figure>

因此，在学习了如何正确地与 Mailgun 的 API 交互之后，这是相当简单的，只使用两个端点来列出电子邮件和检索电子邮件。和对 Postman 的快速测试，我们只需要创建 express.js 服务器，用我们的 API 密钥包装这些端点。提供对 Mailgun API 的访问，同时保证我们的密钥安全。

<figcaption>(Note there are a few other free serverless offering, that would probably work the same)</figcaption>

</figure>

* * *

[![vuejs](../Images/e96c3ae09eadd6df406a16326c89257d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BRw35rmM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hj0kdz90d93ek30tszre.png)

# 与我们的 API 交互——通过 UI 甚至 CLI

在为端点完成我们自己的 API 服务器之后，剩下要做的就是为它创建一个友好的用户界面。

使用 vue.js 很快就可以完成一个 3 视图的单页应用程序。

既然我们已经在做了，我们也可以创建它的 CLI 版本。使用 go lang。(只是因为我们想学习如何在 go =D 中这样做)

* * *

[![github](../Images/df816fcb0aa7af9f8990e8d8b8009a73.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yv8HS8Q7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h9b47x5gxwo7tgbgklsb.png)

# 并使其开源

因为我们可以，在 github 上，在麻省理工学院的许可下。

所以，去领养你自己的宝贝小猫吧。

* * *

<figure>

[![uilicious test](../Images/56edb08c8424c2fe1688cebb24856ea4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UprDDSTb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rmja3d5oynevwmdjrxow.gif)

<figcaption>Catfeeding: Uilicious testing inboxkittens XD</figcaption>

</figure>

# 用自动化 UI 测试进行验证

完全免责声明，自然，因为我在 uilicious.com 工作，我会使用他们的平台来测试我的项目。

上面显示的测试用例使用了以下脚本: