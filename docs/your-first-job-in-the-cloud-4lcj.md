# 你在云中的第一份工作

> 原文：<https://dev.to/yzvyagelskaya/your-first-job-in-the-cloud-4lcj>

## 第一章。云中的第一份工作

### [T1】简介](#intro)

> 我在一朵云上看见一个孩子，他笑着对我说...
> —威廉·布莱克

如今，一个人应该住在北冰洋中一个无人居住的岛上的洞穴里，从未听说过“人工智能”、“机器学习”、“NLP”和一系列流行词。拥有一个数据科学硕士学位，我对明天的人工智能革命感到有点不太兴奋。这并不意味着 *DS* 很无聊或者不合适——相反，它需要投入很多努力，我真的很喜欢那种在危险边缘做事情的感觉。

作为一个相对较新的行业，ML 还没有建立*流程*。我听说过一些与谷歌和脸书相反的事情，但是在小企业中我们仍然被认为是书呆子。二十年前开发者曾经扮演的角色。很高兴看到越来越多的人进入 ML，要么是对上次会议的谷歌幻灯片感到兴奋，要么是好奇神经网络是否真的可以区分照片上的猫和狗。

大公司准备并分享(感谢上帝，现在是 21 世纪)巨大的数据集、经过训练的模型以及初级数据科学家可能在沙盒中使用的一切。在我们确保基于谷歌或脸书数据训练的模型以某种方式工作，甚至可能预测事情(在某些情况下，在一些非常古怪的情况下，但仍然是*如此*激动人心)之后，我们通常想尝试自己训练自己的模型。在我们自己的笔记本电脑上要花几个小时，尽管数据集仅限于过去一年我们四十二个朋友的推文。结果通常看起来*有希望*，但是不尽人意。笔记本电脑不可能继续处理过去 10 年的全部推文信息而不使 SDD 爆炸。

这就是我们接触到神奇词汇的时候了:云计算。或者怎么命名。让我们的谷歌服务器爆炸，而不是我们可爱的笔记本电脑，对不对？没错。我们的下一项工作将是在云中。双关语。

没有那么多的资源，解释一个人如何停止拖延盯着笔记本电脑显示器看模型何时建成，并开始获得生活在 2018 年的好处。有 Google ML，有 Amazon SageMaker，有 Azure Machine Learning Studio，但所有地方的文档都是开发者为灰胡子极客写的。在云中执行第一个作业有一个巨大的门槛。这种写作应该是弥合这种差距。

这不是火箭科学，也没有什么真正复杂的。只需采取几个步骤和考虑几件事情。这是一次激动人心的旅程，一旦完成，接下来的旅程将变得轻而易举。我们走吧。

以下所有内容都是为 Google ML 引擎编写的，但它几乎可以原样应用于任何云计算系统。我会尽量不去深究细节，更多地关注于是什么而不是*怎么样*。

### 出发前

首先，我想参考一下那篇对我把工作搬到云上帮助很大的论文。刘福洋的博客写的 Tensorflow 初学者指南几乎是完美的，除了它没有涵盖陷阱，也没有建议它可能有意义的捷径。

