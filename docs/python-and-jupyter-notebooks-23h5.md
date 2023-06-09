# Python 和 Jupyter 笔记本

> 原文：<https://dev.to/rosejcday/python-and-jupyter-notebooks-23h5>

最近我开始在 Python 上使用 Jupyter 笔记本，但是一直在纠结是否需要下载依赖项或者下载不正确。看到这是一个持续的趋势，并希望开发环境的计算机之间的可移植性，我开始学习 Docker 是如何工作的。

在 2.8 GHz 英特尔酷睿 i7 处理器上工作时，我开始研究在这台计算机上设置 Docker 环境的不同方法，以及以后我想切换到的任何其他计算机。我发现了两种使用 Jupter 笔记本在 Docker 中设置英特尔 Python 的方法。在设置 Python 的英特尔发行版时，我使用 Jupyter 笔记本作为代码、方程和可视化的前端。这是我目前在课堂上使用的，当需要在团队成员之间共享代码时，我发现它非常好用。

如前所述，要设置这一点，我想使用 Docker，它允许将笔记本容器化，以便打包和运行应用程序。通过使用 Docker，这允许在一个容易转移的环境中编码。当使用 Docker 为 Python 发行版设置 Jupyter 笔记本时，可以使用已经准备好的图像，或者在定制自己的图像时使用图像作为基础。下面我来看看在 Jupyter 笔记本上为英特尔 Python 设置 Docker 映像的两种方法。

### Docker 图像

英特尔发行版在 Docker 中有 Python 2 和 Python 3 映像，具有核心或完整配置。核心配置包含具有依赖性的 NumPy/SciPy，而完整配置包含英特尔分发的所有内容。出于我的目的，我使用了完整版的英特尔 Python 2。

为了开始在 Jupyter 笔记本上使用 Docker 映像，我从 Docker Hub 下载了我想要的映像，并设置了一个卷来使用该映像。使用 Docker 容器时，卷是一个可选的附加项，但它允许持久数据。在这个实例中，我使用了一个卷，因为它是我存储所有想要运行的笔记本的地方。当容器不再运行时，数据不再持久，如果数据只存在于容器中，当另一个进程需要它时，就很难取出来。因此，我创建了一个在主机上使用的卷，供以后与容器一起使用。为了设置这个 Docker 容器，我遵循了以下步骤:

