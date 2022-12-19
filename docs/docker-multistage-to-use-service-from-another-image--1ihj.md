# docker 多级，使用来自另一个映像的服务

> 原文：<https://dev.to/taragrg6/docker-multistage-to-use-service-from-another-image--1ihj>

不过，在 docker 世界中，docker 多级听起来是一件很复杂的事情。它不是，也不是必须要实施的事情。事情可以做，即使没有它。今天，我将尝试展示如何将一个 docker 映像中运行的服务用于另一个 docker 映像。简而言之在单个 ***Dockerfile*** 中使用多个映像或者使用来自已经构建的映像的服务。

出于演示目的，我将执行以下任务:

*   用 ubuntu 创建一个 docker 镜像并在上面安装 docker。
*   然后创建另一个映像，并使用安装在第一个映像中的 docker。

**步骤 1 让我们创建我们的第一个 docker 图像:**
请将其保存为 Dockerfile 并构建它。如果你愿意，你也可以看看我的视频短片。

```
From ubuntu
RUN apt-get update && apt-get install -y docker.io 
```

让我们建造它

`docker build -t firstimage .`

这将创建一个 docker 映像，以 ubuntu 作为基础映像并安装 docker。现在，让我们跳到步骤 2。

**步骤 2:从已经构建的映像中使用 docker 服务:**

这次我也会保持简单。假设，我们需要一个 docker 镜像，它需要 ubuntu 作为基础镜像并安装 docker。在这里，我们有一个选项，例如我们可以简单地复制我们在步骤 1 中所做的，并构建我们的映像，或者选择多阶段构建，在这种情况下，我们将利用另一个构建的映像中已经可用的服务。

```
1\. from firstimage as dockerimage

#this is the new image which will utilize docker without installing
#because comes from another already build image above

2\. from ubuntu
3\. RUN apt-get update && apt-get install -y libltdl7
4\. COPY --from=dockerimage /usr/bin/docker /usr/bin/docker 
```

你可以清楚地看到。我们从命令这里得到两个行号(1，2)。第一个获取我们最近构建的图像，并命名为 dockerimage。第二个是我们运行其他命令的新图像。在第(4)行，我们将 docker 从第一个映像复制到我们将要构建的新映像。步骤(3)不是强制性的。我的容器出了问题，所以不得不这么做。

就是这样，这就是我们如何将一个映像中的可用服务复制到另一个映像中。让我们建立第二个图像。

`docker build -t secondimage .`

**让我们测试一下:**
让我们确保我们的第二个图像可以成功地利用 docker 命令。因为我们只是复制而不是安装在它上面。

要做到这一点，有多种选择。使用 docker 文件中的 docker - version 之类的 docker 命令添加 RUN 命令，或者使用 following 命令进入 running docker 容器并运行 docker 命令。

`docker run -it secondimage bash`

它将引导您进入正在运行的容器，只需输入 ***docker*** 就可以知道它是否成功获取了我们从 firstimage 复制的 docker

**链接到短视频演示。如果你更相信视频**

[https://www.youtube.com/watch?v=glOdtHuuj94&feature = youtu . be](https://www.youtube.com/watch?v=glOdtHuuj94&feature=youtu.be)

我知道，我的写作是不连贯的，所以请帮助我指出我的错误，这将提高和激励我每次都写更好的文章。