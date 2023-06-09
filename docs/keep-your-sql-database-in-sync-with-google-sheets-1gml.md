# 让你的 SQL 数据库与谷歌工作表保持同步

> 原文：<https://dev.to/jorinvo/keep-your-sql-database-in-sync-with-google-sheets-1gml>

这基本上是一个自述文件，但对某些人可能会有用。

我创建了 [googlesheets-sql-sync](https://github.com/jorinvo/googlesheets-sql-sync) ，让用户使用 googlesheets 手动插入数据，同时拥有所有可用的 sql 工具进行进一步处理的能力。

googlesheets-sql-sync 使用 [JDBC](https://github.com/clojure/java.jdbc) 并捆绑 PostgreSQL 驱动程序。
可以随时添加额外的驱动程序。
如果您想添加对 SQLite、MySQL 或任何其他 SQL 数据库的支持，打开一个问题，可能很快就会添加。

## 假设和简化

为了简化同步任务，作出以下假设:

*   同步不会太频繁。想想分钟，而不是毫秒。
*   要同步的表的数量不是太多。也许一百个，但不是一百万个。
*   谷歌表单并不太大。它们可能包含一千行，但不是几百万行。
*   头大多是稳定的。重命名、添加或删除列是个例外。

这允许实现中的一些简化:

*   按顺序执行所有指定的同步任务。有足够的时间做这件事。
*   将对 Google API 的 API 请求限制为每秒一个，以尽可能避免速率限制。
*   每次同步时截断每个表，以确保应用所有更改。
*   当表模式与工作表中的标题不匹配时记录错误，并要求用户手动检查错误。在这种情况下，用户很可能只是放下桌子，然后继续前进。
*   在每个同步间隔之前，从磁盘重新读取配置文件，这允许在系统运行时调整配置。

## 设置

### 安装

1.  确保您安装了 Java 8+。通过运行`java -version`进行检查
2.  从 [Github](https://github.com/jorinvo/googlesheets-sql-sync/releases) 下载最新`googlesheets-sql-sync.jar`。

### 设置谷歌应用

1.  创建一个[新项目](https://console.developers.google.com/projectcreate)或在现有项目中工作
2.  启用[工作表 API](https://console.developers.google.com/apis/library/sheets.googleapis.com?q=sheets)
3.  设置你的应用程序的 [OAuth 同意屏幕](https://console.developers.google.com/apis/credentials/consent)如果这是一个组织内部服务，你最有可能想要设置为*内部*并选择*范围* `spreadsheets.readonly`。
4.  创建一个新的 [OAuth 客户端 ID](https://console.developers.google.com/apis/credentials/oauthclient) 或使用一个现有的
    1.  将*“应用类型”*设置为*“Web 应用”*
    2.  至少设置一个正确的*“授权重定向 URI”*。要使用默认设置在本地机器上运行 googlesheets-sql-sync，请使用[http://localhost:9955/oauth](http://localhost:9955/oauth)
    3.  将*【客户 ID】*和*【客户机密】*放在手边以备后用

### 用法

1.  创建一个空配置文件

```
java -jar googlesheets-sql-sync.jar --init 
```

1.  现在填写配置文件中缺少的信息。

    1.  使用上面的 Google 凭据。
    2.  指定至少一个目标和一个使用该目标的工作表。
    3.  您可以在 [JDBC 文档](https://jdbc.postgresql.org/documentation/head/connect.html)中找到更多 DB 选项。
    4.  将`table`命名为您希望它出现在数据库中的名称。
    5.  要得到一个`spreadsheet_id`，打开一个[你的谷歌表单](https://docs.google.com/spreadsheets)，从你浏览器的地址栏复制`/d/`和`/edit`之间的部分。
    6.  使用`A1:Z10`指定`range`。跳过数字来选择所有的行-像`A:ZZ`。如果您的电子表格包含多个工作表，您也可以通过在范围前加上前缀`SomeSheet!A:ZZ`来指定一个*工作表*。例如，`https://docs.google.com/spreadsheets/d/1q5BNyL7-FnApmkjq45HlKPK-W-pdEmTrtpz0iaHm8p0/edit#gid=0`的`spreadsheet_id`就是`1q5BNyL7-FnApmkjq45HlKPK-W-pdEmTrtpz0iaHm8p0`。
2.  用
    启动程序

```
java -jar googlesheets-sql-sync.jar 
```

1.  系统会提示您访问 OAuth URL 来授权和连接您的 Google 帐户。

2.  成功授权后，触发第一次同步
    ，在指定的时间间隔内将出现更多次同步。

### 无服务器运行

通常你不想仅仅为了一个小的同步工具的 OAuth 而打开另一个端口。
要解决这个问题，您可以在本地机器上运行`java -jar googlesheets-sql-sync.jar --auth-only`，然后将生成的`googlesheets_sql_sync.auth.json`文件复制到您的服务器上，并在服务器上运行`java -jar googlesheets-sql-sync.jar --no-server`

### 定制

该程序可以使用命令行标志进行配置。要查看可用选项，请运行:

```
java -jar googlesheets-sql-sync.jar --help 
```

### 陷阱

*   当你认证了一个 Google OAuth 应用，然后扔掉你的`.auth.json`文件，尝试重新认证时，Google 出于某种原因只会发给你`access_token`和`expires_in`，而不会发给你`refresh_token`。若要解决此问题，请前往 https://myaccount.google.com/permissions 的移除该应用的权限，然后重试。

如果您遇到任何[问题](https://github.com/jorinvo/googlesheets-sql-sync/issues)或者您有任何改进建议，请告诉我。