1.  从 [Docker Hub](https://hub.docker.com/r/intelpython/intelpython3_core/) 下载 Docker 镜像。
2.  设置一个文件夹作为 Docker 的卷，`~/Documents/notebooks`被设置在计算机上并附加到 Jupyter 笔记本容器中的`/home/notebooks`。这使得在关闭笔记本电脑后，可以方便地访问文件和控制版本。
3.  打开终端，运行笔记本。

```
# Pull image 
docker pull intelpython/intelpython2_full

# Set up folder 
mkdir ~/Documents/notebooks/ 

# Run the notebook 
docker run -v ~/Documents/notebooks:/home/notebooks -p 8888:8888 intelpython/intelpython2_full jupyter notebook --ip='*' --port=8888 --allow-root --no-browser 
```

Enter fullscreen mode Exit fullscreen mode

这可能适用于许多应用程序，但这是我遇到问题的地方。当我在 Jupyter notebook 中运行代码时，有一个对 seaborn 的调用，它在 Python 中用于基于 matplotlib 的可视化。这个库用于在 Python 中创建更有吸引力的统计图形。从 Docker Hub 使用完整的英特尔 Python 映像并不能提供所需的库。有了这个，我开始使用 Docker 文件定制 Docker 映像，并将其添加到 seaborn 中。

### Dockerfile 进行定制

为了创建可在 Jupyter 笔记本电脑上运行的基于英特尔 Python 的定制 Docker 映像，我基于英特尔 Python 的 Docker Hub Dockerfile 文件创建了一个 Docker 文件。这样，continuumio/miniconda 被用作工作的基础图像。这是因为 Anaconda 是一个由 Python 支持的平台，它包含了 Python 和 r 的最流行的数据科学包。通过使用此映像，在创建定制映像时，可以使用 conda 安装所有不包含在英特尔 Python 中的软件包。

```
# Set the base image using miniconda 
FROM continuumio/miniconda3:4.3.27

# Add metadata
LABEL version="1.0" \
      description="Intel Python 2 using Jupyter Notebooks" \
      date_created="01march2018" \
      date_modified="28march2018" 
```

Enter fullscreen mode Exit fullscreen mode

这样，使用命令 ENV 将环境变量`ACCEPT_INTEL_PYTHON_EULA`设置为“是”。这是对英特尔 Python 最终用户许可协议(EULA)的接受，每次创建新环境时都需要接受该协议。设置此变量后，RUN 命令可用于在新层中执行 shell 命令。每次执行该命令时，都会创建一个新层。使用该命令，conda 可用于安装英特尔 Python、seaborn 和您可能需要或想要的任何其他数据科学库。然后用 apt-get 更新然后安装 g++。配置自定义映像后，现在可以构建并运行它以供使用。

```
# Set environmental variable(s)
ENV ACCEPT_INTEL_PYTHON_EULA=yes

# Installs, clean, and update 
RUN conda config --add channels intel\
    && conda install  -y -q intelpython2_full=2018.0.1 python=2 \
    && conda install seaborn \ 
    && apt-get clean \
    && apt-get update -qqq \
    && apt-get install -y -q g++ 
```

Enter fullscreen mode Exit fullscreen mode

#### 建立形象

完成 docker 文件后，在运行命令之前，请检查您是否位于命令行的正确位置。我经常发现自己在错误的目录中，当我先去看别的东西，然后再回来构建一个图像。

```
$ ls Dockerfile 
```

Enter fullscreen mode Exit fullscreen mode

然后，为了构建映像，运行带有标记-t 的构建命令。这个标签为图像提供了一个易于使用的名称，我调用了 mine test_intel 来快速地从列表中选择它。这可能需要几分钟来构建映像。

```
docker build -t test_intel . 
```

Enter fullscreen mode Exit fullscreen mode

#### 运行图像

构建映像后，您可以检查本地机器上的 Dockers image registry 来查看列表中的映像。运行该命令时，会出现一个列表，显示存储库名称、标记、映像 ID、创建时间和映像大小，如下例所示。这是一个很好的检查，以确保图像在前进之前建立。

```
docker image ls REPOSITORY        TAG      IMAGE ID        SIZE
test_intel        latest   ce5d8aa2966d    6.52GB 
```

Enter fullscreen mode Exit fullscreen mode

一旦完成，就该运行映像了。运行映像的工作方式类似于第一个例子，即在没有定制的情况下设置核心或完整的 Docker 映像。要运行此命令，请将映像名称替换为您在前面的步骤中刚刚创建的新映像 test_intel。

```
docker run -v ~/Documents/notebooks:/home/notebooks -p 8888:8888 test_intel jupyter notebook --ip='*' --port=8888 --allow-root --no-browser 
```

Enter fullscreen mode Exit fullscreen mode

在终端中运行该命令后，应该会出现一个 URL，供您复制并粘贴到浏览器中，以连接到 Jupyter notebook，该笔记本已安装了英特尔 Python 发行版，并已准备就绪。一旦连接上，您就可以开始使用您定制的环境。要关闭服务器和所有内核，请在终端中使用`Control-C`。

### 参考文献

[英特尔为 Python](https://software.intel.com/en-us/articles/intel-optimized-packages-for-the-intel-distribution-for-python)
[Docker](https://www.docker.com/)
[seaborn](https://seaborn.pydata.org/installing.html)
[mini conda](https://hub.docker.com/r/continuumio/miniconda/)
封面图片来源于 [Docker 壁纸](http://crosbymichael.com/docker-wallpapers.html)