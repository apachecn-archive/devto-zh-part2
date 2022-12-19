# 如何在微软 Azure 上创建低成本的 Apache Spark 集群

> 原文：<https://dev.to/saschadittmann/how-to-create-a-low-cost-apache-spark-cluster-on-microsoft-azure-5bia>

几个月前，我在 GitHub 上发现了一个很好的开源工具 AZTK，它提供了一种在 Microsoft Azure 上提供低成本 Apache Spark 集群的快速简单的方法。

在这篇博客文章中，我将向您展示如何在基于 Windows、Linux 或 MacOS 的系统上安装 **Azure 分布式数据工程工具包(AZTK)** ，以及如何使用它配置您的第一个 Apache Spark 集群。

## Azure 分布式数据工程工具包(AZTK)

**Azure 分布式数据工程工具包(AZTK)** 是一个 python CLI 应用程序，用于在 Azure 中的 Docker 集群上提供按需 Spark。这是一种启动和运行 Spark 集群的廉价而简单的方法，对于想要进行大规模试验和测试的 Spark 用户来说，这是一个很好的工具。

这个工具包建立在 Azure Batch 之上，但不需要任何 Azure Batch 知识就可以使用。

更多详情请看[ [GitHub](https://github.com/Azure/aztk) 。

### 显著的特征

*   平均 5 分钟的火花簇供应时间
*   火花簇在 Docker 容器中运行
*   在支持 GPU 的集群上运行 Spark
*   用户可以自带 Docker 图像
*   能够以 80%的折扣使用低优先级虚拟机
*   使用低优先级和专用虚拟机的混合模式集群
*   内置对 Azure Blob 存储和 Azure 数据湖连接的支持
*   定制的 PySpark、Jupyter 和 Anaconda 体验
*   SparklyR、RStudio-Server 和 Tidyverse 的定制体验
*   能够直接从本地机器的 CLI 运行 spark submit

## 入门

### 安装 Python 3

在安装 Azure 分布式数据工程工具包之前，您需要在系统上安装 Python 3 和 pip3。

为此，请看看[Python.org](https://www.python.org)。

### 虚拟环境(可选)

之后，我建议为工具包创建一个单独的虚拟环境。

```
python3 -m venv aztk 
```

一旦你创建了一个虚拟环境，你可以激活它。

在 Windows 上，运行:

```
aztk\Scripts\activate.bat 
```

在 Unix 或 MacOS 上，运行:

```
source activate aztk 
```

### 安装 AZTK

现在您已经准备好安装 Azure 分布式数据工程工具包(AZTK)了，只需简单的:

```
pip install aztk 
```

### 初始化 AZTK

安装完工具包后，就可以创建第一个 Aztk 环境了。为此，您只需调用

```
aztk spark init 
```

该命令在当前目录中创建一个. aztk 文件夹，其文件结构如下:

*   cluster.yaml
*   核心网站. xml
*   震动
    *   。空
*   job.yaml
*   秘密，yaml
*   spark-默认值. conf
*   spark-env.sh
*   嘘.亚 ml

如果您想创建一个机器范围的配置，您可以将**–全局**参数添加到命令中。

### Azure 资源和凭证

为了能够使用工具包，你必须提供一些 Azure 资源，例如 Azure 批处理、Azure 存储帐户、服务主体等。

最简单的方法是登录到 [Azure 门户](https://portal.azure.com/)并在 [Azure 云外壳](https://shell.azure.com/)中执行以下命令。

```
wget -q https://raw.githubusercontent.com/Azure/aztk/v0.7.0/account_setup.sh -O account_setup.sh &&
chmod 755 account_setup.sh &&
/bin/bash account_setup.sh 
```

回答几个问题后，该命令返回所需的设置，您可以在**中添加/更新这些设置。aztk/secrets.yaml** 文件。

<figure>[![secrets.yaml Settings](../Images/5092d5e14598f8f8816f2cf652c8caa4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a4kSTvCT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://datainsights.cloud/images/posts/AztkGettingStarted01.png) 

<figcaption>将 Azure 凭证添加到 secrets.yaml 文件</figcaption>

</figure>

## 配置您的第一个 Apache Spark 集群

最后，我们准备使用 AZTK 提供我们的第一个 Apache Spark 集群。

```
aztk spark cluster create --id mycluster --size 0 --size-low-priority 5 --vm-size standard_d12_v2 
```

*   使用 **id** 参数，您可以为您的集群指定一个唯一的 id(在您的 Azure 批处理帐户中)。
*   **size** 参数指定专用虚拟机的数量(按全价收费)。
*   **size-low-priority** 参数指定低优先级虚拟机的数量(按正常价格的 20%收费)。这当然有一个缺点。如果 Azure 需要其他客户的虚拟机，它们将被删除。
*   **vm-size** 参数指定要使用的虚拟机的[类型。](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/sizes)

<figure>[![Provision Apache Spark Cluster](../Images/87050c6f32becd2d09247bebf27e6fc3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--53oDrz_Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://datainsights.cloud/images/posts/AztkGettingStarted02.png) 

<figcaption>置备你的第一个阿帕奇星火集群</figcaption>

</figure>

> 您还可以使用 Azure N 系列虚拟机来配置支持 GPU 的集群。

## 获取集群信息

一旦集群被配置、存在或删除，您可以使用以下命令获取更多详细信息:

```
aztk spark cluster list 
```

<figure>[![List Apache Spark Clusters](../Images/15c204a1eab239ae7a195754cf61d4e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w2ibdecv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://datainsights.cloud/images/posts/AztkGettingStarted03.png) 

<figcaption>列出所有阿帕奇星火星团</figcaption>

</figure>

```
aztk spark cluster get --id mycluster 
```

<figure>[![Get Cluster Details](../Images/d8bc600bf087e845ba44936bc7637636.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Jvw1Jfgg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://datainsights.cloud/images/posts/AztkGettingStarted04.png) 

<figcaption>获取单个星团的详细信息</figcaption>

</figure>

## 连接到集群

使用下面的命令，您可以 ssh 连接到集群的主节点，并向集群上的服务(和插件)进行端口转发。

```
aztk spark cluster ssh --id mycluster 
```

<figure>[![Get Cluster Details](../Images/ce5a7b9a7ff696aef68413abae13fb50.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jkxIwOhR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://datainsights.cloud/images/posts/AztkGettingStarted05.png) 

<figcaption>获取单个星团的详细信息</figcaption>

</figure>

建立连接后，您可以使用端口转发来访问 Spark Web UI 等服务。

<figure>[![Apache Spark Web UI](../Images/0cf91a7bb08f50d828cd3c523217733f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wK3e46SX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://datainsights.cloud/images/posts/AztkGettingStarted06.png) 

<figcaption>通过端口转发访问 Spark Web UI</figcaption>

</figure>

## 删除集群

最后但同样重要的是，如果不再需要集群，不要忘记删除它。

```
aztk spark cluster delete --id mycluster 
```

## 演示

为了观看如何设置 AZTK 和配置您的第一个 Spark 集群的演示，我制作了一个简短的视频:

[https://www.youtube.com/embed/Kr62gDdRMyQ](https://www.youtube.com/embed/Kr62gDdRMyQ)