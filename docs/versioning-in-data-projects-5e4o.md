# 数据项目中的版本控制

> 原文：<https://dev.to/diogoaurelio/versioning-in-data-projects-5e4o>

可再现性是科学的一个支柱，通过 git 进行版本控制对它来说是一个福音。对于纯软件工程来说，它工作得非常完美。然而，机器学习项目不仅与代码有关，还与数据有关。用两个不同的数据集训练的同一个模型可以产生完全不同的结果。

因此，当我在数据团队的 git repos 上偶然发现 csv 文件时，这并不奇怪，因为他们很难跟踪代码和元数据。然而，对于今天的庞大数据集来说，这是不可能的。我见过几个解决这个问题的方法，没有一个是防弹的。这篇文章不是关于这些黑客，而是关于它的开源解决方案: [DVC](https://dvc.org/) 。

[![dvc_flow_large](../Images/49fa3612e31140d31ab57c9456d82a67.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SibN27D1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://datacenternotes.files.wordpress.com/2018/09/dvc_flow_large.png)

让我们用一个 kaggle 挑战来举例:[用高级回归技术预测房价](https://www.kaggle.com/c/house-prices-advanced-regression-techniques)

现在克隆回购:

```
git clone git@github.com:diogoaurelio/kaggle-house-prices.git
cd kaggle-house-prices 
```

在这篇文章中，我们需要几个 python 包——比如 dvc、boto3 和机器学习相关的包——所以我们将举例说明如何配置本地 anaconda 并安装本教程所需的所有依赖项:

```
conda install -n kaggle python=3.6 -y
source activate kaggle
pip install -r pip_requirements.txt
conda install -c conda-forge --file conda_requirements.txt 
```

或者，如果你只是想安装 [DVC，你也可以通过 pip:](https://dvc.org/doc/get-started/install)

```
pip install dvc 
```

回到 git 存储库，您会注意到有一个名为。dvc”。

```
.dvc/
├── cache
└── config

1 directory, 1 file 
```

这是我在本地运行“dvc init”命令时创建的，该命令是从用户那里抽象出来的(正如目录名中的点所示)。

现在有趣的部分来了。DVC 有远程数据存储的概念，用于[共享和版本化数据](https://dvc.org/doc/use-cases/data-and-model-files-versioning)。目前在实践中可以有[六种类型:本地目录、AWS S3、谷歌云存储、Azure Blob 存储、SSH 或 HDFS](https://dvc.org/doc/get-started/configure) 。要配置远程数据存储，只需将本地目录映射到远程数据存储。在这种情况下，我将“数据”目录(git 忽略了它)映射到远程存储，如下所示:

```
dvc remote add -d data s3://kaggle-datasets-store/house-prices 
```

接下来让我们将来自[ka ggle house prices](https://www.kaggle.com/c/house-prices-advanced-regression-techniques/data)的数据下载到“data”目录中，比如“train.csv”。您也可以使用以下命令从终端下载:

```
kaggle competitions download -c house-prices-advanced-regression-techniques 
```

现在让我们将它添加到我们的数据版本化存储中:

```
dvc add data/train.csv 

# you should see an output similar to the following:
Adding 'data/train.csv' to 'data/.gitignore'.
Saving 'data/train.csv' to cache '.dvc/cache'.
Created 'hardlink': .dvc/cache/80/ccab65fb115cbad143dbbd2bcd5577 -&gt; data/train.csv
Saving information to 'data/train.csv.dvc'. 
```

正如您在标准输出中看到的，该命令将生成一个“train.csv.dvc”文件，其中包含关于“train.csv”文件的元数据。本质上是指向文件的指针的 md5 散列。

将这些数据推送到 S3 很简单:

```
dvc push -r data 
```

注意，我指定的是“数据”存储库。如果你想了解 dvc 的配置，只要看看。DVC/配置文件。您会注意到我配置了一个 AWS 概要文件，它与我的本地 aws CLI 凭证文件相匹配。以下是如何配置您的本地 aws 凭据以访问 S3 存储桶:

```
aws configure 
```

这将提示您输入凭证以及默认区域和输出格式，并将它们存储在“~/”中。aws/config "和" ~/。AWS/凭证”。如果您希望拥有多个 AWS 帐户的凭证，那么 aws cli 允许您配置“配置文件”来存储这些凭证。只需按照以下步骤操作:

```
aws configure --profile 
```

如果你看我的”。dvc/config”您会注意到，我已经为自己的 AWS 帐户配置了别名为“diogo”的 AWS 概要文件。

我没有找到如何显式地进行这种配置的文档，所以如果你想知道你可以改变哪些其他配置，你可以像我一样做，只是看看 [DVC 源代码](https://github.com/iterative/dvc/blob/master/dvc/config.py)。

将数据推送到 S3 后，您将能够与其他同事共享。他们只需要运行:

```
dvc pull 
```

请注意，在我们的情况下，您将无法从我的 S3 桶中下载，因为它没有配置为公共访问，这对于您的公司数据也将是相同的情况。

现在让我们把一切联系起来，也就是我们的模型和数据。为此，我使用了来自 python 笔记本的[代码，使用了来自](https://www.kaggle.com/dansbecker/xgboost) [DanB](https://www.kaggle.com/dansbecker) 的 XGBoost 。DVC 有运行命令，您可以在其中指定哪些依赖项以及所需的输出。在我们的案例中:

```
dvc run -d data/train.csv --file xgboost_base_model.dvc -O data/xgboost_base.pkl python src/xgboost_base.py 
```

这将创建一个名为“xgboost_base_model.dvc”的文件，其中包含类似以下内容:

```
cmd: python src/xgboost_base.py
deps:
- md5: 80ccab65fb115cbad143dbbd2bcd5577
path: data/train.csv
md5: e10b323ae9bb3887626a7dc97d6a64d6
outs:
- cache: false md5: d53d8021c4c362b88da61c347af52196
path: data/xgboost_base.pkl 
```

注意，这个 dvc 文件包含我们的“xgboost_base.py”脚本的散列，以及数据的散列。它的美妙之处在于，我们现在只需“dvc repro xgboost_base_model.dvc ”,它就会检测数据或脚本是否发生了变化。如果没有发生变化，那么它将简单地输出:“管道是最新的。没什么可复制的。”

完成这些之后，我们现在就可以将所有代码添加到。git，包括“dvc repro xgboost_base_model.dvc”。这使得其他人也能够复制相同的结果。

这个例子相当简单。然而，更有趣的是将 dvc run 命令与几个步骤链接起来，以构建管道——这是一个定义完整机器学习过程的多阶段操作。我们强烈建议你看一看 DVC 自己关于管道的教程。