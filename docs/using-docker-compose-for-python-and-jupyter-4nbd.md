# 为 Python 和 Jupyter 使用 docker-compose

> 原文：<https://dev.to/rosejcday/using-docker-compose-for-python-and-jupyter-4nbd>

在我写完上一篇关于开始使用 Docker for Jupyter notebooks 和 Python 的文章后，我得到了一个学习`docker-compose`来替换 Docker 文件中冗长的 docker run 命令的建议。继续工作，我开始研究`docker-compose`,看看如何更好地改进我用 Docker 和英特尔 python 发行版构建的开发环境。

### 码头工-化合物

在研究`docker-compose`时，据说 compose 命令是 Docker 的一个工具，用于定义和运行多个容器应用程序，其中一个 compose 文件用于定义应用程序所需的服务。通过以这种方式设置应用程序，可以从一个命令启动所有服务。这样，可以通过四个简单的步骤创建应用程序:

1.  建立 Dockerfile 文件来定义应用程序环境。
2.  添加一个需求文件来下载 Python 包。
3.  创建 docker-compose.yml 文件来定义组成应用程序的服务。这些将在一个隔离的环境中一起运行。
4.  运行 docker-compose build 构建应用程序，运行 docker-compose up 启动并运行应用程序。

### 设置 Dockerfile 和需求

首先，让我们看看我上次开始的 [Dockerfile](https://dev.to/rosejcday/python-and-jupyter-notebooks-23h5) 。在这个 Dockerfile 文件中，我使用 miniconda 作为安装了 Intel Python2 的基础映像。如前所述，英特尔发行版在 Docker 中有 Python 2 和 Python 3 映像，具有核心或完整配置。核心配置包含具有依赖性的 NumPy/SciPy，而完整配置包含英特尔分发的所有内容。

因为我通常使用 Python2 来创建类，所以我选择了 Intel Python2 版本来安装在 miniconda 的顶端。这样，请记住将最终用户许可协议设置为“yes”作为环境变量。

```
# Set the base image using miniconda 
FROM continuumio/miniconda3:4.3.27

# Set environmental variable(s)
ENV ACCEPT_INTEL_PYTHON_EULA=yes 
```

Enter fullscreen mode Exit fullscreen mode

有了初始图像和环境变量，我将工作目录设置为`/home/notebooks`。正如你可能已经看到的，我在上一篇文章中使用了`/home/notebooks`作为挂载卷。通过一些研究，我意识到我也可以使用命令 WORKDIR 将它设置为工作目录，这样我就可以直接看到我的文件，而不是它们上面的几个文件夹。这只节省了我打开笔记本时的点击次数。

```
# Set working directory 
WORKDIR /home/notebooks 
```

Enter fullscreen mode Exit fullscreen mode

在建立工作目录之后，我通过添加一个需求文件来修改我的运行语句。这个文件被添加到容器的/app/目录中，然后使用 pip 调用。我发现这种方法是处理 Python 依赖项安装最常见的方法之一。这也使得 RUN 命令更短，因为它允许将任何 Python 依赖项放在一个单独的文件中。

```
# Add requirements file 
ADD requirements.txt /app/

# Installs, clean, and update 
RUN apt-get update \
    && apt-get clean \
    && apt-get update -qqq \
    && apt-get install -y -q g++ \ 
    && conda config --add channels intel \
    && conda install  -y -q intelpython2_full=2018.0.1 python=2 \ 
    && pip install --upgrade pip \ 
    && pip install -r /app/requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

这是我到目前为止为图片使用的小 Python 依赖列表。

```
mapboxgl
seaborn
timestring 
```

Enter fullscreen mode Exit fullscreen mode

最后，在建立需求文件之后，我添加了一行 CMD。CMD 命令默认执行容器的指令，除非指定了另一个命令。了解了这一点，默认命令是运行 jupyter notebook。

```
# Run shell command for notebook on start 
CMD jupyter notebook --port=8888 --no-browser --ip=0.0.0.0 --allow-root 
```

Enter fullscreen mode Exit fullscreen mode

### 定义服务

改编 docker 文件后，下一步是在 YAML 文件中定义服务。该文件的目的是用 Docker 设置多个服务，每个服务都在它们自己的容器中，该容器与您已经在 Docker 文件中指定的环境一起使用。因此，YAML 文件将用于配置这些服务，稍后，可以使用一个命令创建和启动这些服务。

这样，我创建的 YAML 文件目前是一个单一的服务，允许为英特尔 Python2 设置 Jupyter 笔记本。这个容器被命名为 python_notebook，使用端口 8888。用这个容器设置的最后一件事是在`/home/notebooks`安装我所有笔记本的一个卷。

```
version: '3'
services:
  notebook:
    container_name: python_notebook
    labels:
      description: Intel Python 2 using Jupyter Notebooks
      name: jupyter notebook 
    ports:
      - "8888:8888"
    volumes:
      - ~/Documents/notebooks:/home/notebooks
    build: . 
```

Enter fullscreen mode Exit fullscreen mode

稍后，可以修改这个文件来添加数据库服务、其他数据分析工具等等。

### 构建并运行 docker-compose

在更新 docker 文件、添加 requirements.txt 文件并为服务创建 YAML 文件之后，我们准备再次运行我们的 Intel2 容器。使用 docker-compose 时运行的第一个命令是构建容器。如下所示，该命令允许在需要时更新任何服务，并将构建容器。每当更新服务时都应运行此命令，以便在使用前可以重新构建服务。注意:如果是第一次运行，这可能需要几分钟时间。

```
docker-compose build 
```

Enter fullscreen mode Exit fullscreen mode

构建完所有服务后，就可以启动容器了。如下所示的命令将启动您为此项目定义的所有服务。对于这个特定的示例，该命令将运行上面指定的 python_notebook。

```
docker-compose up 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中运行这个命令后，您将会收到一个 URL 来打开 Jupyter notebook。复制并粘贴到浏览器中打开它。当您完成应用程序时，可以使用下面的命令来关闭服务。

```
docker-compose down 
```

Enter fullscreen mode Exit fullscreen mode

这要么需要在另一个终端窗口中运行，要么你可以在当前终端中按`Control C`退出。

### 参考文献

[英特尔针对 Python 发布的英特尔优化包](https://software.intel.com/en-us/articles/intel-optimized-packages-for-the-intel-distribution-for-python)
[Docker](https://www.docker.com/)
[Docker 撰写版本 3](https://docs.docker.com/compose/compose-file/)
[Docker 云栈 YAML](https://docs.docker.com/docker-cloud/apps/stack-yaml-reference/)
封面图片来源于 [Docker 壁纸](http://crosbymichael.com/docker-wallpapers.html)