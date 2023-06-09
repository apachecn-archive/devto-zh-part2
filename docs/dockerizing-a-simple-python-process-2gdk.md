# 将一个简单的 Python 过程归档

> 原文：<https://dev.to/thejessleigh/dockerizing-a-simple-python-process-2gdk>

这是将一个简单的 Python 项目从本地脚本投入生产的系列文章的第二部分。在第一部分中，我谈到了我在将一个旧项目从 Python 2 转换到 Python 3 时遇到的一个问题。

这一部分将介绍我如何将 Python 进程、其输入和输出放入 Docker 容器，并在 [Dockerhub](https://dockerhub.com) 上公开一个图像。

*要求我不在这里赘述。去 Docker.com，按照那里的指示*

*   下载 docker
*   创建码头工人 id
*   在 Dockerhub 上使用您的 docker id 登录

# Docker 是什么？

[Docker](https://docker.com) 是一个集装化平台。容器化是一种将代码单元和它们的依赖项打包在一起的方法，这样它们就拥有了独立运行所需的一切。

使用 Docker 可以帮助解决“它在我的机器上工作”的问题，编写 Docker 化的代码是鼓励深思熟虑的代码实践的一个好方法。Docker 容器应该是简单的，负责尽可能少的东西，并且尽可能少依赖外部。

## Docker 图像 vs docker 容器

在这篇文章中，以及在网上，你会看到术语`container`和`image`。一个`image`基本上是当你使用`docker build`命令时创建的你的 dockerized 代码的快照——下面会详细介绍。当你在 Docker 图像上使用`docker run`时，它会启动一个容器。所以一个`container`是一个`image`的运行实例。

# 解剖一个 Dockerfile 文件

我决定将我的 csv writer 从本系列的前一篇文章[中分类，这样我就可以很容易地在不同环境之间移动它。](https://dev.to/thejessleigh/converting-a-csv-writer-from-python-2-to-python-3-1cnb)

为此，我需要一个 docker 文件。Dockerfile 是没有文件扩展名的文本文件。

下面是我的 Python 代码的 docker 文件的样子:

```
FROM python:3.7
ARG export_file=goodreads.csv
COPY $export_file goodreads_export.csv
COPY converter.py /
CMD ["python", "./converter.py"] 
```

## 从

这里的 FROM 关键字表示依赖关系。Docker 容器没有自动加载语言。为了访问 Python 来运行代码，我们需要指示图像包含`python:3.7`。

> 关于 Docker 注册表的说明:
> 默认的 Docker 注册表是 [Dockerhub](https://dockerhub.com) 。如果 docker 图像在 Dockerhub 上可用，则在从 docker repo 拉或推时，不需要指定 url。你只需要作者的用户名和回购名称。例如，您可以使用命令`docker pull thejessleigh/goodreads-libib-converter`从这篇文章中提取 docker 图像。如果你使用不同的注册表，你需要告诉 Docker 去哪里。例如，如果你使用 Quay，你会做`docker pull quay.io/example-username/test-docker-repo`。
> 
> 我的 Dockerfile 中的 python 依赖项没有用户名，因为它是托管在 Dockerhub 上的官方 repo。

## 生气了

`ARG`声明一个论点。这是 docker 文件中唯一可以放在`FROM`前面的指令，尽管为了一致起见，我更喜欢把`FROM`放在前面。

在上面的例子中，我声明了一个`ARG` `export_file`并给它一个默认值。它期望一个名为`goodreads.csv`的文件与 Dockerfile 在同一个目录中。如果我想传入不同的东西，我会在构建图像时用`--build-arg=export_file=my_goodreads_export.csv`指示它使用不同的文件名。

## 复制

`COPY`和`ADD`将文件内容复制到 docker 映像中。这是我导入输入文件和 Docker 映像执行的实际 Python 代码的地方。

`COPY`需要两个参数:

*   您要放入图像的文件的位置
*   文件*在*docker 图像中的位置

因此，无论我将哪个文件作为 CSV 文件包含在 Docker 容器中，都将被称为`goodreads_export.csv`。这很棒，因为这意味着无论我用什么构建 docker 映像，文件名总是一致的。我不必担心让 Python 代码处理不同的文件名或路径。它总能找到`./goodreads_export.csv`。

`COPY`和`ADD`有一些微妙的区别， [@ryanwhocodes](https://dev.to/ryanwhocodes) 已经写过了，我就把他的帖子[留在这里](https://web.archive.org/web/20181023142837/https://dev.to/ryanwhocodes/dockerfile-copy-vs-add-key-differences-and-best-practices-4191)。

> 更新于 2019 年 9 月:这篇文章似乎在 dev.to 上不再可用，所以我用 archive.org 链接替换了嵌入的文章。

## 运行

`RUN`发出指令，作为映像的一部分执行和提交。如果我正在对接一个需要安装外部包的 Python 项目，我可以使用`RUN`来`pip install`那些依赖项。然而，`converter.py`是一个非常简单的过程，不需要外部包，所以我不需要在构建过程中运行任何东西。

## CMD

每个 docker 文件只能有一条`CMD`指令。如果 Dockerfile 包含多个`CMD`，那么只有最后一个会执行。

`CMD`是当您将图像实例作为容器运行时，您希望图像执行的命令。它不是作为映像构建过程的一部分执行的。`CMD`在这方面与`RUN`不同。

# 建立码头工人形象

现在，我们拥有了从 Docker 文件为 Python 代码构建 Docker 映像所需的一切。

如上所述，Docker `image`是准备执行命令或程序的环境的惰性快照，但是*还没有*执行该命令。

为了使用上面的 Dockerfile 文件进行构建，我们运行

```
docker build --build-arg=export_file=goodreads_export.csv -t goodreads-libib-converter . 
```

`--build-arg`告诉 Docker 用名为`goodreads_export.csv`的文件构建图像，覆盖默认的期望`goodreads.csv`。

`-t goodreads-libib-converter`“标记”图像为`goodreads-libib-converter`。这就是你如何用一个人类可读的`REPOSITORY`名创建你的容器。

告诉 Docker 在当前目录中查找要构建的 Docker 文件。

完成此操作后，通过检查我的图像列表，我可以看到图像已成功创建。

```
> docker image list
REPOSITORY                 TAG       IMAGE ID       CREATED             SIZE
goodreads-libib-converter  latest    1234567890     12 seconds ago      924MB 
```

# 运行 Docker 容器

现在我有了一个`image`，我有了一个能够运行我的程序的独立环境，但是它实际上还没有执行用`CMD`指定的核心过程。我是这样做的:

```
docker run goodreads-libib-container 
```

我看到我的`converter.py`文件中的打印调试语句执行，所以我知道有多少 CSV 行被转换。当我在本地运行程序时，它创建了一个名为`libib_export.csv`的输出文件。然而，当我现在检查我的目录的内容时，它不在那里。那有什么用！？

## 存取写出的文件

我不再在以前的目录中运行 Python 代码。我正在 Docker 容器中运行它。因此，任何被写出的文件也将被存储在 Docker 容器中的*中。输出文件在那里对我没有多大用处！*

我在本地运行 Docker 容器，所以我所要做的就是找到容器，并将输出文件从它的 dockered 位置复制到我真正想要的位置。

```
docker cp container_id:/libib_export.csv ~/outputs/libib_export.csv 
```

这将从`converter.py`中提取结果 CSV 输出，并将其放在我可以访问的地方。

我可以用
算出`container_id`(或者人类可读的名字)

```
> docker ps -a
CONTAINER ID  IMAGE                   COMMAND                  CREATED             NAMES
e00000000000  goodreads-libib-export  "python ./converter.…"   24 seconds ago      naughty_mcclintock 
```

> 是的，naughty_mcclintock 实际上是我在本地使用的容器的过程生成的名称。

从一个容器中复制一个文件到我想要的位置对于本地环境来说是很好的，但是如果我想把这个项目投入生产的话，它的用途就有限了。对于处理 Docker 容器的输出文件，还有其他更好的选择，但是我们将在本系列的另一篇文章✨中讨论✨

# 提交一个 docker 图像

在我们运行了容器以确认它工作之后，我们可能要根据它执行时所做的更改来创建一个新的映像。我们正在准备想要上传到外部 Docker 注册中心的图像，比如 Dockerhub。

提交 Docker 映像时，我们需要指定注册表(如果它不是 dockerhub)、作者名、存储库名和标记名。

```
docker commit -m "Working Python 3 image" naughty_mcclintock thejessleigh/goodreads-libib-converter:python3 
```

我的`docker commit`成功了，所以我在终端中看到了 sha256 散列输出。当然，创建提交消息是可选的。但是我喜欢这样做来保持有条理。

> 关于 Docker 图像标签的注意:
> 当你拉一个 Docker 图像并且你没有指定标签时，它将使用默认标签(通常是`latest`)。标签是您可以跟踪项目中的更改而不覆盖以前版本的方法。例如，如果您(出于某种原因)仍在使用 Python 2，您可以通过运行`docker pull thejessleigh/goodreads-libib-converter:python2`来访问 Python 2 映像。现在我的摇杆回购上的`:python3`和`latest`标签是一样的，但你可以拉任何一个。

# 将 docker 图片推送到 Dockerhub

现在，我有了一个想要向世界发布的图像，我可以把它推送到 Dockerhub。

首先，我需要登录 Dockerhub 并创建一个存储库。存储库需要一个名字，并且应该有一个详细描述项目目的的简短描述，以及一个解释依赖性、需求、构建参数等的详细描述。您还可以将 Docker 存储库设为私有。

完成之后，我运行`docker push`，它将我指定的项目和标签的最新提交发送到外部注册表。如果您没有指定标签，这个推送将覆盖您的存储库中的`latest`标签。

```
docker push thejessleigh/goodreads-libib-converter:python3 
```

如果你去[我的 Dockerhub profile](https://hub.docker.com/u/thejessleigh) ，你可以看到`goodreads-libib-converter`项目，并拉 Python 2 和 Python 3 的两个版本。

# 下一步

现在我有了一个工作 Docker 映像，我想把它投入生产，这样任何人都可以把他们的 Goodreads 库 CSV 转换成 Libib 库 CSV。我将使用 AWS 来做这件事，这需要一点设置。

本系列的下一部分将介绍如何设置 AWS IAM 帐户，设置`awscli`并配置您的本地概要文件，以及创建一个您的 IAM 帐户可以访问的 s3 bucket。