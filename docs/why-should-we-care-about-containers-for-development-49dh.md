# 为什么我们应该关心开发容器

> 原文：<https://dev.to/spboyer/why-should-we-care-about-containers-for-development-49dh>

[![Why should we care about containers for development](img/036b2f45e9aab896df9f4db3bf4c2299.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JeNAgUsF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://tattoocoder.com/conteimg/2018/05/whycontainers.png)

在您的开发生涯中，可能不止一次，您花了几个小时对一个问题进行故障诊断，却发现这是一个依赖或版本问题，对吗？

一个接一个变化的环境、过时的组件和设置开发机器是我们都不能缺少的挫折。

我们已经通过虚拟机解决了其中的一些问题，但是管理整个机器并在每个环境中充分利用它们的成本很高。这就是容器解决许多挑战的地方。

## 为什么是容器

毫无疑问，在过去的一年或更长的时间里，你已经听到了关于容器的讨论。如果不是容器，那么是与之相关的一些技术、框架或工具；Docker，Kubernetes，微服务只是其中的几个。从开发人员的角度来看有什么好处？

## 在我的机器上工作

您的代码或应用程序部署到的每个环境的场景可能是不同的。多少台服务器，CPU、RAM 等的配置。可能因开发、QA 和生产而异，不保证应用程序的性能相同。使用容器提供了在启动时定义这些参数的能力，这样无论在哪里部署，每个映像都有相同的需求。

```
docker run --cpus=2 --memory=1.5g myapp 
```

Enter fullscreen mode Exit fullscreen mode

## 微服务

由许多服务组成的应用程序可能不是用同一种语言或框架开发的。如果你在开发一个服务。NET Core 调用用 Node.js 或 Go 编写的下游服务，负责该服务的开发团队可以构建容器映像，并使其在您的私有注册中心可用。

使用 docker-compose 文件，您可以引入容器映像，而不需要在您的机器上安装 Node.js 或 Go，并根据开发版本测试您的调用。

```
services:

  nodeservice:
    image: nodeservice:dev
    environment:
      - NODE_ENV=Development

  namesweb:
    depends_on:
      - nodeservice
    image: namesweb
    build:
      context: .
      dockerfile: namesweb/Dockerfile
    environment:
      - NODE_SERVICE_ENDPOINT=NodeService-Dev
    ports:
      - "57270:80" 
```

Enter fullscreen mode Exit fullscreen mode

## 测试数据

很少有应用程序不涉及数据，在本地维护生产系统的实例可能是一项任务。安装工具、平台、服务器等。，以及保持所有这些项目都是最新的，这超出了您作为开发人员可能要担心的事情。

在这个 docker-compose 文件中，启动了一个 Linux 上的 SQL Server 实例，并使用一个脚本文件创建了测试数据库，最后，使用[批量复制工具](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-bcp?view=sql-server-linux-2017&WT.mc_id=whycontainers-devto-shboyer)加载测试数据。

```
version: '3.0'  
services:

  mssql:
    image: microsoft/mssql-server-linux:latest
    container_name: mssql
    ports:
      - 1433:1433
    volumes:
      - /var/opt/mssql
      # we copy our scripts onto the container
      - ./sql:/usr/src/app
    # bash will be executed from that path, our scripts folder
    working_dir: /usr/src/app
    # run the entrypoint.sh that will import the data AND sqlserver
    command: sh -c ' chmod +x ./start.sh; ./start.sh & /opt/mssql/bin/sqlservr;'
    environment:
      ACCEPT_EULA: 'Y'
      SA_PASSWORD: P@$$w0rd 
```

Enter fullscreen mode Exit fullscreen mode

### start.sh

```
#!/bin/bash
wait_time=20s

echo creating resources in $wait_time  
sleep $wait_time  
echo starting...

echo 'creating Names DB'  
/opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P $SA_PASSWORD -i ./init.sql

echo 'creating Names Table'  
/opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P $SA_PASSWORD -i ./data/NameTable.sql

echo 'importing data...'  
# Bulk load data from a csv file
/opt/mssql-tools/bin/bcp Names in data/Names.csv -S 0.0.0.0 -U sa -P $SA_PASSWORD -d Names -c -t ','

echo 'add uniqueid column'  
/opt/mssql-tools/bin/sqlcmd -S localhost -d Names -U SA -P $SA_PASSWORD -I -Q "ALTER TABLE Names ADD ID UniqueIdentifier DEFAULT newid() NOT NULL;"

echo 'checking data'  
/opt/mssql-tools/bin/sqlcmd -S localhost -d Names -U SA -P $SA_PASSWORD -I -Q "SELECT TOP 5 ID,FullName FROM Names;" 
```

Enter fullscreen mode Exit fullscreen mode

### init . SQL&name table . SQL

```
DROP DATABASE Names

CREATE DATABASE Names;  
GO

USE Names;  
GO

CREATE TABLE Names(  
  LastName nvarchar(max),
  FirstName nvarchar(max),
  FullName nvarchar(max)
);
GO 
```

Enter fullscreen mode Exit fullscreen mode

使用这个测试数据库映像，可以加载生产数据的子集，供应用程序使用。将`container_name: mssql`设置为连接字符串的**服务器名**。现在，应用程序只需点击该服务器，更改设置即可。

> 如果您对所有容器使用 docker-compose，DNS 名称解析会自动处理。要单独使用数据库容器，请使用命令`docker inspect -f "{{ .NetworkSettings.IPAddress }}" <containerId>`
> 获取 IP 地址

```
"ConnectionStrings": {
    "NamesConnection": "Server=mssql;Initial Catalog=Names;User=sa;Password=P@55w0rd;MultipleActiveResultSets=true"
  } 
```

Enter fullscreen mode Exit fullscreen mode

## 部署

从来就没有简单部署应用程序的场景，对吗？故障回退更难，有时这本身几乎就是另一种部署。

无论你是使用一个完整的 CI/CD 系统来构建你的容器，命令行工具通过`docker build`还是 Visual Studio Docker 工具；当从注册表部署到主机时，所有的依赖项都被隔离到映像，它仍然是同一个容器。

如果部署出现问题，事情就会出错，将部署回滚到某个版本就像将映像的版本号从 1.1 更改为 1.0 一样简单，您的应用程序就会重置。

## 总结

容器为我们的应用程序及其依赖项提供了打包和部署机制。容器注册中心是一个强大的概念，有助于应用程序的部署和分发。

当在本地工作时，容器也改善了我们开发体验的“内循环”,特别是当我们倾向于在单片应用程序上构建微服务时。它们提供了跨本地和远程环境(包括云)的更好的对等性，并帮助我们的基础架构变得更加不可改变。

围绕容器的充满活力的工具生态系统也帮助我们消费云原生平台和开发方法。无论我们是使用无服务器容器、支持容器的 PaaS 平台，还是像 Kubernetes 这样的 orchestrator，我们都专注于我们的应用程序，而不是考虑和管理我们将它部署到的单个或多个主机。

## 资源

*   [Azure 集装箱开发中心](https://docs.microsoft.com/azure/containers/?WT.mc_id=whycontainers-devto-shboyer)
*   [Visual Studio Docker 工具](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/visual-studio-tools-for-docker?view=aspnetcore-2.1&WT.mc_id=whycontainers-devto-shboyer)
*   [Azure 容器实例](https://docs.microsoft.com/azure/container-instances/container-instances-overview?WT.mc_id=whycontainers-devto-shboyer)
*   [Azure Kubernetes 服务(AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes?WT.mc_id=whycontainers-devto-shboyer)
*   [Linux 上的 SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-overview?view=sql-server-linux-2017&WT.mc_id=whycontainers-devto-shboyer)![](img/f1cbd94dc211b1092a3b73ec1a4be6a8.png)