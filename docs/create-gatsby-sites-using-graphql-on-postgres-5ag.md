# 在 Postgres 上使用 GraphQL 创建 Gatsby 站点

> 原文：<https://dev.to/hasurahq/create-gatsby-sites-using-graphql-on-postgres-5ag>

**TL；DR:**Gatsby 站点的现有 postgres 数据库中的源数据。即时设置。教程/样板文件👉 [gatsby-postgres-graphql](https://github.com/hasura/graphql-engine/tree/master/community/sample-apps/gatsby-postgres-graphql)

[![](../Images/d57ca240083358963e484a1b6a649ab9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HF7PVNfu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/content/images/downloaded_images/create-gatsby-sites-using-graphql-on-postgres-603b5dd1e516/1-Wvut9R2FrxSCawAb-5tPpw.png)

* * *

Gatsby 是一个神奇的框架，可以帮助你构建静态渲染的 react 站点。静态网站，像那些用 Gatsby 构建的网站，使用 cdn 很容易部署和扩展，并为用户提供惊人的体验，因为页面加载“极快”。🤓

但是，在某些情况下，Gatsby 站点所需的数据可能来自数据库，这在许多现实场景中可能会出现。

例如，在最近与 David Eads 的一次谈话中，他告诉我，虽然 Gatsby 在伊利诺伊州 T2 的 ProPublica 得到了有效的应用，但在整合他们使用的一些大型数据集(Postgres)方面存在一个痛点。

但是，那些日子已经一去不复返了🎉因为:

1.  您可以使用惊人的 [gatsby-source-graphql](https://www.gatsbyjs.org/docs/third-party-graphql) 插件将任何 GraphQL API 设置为 gatsby 数据源
2.  您可以使用 Hasura 通过 GraphQL 立即公开现有的 [Postgres 数据库](https://hasura.io/learn/database/postgresql/introduction/)

> ![David Eads profile image](../Images/259aae3ecef7ce7ca07c026546f385be.png)大卫@ Eads![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)哈利路亚！！我昨晚才开始使用这个源码插件，它就像宣传的那样工作。 [@HasuraHQ](https://twitter.com/HasuraHQ) 整合我来了！！[twitter.com/gatsbyjs/statu…](https://t.co/2kAy4L4fBE)2018 年 9 月 25 日晚 22 点盖茨比 @gatsbyjs📣宣布支持用 Gatsby 本地查询第三方 GraphQL APIs！@freiksenet 关于他最近关于盖茨比 https://t.co/3cSLlB0QNv https://t.co/K04YvnLENZ[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1044708144956538880)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1044708144956538880)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1044708144956538880)的博文

Karthik Venkateswaran 整理了一个简洁的小样板和教程，这样你就可以快速开始了！

在 github 上查看。

尝试一下，让我们知道你的想法。如果您有任何问题或遇到任何麻烦，请随时通过 [twitter](https://twitter.com/hasurahq) 、 [github](https://github.com/hasura/graphql-engine) 或我们的 [discord 服务器](https://discord.gg/vBPpJkS)联系我们。