# 本地和远程的 Postgresql:如何同步？

> 原文：<https://dev.to/danroc/postgresql-on-local-and-remote-how-do-you-sync-4254>

嗨！！

我花了一些时间为我的小 webapp 构建后端，现在我终于开始做前端了，所以我想把它全部放到网上(Flask with Postgresql)。

我已经在本地数据库中添加了很多数据，所以我将对我的在线 postgresql 服务器进行转储恢复，将它复制到远程服务器。

然而，我仍然希望继续在本地向它添加数据，并且最终通过在线使用后端...

在线数据库应该总是最新最好的。如何让这一切保持同步？

我找到了关于**逻辑复制**的信息，但是对我来说这些都是非常技术性和令人困惑的...我在寻找**真实世界的**案例...

那么如果你也这样做，你是怎么做的呢？？

来自北京的问候
丹尼尔