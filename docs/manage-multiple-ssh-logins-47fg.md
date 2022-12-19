# 管理多个 SSH 登录

> 原文：<https://dev.to/czarpino/manage-multiple-ssh-logins-47fg>

当您有许多远程服务器要登录时，这可能会非常麻烦。如果它们支持 SSH 访问，或者如果您可以配置它们支持 SSH 访问，那么管理对多个服务器的访问将会非常容易。

大多数程序员似乎不知道的是，SSH 可以使用`~/.ssh/config`中的配置文件为 SSH 主机创建各种别名。如果您在 Bash 中使用别名，那么您可以将它视为创建到服务器的 SSH 连接的别名。

```
# Without config
ssh -i ~/.ssh/linode01.id_rsa czar.pino@120.111.122.123

# With config
ssh cp.linode01 
```

Enter fullscreen mode Exit fullscreen mode

通过使用`~/.ssh/config`文件，不再需要每次连接时都提供所有的连接参数。连接到主机所需的所有信息都可以在配置文件中的主机条目中定义。

```
Host cp.linode01
    Hostname 120.111.122.123
    User czar.pino
    IdentityFile ~/.ssh/linode01.id_rsa 
```

Enter fullscreen mode Exit fullscreen mode

这样做的另一个好处是，您还会自动拥有一个您可以连接到的所有 SSH 服务器的文档。不需要维护一个容易过时的电子表格，该表格必须单独更新。

```
Host cp.linode01
    Hostname 120.111.122.123
    User czar.pino
    IdentityFile ~/.ssh/linode01.id_rsa

Host cp.linode02
    Hostname 120.111.122.124
    User czar.pino
    IdentityFile ~/.ssh/linode02.id_rsa

Host cp.ec2-01
    Hostname 121.111.122.123
    User ec2-user
    IdentityFile ~/.ssh/ec2-01.id_rsa

Host cp.ec2-02
    Hostname 121.111.122.124
    User ec2-user
    IdentityFile ~/.ssh/ec2-02.id_rsa 
```

Enter fullscreen mode Exit fullscreen mode

*最初发表于[https://plog.czarpino.com/storing-database-backups/](https://plog.czarpino.com/storing-database-backups/)T3】*