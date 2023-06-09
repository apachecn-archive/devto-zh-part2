# TablePlus 的基本特性 MySQL、PostgreSQL、SQLite 等的最佳 GUI 工具

> 原文：<https://dev.to/tungtono/essential-features-of-tableplus---the-best-gui-tool-to-manage-mysql-postgresql-sqlite-and-more-31d8>

TablePlus 帮助您在处理多个关系数据库时更加高效。您可以连接到服务器，创建一个新的数据库，以闪电般的速度操作和保存您的数据，高端安全有保证。

目前，TablePlus 只适用于 macOS，windows 版本将很快推出。

从 [TablePlus 官网](https://tableplus.com)下载应用。

### 基本特征

#### 连接服务器

您可以使用 TablePlus 创建到多个数据库的连接:MySQL、PostgreSQL、SQLite、Microsoft SQL Server、Amazon Redshift、MariaDB、CockroachDB、Vertica，甚至 Redis。
你需要准备好主机、端口、用户名、密码进行连接。对于 SQLite，你可以双击数据库文件(通常它有`.db`、`.sqlite`或`.sqlite3`文件扩展名)用 TablePlus 打开。

[![Create a new connection](img/474487093fbfafb7cdd73bdfa8e940d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WAOL7QQS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tableplus.com/asseimg/getting-started/connection-pg.png) 
你也可以导入网址，只需复制粘贴，它为你节省了成吨的时间。

#### 打开任何东西

打 Cmd + P 并键入关键字，TablePlus 将从你的数据库中查找并打开表、视图、函数或快照，非常方便。

[![Open Anything](img/ecf8721f7a4cbef5294ebc0b4956bb6a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TYg9meZv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tableplus.com/asseimg/getting-started/open-anything.png)

#### 编辑数据

TablePlus 有一个类似电子表格的界面，让你以最直观的方式浏览和编辑数据。

[![Edit Data](img/3d31cf114555b8cba17c8dd7da9947ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6FB9pkck--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tableplus.io/asseimg/getting-started/edit-data.png)

记得在编辑后点击 cmd + S 将更改提交给服务器。

#### SQL 查询编辑器

要打开 SQL 编辑器，请按 cmd + E 键。
您可以编写多个查询，并选择同时运行它们或只运行光标处的查询。如果选择运行全部，结果可以拆分并显示在多个选项卡中。

[![SQL Query Editor](img/1aabda2c94548a561c5b0bdf953f2092.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lls_HCsh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tableplus.io/asseimg/getting-started/run-query.png)

#### 创建新表格

您可以从 GUI 工具创建一个新表，而不是运行查询。打开数据库，点击+ new table 按钮，就像你在 macOS 的 Finder 中创建新文件夹一样简单。

[![Create a new table](img/f2a839cad9e96bdbeabc7d1f73789791.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6GL5G7BR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tableplus.io/asseimg/getting-started/create-a-new-table.png)

#### 数据过滤

使用此功能可以快速筛选出您想要查看的记录。您可以一次应用多个过滤器，每个过滤器包含三个部分:

*   列名的下拉列表
*   常见的条件如等于，包含，为空，…
*   一个文本框来填充值。

[![Data Filter](img/0a3321b02141c00503e613e39b0a7d31.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--m57KGa9S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tableplus.io/asseimg/getting-started/filter-data.png)

#### 导出/导入数据

您可以从 CSV 文件导入表，也可以将表从数据库导出到 CSV、JSON 或 SQL 文件。

[![Import Data](img/0d6c838a073e05f58ecedf5e8bca203a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XHSWLHf7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tableplus.io/asseimg/getting-started/import-data-csv.png)

[![Export Data](img/a78dc75ed730c8ec831769a9259739f9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0QVxu0xp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tableplus.io/asseimg/getting-started/export-data.png)