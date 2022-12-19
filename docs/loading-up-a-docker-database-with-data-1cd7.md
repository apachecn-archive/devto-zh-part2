# 用数据加载 Docker 数据库

> 原文：<https://dev.to/mfurmaniuk/loading-up-a-docker-database-with-data-1cd7>

我们都需要数据。

尤其是在本地环境中运行代码时，生产环境中的代码将在后端数据库中运行。当我构建一些本地 Dockerized 应用程序(Tomcat 和 MySQL)时，我想让我的 UI 显示一些数据。

这在一定程度上是为了知道事情已经启动并运行，因为我们的主页调用数据库在初始页面上显示一些信息。驻留在后端的数据，在本例中是 MySQL 容器。我考虑过如何添加启动脚本，或者设置一个已经加载的 MySQL 数据库，但是后来我发现了 Docker 的 entrypoint initdb.d，这很容易就解决了我的问题。

> 复制部署/docker/MySQL/very-small-db . SQL/docker-entry point-initdb . d

这需要我的数据库转储，我把它做得很小，所以它只需要我的模式和可能几天的数据，并在我启动容器时用它初始化我的数据库。这样我就可以随心所欲地摆弄数据集，重新构建容器，然后就可以上路了。

使用工具的最大好处是当你突然发现一个新的特性，或者使用它们的方法。