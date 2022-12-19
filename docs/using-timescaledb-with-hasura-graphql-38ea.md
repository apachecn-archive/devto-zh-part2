# 将 TimescaleDB 与 Hasura GraphQL 一起使用

> 原文：<https://dev.to/hasurahq/using-timescaledb-with-hasura-graphql-38ea>

[TimescaleDB](http://bit.ly/2ok88xP) 是第一个原生支持完整 SQL 的开源时序数据库，部署在全球各行业的生产中，用于支持开发运维、物联网、SaaS 和机器学习中的应用。它被打包成一个 Postgres 扩展。由于 [Hasura GraphQL 引擎](https://hasura.io)可以在任何 Postgres 数据库上即时提供 GraphQL APIs，它也可以与 TimescaleDB 一起工作。

例如，您可以使用 [TimescaleDB](http://bit.ly/2ol34t5) 函数创建强大的视图，并使用 [Hasura](https://hasura.io) 将自动提供的 GraphQL 实时查询即时构建实时仪表板。您可以利用 TimescaleDB 的快速摄取功能，并使用应用程序中的 GraphQL 变体快速轻松地插入数据。

在这篇文章中，我们将在一个数字海洋虚拟机上进行设置。

## TL；速度三角形定位法(dead reckoning)

这些是我们将在本指南中涉及的主题:

*   创建一个数字海洋水滴并安装 docker 和 docker-compose
*   使用 docker-compose 运行带有[时标数据库](https://github.com/timescale/timescaledb)的[哈希拉](https://hasura.io)
*   了解什么是超表
*   对超表运行 GraphQL 查询
*   运行 GraphQL 订阅并获取实时数据

## 创建数字海洋水滴

打开[数字海洋控制台，](https://cloud.digitalocean.com/)点击“创建”按钮，从菜单中选择“水滴”:

[![](../Images/d1981eeac9fc42c1175d663172b5aef2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M8CmsQpZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/content/images/downloaded_images/using-timescaledb-with-hasura-graphql-d05f030c4b10/1-g80WOqOvbxe8jIrc9tWk-A.png)

在下一个屏幕中，选择图像为“Ubuntu 16.04”:

[![](../Images/b9f42b3227724e696725022eab8ea815.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Lsbf7Fer--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/content/images/downloaded_images/using-timescaledb-with-hasura-graphql-d05f030c4b10/1-tqoxP1KNXzXTIJhpJQzcWw.png)

然后选择机器尺寸和您喜欢的地区。

接下来，确保添加一个 SSH 密钥(您可以在您的计算机上添加现有的 SSH 密钥)，以便我们可以登录到我们的服务器。最后，单击“创建”按钮。

## 安装对接器

获取你的 droplet 的公共 IP，然后 SSH 到服务器:

```
$ ssh -i <path-to-the-private-key> root@<public-ip-of-droplet> 
```

登录后，安装 Docker 和 Docker compose:

```
$ apt-get update
$ apt-get install docker.io docker-compose
# verify docker is working
$ docker info 
```

## 用 Hasura 运行 TimescaleDB

要独立运行 TimescaleDB，我们只需运行他们的 [docker 镜像](http://bit.ly/2okxVGg) :

```
$ docker run -d -p 5432:5432 timescale/timescaledb:latest-pg10 
```

这将提取`timescale/timescaledb` docker 映像并运行它，将主机的端口`5432`绑定到容器的端口`5432`。

但是让我们使用 docker compose 来一起运行 TimescaleDB 和 Hasura GraphQL。

将以下内容复制并粘贴到一个文件中，将其命名为`docker-compose.yaml`: