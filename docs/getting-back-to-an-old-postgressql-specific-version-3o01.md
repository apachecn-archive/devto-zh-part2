# 回到旧的 PostgreSQL 特定版本

> 原文：<https://dev.to/gaetanm/getting-back-to-an-old-postgressql-specific-version-3o01>

# TL；速度三角形定位法(dead reckoning)

## 从 9.6.7 回到 9.6.6 的例子

```
brew services stop postgresql 
brew services stop postgresql@9.6

brew uninstall postgresql 
brew uninstall postgresql@9.6

rm -rf /usr/local/var/postgres .psql_history .psqlrc .psql.local .pgpass .psqlrc.local 
```

Enter fullscreen mode Exit fullscreen mode

在此选择具有所需版本[的 Postgres.app 版本。安装它，“初始化”服务器，然后运行:](https://github.com/PostgresApp/PostgresApp/releases/) 

```
sudo mkdir -p /etc/paths.d && echo /Applications/Postgres.app/Contents/Versions/9.6/bin | sudo tee /etc/paths.d/postgresapp 
```

Enter fullscreen mode Exit fullscreen mode

重启外壳。

# 详细信息

我在工作中做的项目的 PostgreSQL 版本是`9.6.6`。问题是我的机器用的是更新的版本。每次我运行`db:migrate`时，它都会导致与`db/structure`文件的冲突。

像大多数运行在 macOS 上的开发人员一样，我通常使用自制软件。它允许通过运行`brew switch formula@version`轻松切换到公式的另一个已安装版本。正如你猜的那样，我试过了，但问题是家酿只处理每个版本的主要版本，我需要一个特定的**次要**版本。

因为我还需要在新旧 Rails 项目之间切换，所以我经常需要更改运行的 PostgreSQL 版本。我目前的解决方案是使用 [Postgres.app](https://postgresapp.com) 。

[![Alt Postgres.app](../Images/d927a6e8b6f21e0cac9792035d7e20a1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Iis1dZT3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uiu88odsbeque9vpswzi.png)

如果您已经通过 Homebrew 在您的系统上安装了 PostgreSQL，但想要使用 Postgres.app 的特定版本(如以前的版本),只需遵循以下步骤:

## 1。删除安装的所有 PostgreSQL 版本

*   检查运行版本`brew services list`然后停止它们`brew services stop postgresql`。

*   删除每个已安装版本中带有`brew uninstall postgresql`和`brew uninstall postgresql@version`的所有已安装公式。

*   然后删除相关文件`rm -rf /usr/local/var postgres .psql_history .psqlrc .psql.local .pgpass .psqlrc.local`。

## 2。安装 Postgres.app

*   [下载具有所需版本的版本](https://github.com/PostgresApp/PostgresApp/releases/)。

*   安装它并创建新的服务器:

[![Alt Postgres.app](../Images/bcceee1259703830c3cc7c710e8d86cb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Tp7K_Fy---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rylmc6d1j63yoo1cjbnq.png)

*   初始化它。

## 3。使 PostgreSQL 相关工具可用于终端

这最后一步允许您使用诸如 pg_dump 等工具。刚刚运行:

```
sudo mkdir -p /etc/paths.d &&
echo /Applications/Postgres.app/Contents/Versions/9.6/bin | sudo tee /etc/paths.d/postgresapp` 
```

Enter fullscreen mode Exit fullscreen mode

(别忘了用你的版本替换 9.6)。

重新启动你的外壳。欢迎回到过去！

如果你像我一样有更好的解决方案在 PostgreSQL 的特定版本之间切换，而不需要外部应用程序，请不要犹豫，分享它！:)