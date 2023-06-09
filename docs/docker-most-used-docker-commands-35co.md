# Docker:最常用的 Docker 命令

> 原文：<https://dev.to/flaviabastos/docker-most-used-docker-commands-35co>

Docker 已经存在几年了，但是它听起来太复杂了，我从来不知道它到底在解决什么问题。直到最近我才了解到它，并开始在工作和个人项目中使用 Docker。

我开始写一个帖子解释一些关于 Docker 的基本东西，但结果是一个又长又无聊的帖子。因此，相反，这里有更多的快速获取信息，我希望我当时就有这些信息(以及一些用于进一步了解信息的资源)。

**Docker 是什么？**

Docker 是一个平台，它允许你在一个容器中隔离你的运行环境。例如，您可以在容器内运行应用程序及其所有依赖项，而不必担心依赖项版本冲突。

**什么是容器？**

容器是图像的“运行”实例。

**什么是图像？**

映像只不过是一组描述容器启动并运行时应该是什么样子的步骤(操作系统、应用程序、卷、端口等)。这个描述放在 other 文件中，用作构建映像的基础(换句话说，实现步骤并准备容器)。所以…也许我们可以说一幅图像是一个“实现的”或“执行的”描述。

**流程**

1.  只做一次:[在本地安装 Docker](https://docs.docker.com/install/#supported-platforms)

2.  获取一个映像:要么从注册表(比如 [Docker Hub](https://hub.docker.com/explore/) )复制(“拉”)一个，要么自己写一个。如果你选择自己写，你必须创建一个“Dockerfile”(描述步骤)，你还必须“构建”那个图像。

3.  运行图像:这将是一个容器。

就是这样。

**基本 Docker 命令:**

从 Docker 的注册表中提取(复制)一个图像( [Docker Hub](https://hub.docker.com/explore/) ):

```
docker pull image-name 
```

构建标记为“图像标签”的图像:

```
docker build --tag image-tag --file path-to-your-Dockerfile 
```

列出所有本地图像:

```
docker images 
```

运行一个图像来创建一个容器(这是这个命令最简单的版本，我几乎从来没有这样使用过):

```
docker run image-tag 
```

其中: *image-tag* 是我创建的图像

在后台运行映像(“分离”)，将主机端口 80 映射到容器端口 8080:

```
docker run -d -p 80:8080 image-tag 
```

列出所有正在运行的容器:

```
docker ps 
```

列出所有容器(包括停止的):

```
docker ps -a 
```

Exec 进入容器(也就是通过命令行访问容器):

```
docker exec -it image-name bash 
```

注意: *bash* 是将在容器中创建 bash 会话的命令；你可能需要一些其他的东西，但这将适用于大多数情况

停止容器:

```
docker stop image-name 
```

移除容器:

```
docker rm image-name 
```

**注意:在上面的大多数命令中，也可以使用“容器 id”来代替“图像名称”(除了构建和运行，因为在这些情况下，*容器 id* 只会在容器启动时创建)。例如，如果您有一个很长的图像名，这可能会很方便，因此使用*容器 id* 的前几个或三个字母就足以运行 docker 命令。

**Docker compose 怎么样？**

Docker compose 将多个图像合并到一个简洁的包中，因此您可以运行这个包来立即调出 docker compose 文件中列出的所有图像，而不是手动运行每个单独的图像。每个图像都有自己的容器。

对我来说，一个非常常见的用途是拥有一个 docker compose 文件，其中包含一个应用程序(django，rails，.NET)和一个用于数据库的图像。一旦你运行这个“包”,两个容器都会启动，你可以通过 docker exec 分别访问它们。

要使用 docker compose，您必须具备:

1.  您想要组合在一起的应用程序的图像
2.  一个 docker compose 文件将描述这些图像如何交互——在我上面描述的场景中非常常见，例如，确保只在数据库容器运行之后*启动应用程序容器。*

然后，从 docker compose 文件所在的目录中，您可以使用:
运行它

```
docker-compose up -d 
```

**注意: *-d* 在后台运行(分离模式)，可选

停止所有容器:

```
docker-compose down 
```

这是很多信息，但它描述了一些简单的用例，可以让你开始使用 Docker。

资源:

*   [初学者 Docker](https://docker-curriculum.com)
*   [Docker 备忘单](https://dockercheatsheet.painlessdocker.com/)
*   [Python 官方图片](https://hub.docker.com/_/python/)
*   [节点官方图片](https://hub.docker.com/_/node/)
*   [Debian 官方图片](https://hub.docker.com/_/debian/)

> 帖子 [Docker:最常用的 Docker 命令](https://wp.me/pa0b0y-U)最初发表在 [flaviabastos.ca](https://flaviabastos.ca/) 上