# 如何用 Heroku Postgres 连接 Datagrip(或任何 intellij IDE)

> 原文：<https://dev.to/tonioriol/how-to-connect-datagrip-or-any-intellij-ide-with-heroku-postgres-23go>

[2020 年 9 月更新]:

额外的`sslmode=require`需要它才能工作。

[原文]:

> Heroku Postgres 强迫你使用 SSL，但是这个连接不是由一个著名的 CA 签名的。为了让它在 DataGrip 中工作，你必须修改一些 JDBC 设置。以下是如何做到这一点:
> 
> 在“添加连接”对话框中，输入用户名、密码、主机名、端口和数据库名称。你必须手动输入这些，因为 DataGrip 的 URL 支持只适用于 JDBC 数据库 URL，而不是 Heroku 使用的更常见的 Postgres URLs。
> 
> 接下来，单击高级选项卡。然后找到 ssl 属性并将其设置为 true。然后找到 sslfactory 属性，将其设置为 org . PostgreSQL . SSL . nonvalidatingfactory。
> 
> 你应该可以走了！

[让 Jetbrains DataGrip 与 Heroku Postgres Andy Fiedler 合作](http://andyfiedler.com/2016/02/getting-jetbrains-datagrip-to-work-with-heroku-postgres)