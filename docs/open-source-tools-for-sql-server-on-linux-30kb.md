# Linux 上 SQL Server 的开源工具

> 原文：<https://dev.to/spboyer/open-source-tools-for-sql-server-on-linux-30kb>

[![Open source tools for SQL Server on Linux](img/72d97d2a5ae12cb8d9334503e8a6f74a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--H3qQp3bb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://tattoocoder.com/conteimg/2018/07/pablo--4-.png)

我喜欢伟大的工具，如果它是开源的就更好了。

最近，我写了一篇关于[为什么我们应该关注容器](https://tattoocoder.com/why-should-we-care-about-containers-for-development/)的文章，其中我在一个容器中使用了 Linux 上的 SQL Server，并为一个 ASP.NET 核心应用程序加载了一些测试数据，而不是使用完整的数据库服务器。

[Linux 上的 SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-overview?view=sql-server-linux-2017&WT.mc_id=none-devto-shboyer)有一个内置的命令行工具，用于连接和执行针对数据库的命令。然而，每次你使用它时；它需要登录，密码等。

```
/opt/mssql-tools/bin/sqlcmd -S localhost -d Names -U SA -P $SA_PASSWORD -I -Q "SELECT TOP 5 ID,FullName FROM Names;" 
```

Enter fullscreen mode Exit fullscreen mode

## mssql-cli

介绍 [mssql-cli](https://github.com/dbcli/mssql-cli) ，一款全新的交互式跨平台 cli 工具。该工具为命令行带来了现代体验，包括语法突出显示、智能感知、多行模式、特殊命令和许多配置设置。与 sqlcmd 中的交互体验相比，这是一个巨大的升级。

[![Open source tools for SQL Server on Linux](img/fa957dbd1293a095e3d378d7befba278.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Jzt4PhYx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/dbcli/mssql-cli/raw/master/screenshots/mssql-cli-autocomplete.gif)

### 添加到容器中

为了在 Linux 容器(Ubuntu 16.04)上的我的 SQL Server 中安装 **mssql-cli** 工具，创建一个 docker 文件并使用 Ubuntu 16.04 文档中的说明。

```
FROM microsoft/mssql-server-linux:latest

# Import the public repository GPG keys
RUN wget -qO- https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

# Update the list of products
RUN apt-get update

# install curl
RUN apt-get install curl -y

# Register the Microsoft Ubuntu repository
RUN curl -o /etc/apt/sources.list.d/microsoft.list https://packages.microsoft.com/config/ubuntu/16.04/prod.list

# Update the list of products
RUN apt-get update

# Install mssql-cli
RUN apt-get install mssql-cli 
```

Enter fullscreen mode Exit fullscreen mode

构建并运行映像。

```
# build the image and tag it sql1
docker build -t sql1 .

# run the image, accepting the EULA and setting the user/password for sa.
# also used --name for ease instead of using ID
docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=!Passw0rd' -p 1433:1433 --name sql1 -d sql1 
```

Enter fullscreen mode Exit fullscreen mode

Exec 进入容器并启动**MSSQL-CLI**T2】

```
docker exec -it sql1 mssql-cli 
```

Enter fullscreen mode Exit fullscreen mode

启动时，会提示您输入用户名/密码。

[![Open source tools for SQL Server on Linux](img/1d1afa5863b78d1d59d48f69e01702ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--s9XQOIv3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://tattoocoder.com/conteimg/2018/07/Screen-Shot-2018-07-17-at-12.20.03-PM.png)

## SQL 操作工作室

*   [下载&安装](https://docs.microsoft.com/sql/sql-operations-studio/download?view=sql-server-2017&WT.mc_id=opensource-devto-shboyer)
*   [快速入门:使用 SQL Operations Studio(预览版)连接并查询 Azure SQL 数据库](https://docs.microsoft.com/sql/sql-operations-studio/quickstart-sql-database?view=sql-server-2017&WT.mc_id=opensource-devto-shboyer)

如果你更喜欢 GUI 体验， [SQL Operations Studio](https://docs.microsoft.com/sql/sql-operations-studio/what-is?view=sql-server-2017&WT.mc_id=opensource-devto-shboyer) 是一个新的跨平台和[开源](https://github.com/Microsoft/sqlopsstudio)数据库管理工具，可以与 SQL Server、Azure SQL DB 和 Azure SQL 数据仓库协同工作。

SQL Operations Studio 从 VS 代码派生而来，使用了 VS 代码的许多优秀的编辑器特性，同时添加了用于与 SQL Server 数据库交互的现代 UI。此外，还有一个类似于 VS 代码的扩展性管理器，以便 DBA 和开发人员可以选择他们想要的特性。

### 连接到数据库

为了连接到 docker 容器，端口 1433 被映射到 **localhost:1433** 。因此， **localhost** 就是服务器。

[![Open source tools for SQL Server on Linux](img/3001fc80b98e408eac2f3756f86fb5c0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VFq4jPFv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://tattoocoder.com/conteimg/2018/07/Screen-Shot-2018-07-17-at-12.28.54-PM.png)

SQL Ops Studio 除了运行查询之外，还有很多强大的功能。您也可以[构建出色的服务器和数据库管理仪表板](https://docs.microsoft.com/sql/sql-operations-studio/insight-widgets?view=sql-server-2017&WT.mc_id=opensource-devto-shboyer)。

[![Open source tools for SQL Server on Linux](img/83d9b59e2ed2120d34b8f44eede1ced6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dB43jnRd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://docs.microsoft.com/en-us/sql/sql-operations-studio/media/insight-widgets/database-dashboard.png%3Fview%3Dsql-server-2017)

代码是开源的，试一试并提供反馈-[https://github.com/Microsoft/sqlopsstudio](https://github.com/Microsoft/sqlopsstudio)