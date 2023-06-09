# 使用 Docker for Windows 在 Linux 容器上运行 SQL Server

> 原文：<https://dev.to/funkysi1701/running-sql-server-on-a-linux-container-using-docker-for-windows-24k3>

最近我一直在研究 Docker 有什么值得大惊小怪的，因为 Docker 在自动化方面非常棒。

我的开发环境通常需要安装 SQL Server。SQL 有点像野兽，有很多选项，需要时间来设置你想要的方式。

然而，由于 Microsoft 现在已经创建了一个在 Linux 上运行的 SQL Server 版本，您可以在 Linux 容器中只使用几个命令来运行 SQL Server。

我假设您已经在开发机器上安装了 Docker for windows。如果没有，请前往[码头](https://docs.docker.com/docker-for-windows/install/#where-to-go-next)并找出方法。

微软在 Linux 容器中设置 SQL Server 的指南可以在[这里](https://docs.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker?view=sql-server-2017)找到。

首先你需要下载图像。在 powershell 窗口中运行:

```
docker pull mcr.microsoft.com/mssql/server:2017-latest 
```

Enter fullscreen mode Exit fullscreen mode

这将下载最新的 sql server 映像。

要运行此映像，请运行以下命令:

```
docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=password"
-p 1433:1433 --name sql
-d mcr.microsoft.com/mssql/server:2017-latest 
```

Enter fullscreen mode Exit fullscreen mode

若要运行 SQL Server 映像，您需要接受条款和条件，并设置默认 sa 密码。这些是作为环境变量添加的，带有-e 标志。

您还需要设置您的容器将运行的端口(1433 是默认的 sql 端口)，并为您的容器命名，在本例中为“SQL”。

如果您已经安装了 SQL Server，您将无法在与本地安装相同的端口上运行容器。要解决这个问题，您可以选择不同的端口。

```
docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=password" 
-p 1434:1433 --name sql
-d mcr.microsoft.com/mssql/server:2017-latest 
```

Enter fullscreen mode Exit fullscreen mode

-p 1434:1433 将容器上的 1433 端口映射到本地环境的 1434 端口。

运行此命令后，您可以使用您提供的凭据将 SQL Server Management Studio (SSMS)连接到(本地)或(本地)1434(如果您使用的是不同的端口),并执行您喜欢的任何 SQL。

如果您的开发环境需要 windows 身份验证，这当然不适合您，如果不需要，您也可以使用。

我一直使用的开发环境有各种 powershell 脚本用于设置。这些假设 windows auth。不过，我已经对它们进行了修改，以接受自定义凭据。

```
$credential = Get-Credential $server.ConnectionContext.LoginSecure=$false 
$server.ConnectionContext.set_Login($credential.UserName) 
$server.ConnectionContext.set_SecurePassword($credential.Password) 
```

Enter fullscreen mode Exit fullscreen mode

Get-Credential 命令创建一个对话框，您可以在其中输入 SQL 凭据，然后将凭据存储在一个变量中，并在脚本的其余部分中使用。

如何将备份文件恢复到我的容器中？

运行:

```
docker exec -it sql mkdir /var/opt/mssql/backup
docker cp database.bak sql:/var/opt/mssql/backup 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个备份文件夹，并将备份文件从本地环境复制到容器中。然后，您可以使用 management studio 来恢复备份文件(或者您可以编写一个 sql 脚本来执行此操作)。恢复数据库时需要注意的一点是，确保文件恢复到 Linux 位置，而不是 windows 位置。

到目前为止，我遇到的唯一问题是缺乏对 SSIS 包的支持和没有 windows 认证。有一些 sql server windows 映像可供使用，但我还没有尝试过，它们可能与其中一些选项配合使用效果更好。