谷歌也有一个关于 ML 引擎的[文档](https://cloud.google.com/ml-engine/docs/)，我希望我能聪明到用它*作为指南*。我们仍然需要它来快速查找这个和那个。

首先，我们需要[设置我们的云环境](https://cloud.google.com/ml-engine/docs/tensorflow/getting-started-training-prediction#setup)。我在这里参考谷歌指南，因为事情往往会随着时间的推移而变化，我希望他们将保持这一信息的更新。

在我们为 ML 启用帐户之后，我们应该设置我们的本地环境。我强烈建议使用 Linux，MacOS 或多或少是健壮的，Windows 会让你哭。一旦我们要在云中运行作业，我相信您已经安装并配置了 python。我们需要安装的是 [Google SDK](https://cloud.google.com/sdk/docs/) 。这很简单，从链接的页面下载并安装。

现在我们需要设置我们的凭证。 [`gcloud init`](https://cloud.google.com/sdk/docs/initializing) 应该做的事。

让我们检查一下它是否如预期的那样工作:

```
$ gcloud ml-engine models list
Listed 0 items. 
```

Enter fullscreen mode Exit fullscreen mode

哇哦。我们都准备好了。

### 我们的第一份工作

这是重要的部分。不要试图上传和运行你的最后一个项目。它会失败，你会感到沮丧。让我们慢慢地进入冷水。让我们让您的第一个任务成功完成，当您检查您的任务状态时，会显示一个迷人的绿灯图标。

云希望上传 python 包，并指定执行它的主模块。所以，让我们用一个非常简单的 python 包。让我们假设它被命名为`test1.py`，并驻留在名为`test1`的目录中。

```
# coding: utf-8 import logging
import argparse

if __name__ == "__main__":
  parser = argparse.ArgumentParser()

  parser.add_argument(
    '--job-dir',
    help='GCS job directory (required by GoogleML)',
    required=True
  )
  parser.add_argument(
    '--arg',
    help='Test argument',
    required=True
  )
  arguments = parser.parse_args().__dict__
  job_dir = arguments.pop('job_dir')
  arg = arguments.pop('arg')

  logging.info("Hey, ML Engine, you are not scary!")  
  logging.warn("Argument received: {}.".format(arg)) 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`logging`是因为与简单的`stdout`不同，日志可以通过 web 界面获得。

此外，您还需要在本地安装一个云配置文件。它可以放在任何地方，我更喜欢每个项目有一个配置文件。将`test1.yml`放在同一个目录:

```
trainingInput:
  scaleTier: CUSTOM
  # 1 GPU
  masterType: standard_gpu
  # 4 GPUs
  # complex_model_m_gpu
  runtimeVersion: "1.9"
  pythonVersion: "3.5" 
```

Enter fullscreen mode Exit fullscreen mode

我不确定是谁做的决定，但是 ML 引擎的默认`python`版本*是`2.7`，这就是为什么最后两行是必须的。*

您还需要创建一个文件`setup.py`，包含我们项目的描述。它将由 Google SDK 处理。

```
from setuptools import find_packages
from setuptools import setup

setup(
    name='test1',
    version='0.1',
    description='My First Job'
) 
```

Enter fullscreen mode Exit fullscreen mode

嗯，就是这样。我们试试(这个文件`test1.sh`应该和包文件夹在同一层。)

```
#!/bin/bash

export BUCKET_NAME=foo-bar-baz-your-bucket-name
export REGION=us-east1
export JOB_NAME="test1_$(date +%Y%m%d_%H%M%S)"
export JOB_DIR=gs://$BUCKET_NAME/$JOB_NAME

gcloud ml-engine jobs submit training $JOB_NAME \
    --staging-bucket gs://$BUCKET_NAME \
    --job-dir gs://$BUCKET_NAME/$JOB_NAME \
    --region $REGION \
    --runtime-version 1.9 \
    \
    --module-name test1.test1 \
    --package-path ./test1 \
    --config=test1/test1.yaml \
    -- \
    --arg=42 
```

Enter fullscreen mode Exit fullscreen mode

**NB！**您必须指定您的存储区名称*和*您可能还需要更改地区。

我强烈建议从一开始就创建一个 shell 脚本来运行(调度/排队)作业。当涉及到修改时，处理起来要容易得多。

这里有三个论点的“子部分”:前四个是特定于工作的，在不同的工作中保持不变。第二个是特定于工作的设置。第三个(在`--`之后)包含将被传递给包的`__main__`函数的参数。

去试试吧:

```
./test1.sh

Job [test1_20180818_085812] submitted successfully.
Your job is still active. You may view the status of your job with the command

  $ gcloud ml-engine jobs describe test1_20180818_085812

or continue streaming the logs with the command

  $ gcloud ml-engine jobs stream-logs test1_20180818_085812
jobId: test1_20180818_085812
state: QUEUED 
```

Enter fullscreen mode Exit fullscreen mode

现在您可以按照建议执行`gcloud ml-engine jobs describe ...`。它会吐出另一部分文本。复制最后一个链接并粘贴到您的浏览器地址栏中。你应该看看...

你应该看到什么，我将在下一章描述。快乐云！

[![yzvyagelskaya image](../Images/ea80fd4e9cc0f45ad4861b2dfc4912b7.png)](/yzvyagelskaya) [## 您在云计算领域的第一份工作/2

### Yulia Zvyagelskaya 8 月 19 日 1815 分钟阅读

#machinelearning #tutorial #cloudcomputing](/yzvyagelskaya/your-first-job-in-the-cloud-2-4ob8)[![yzvyagelskaya image](../Images/ea80fd4e9cc0f45ad4861b2dfc4912b7.png)](/yzvyagelskaya) [## 您在云计算领域的第一份工作/ 3

### Yulia Zvyagelskaya 8 月 25 日 184 分钟阅读

#machinelearning #tutorial #cloudcomputing](/yzvyagelskaya/your-first-job-in-the-cloud--3-28ka)