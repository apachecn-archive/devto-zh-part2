# 用 TensorFlow 教我的机器人

> 原文：<https://dev.to/whatrocks/teaching-my-robot-with-tensorflow-3i1a>

如果你像我一样，那么你会想尽一切办法拥有自己的 R2-D2 或 BB-8 机器人伙伴。想象一下你们一起经历的可爱冒险吧！

我很高兴地告诉大家，安奇·科兹莫就是你们一直在寻找的机器人。

科兹莫是挤在狭小生活空间里的大人物。你不需要知道如何编码来玩 Cozmo - *但是如果你知道* -那么 Cozmo 就有更强大的能力。

在这篇文章中，我将向你展示如何通过 FloydHub 上的 TensorFlow 使用迁移学习来教你自己的 Cozmo 识别日常物体。

## 设置

安装 [Cozmo Python SDK](http://cozmosdk.anki.com/docs/) ，创建一个新的 virtualenv，并将 [cozmo-tensorflow](https://www.github.com/whatrocks/cozmo-tensorflow) 项目克隆到您的本地机器上。

```
virtualenv ~/.env/cozmo -p python3
source ~/.env/cozmo/bin/activate
git clone https://www.github.com/whatrocks/cozmo-tensorflow
cd cozmo-tensorflow
pip install -r requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

接下来-登录 FloydHub CLI(在此注册一个[免费账户)。如果你需要安装 FloydHub CLI，只需](https://www.floydhub.com/plans)[查看我们](https://docs.floydhub.com/guides/basics/install/)[文档](https://docs.floydhub.com/)中的指南。

```
floyd login 
```

Enter fullscreen mode Exit fullscreen mode

## 1。使用 Cozmo 生成训练数据

为深度学习项目获取足够的训练数据可能是一件痛苦的事情。但谢天谢地，我们有一个机器人，他喜欢跑来跑去，用他的相机拍照，所以我们就让 Cozmo 拍一些我们希望我们的机器人学习的东西吧。

让我们从一罐美味的高价矿泉水开始。把科兹莫直接放在一罐矿泉水前面。确保你的机器人在拍照时有足够的空间绕着罐子旋转。运行`cozmo-paparazzi`脚本时，请确保输入 Cozmo 正在拍摄的对象的名称。

```
python3 cozmo-paparazzi.py seltzer 
```

Enter fullscreen mode Exit fullscreen mode

[![CozmoPaparazzi](../Images/c40ae843a349eb1bf1c3ccf42be3c60b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MciKfK5w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hvn6reuu4pxc8xyx3rzn.gif)

重复这一步，你想让 Cozmo 学习多少对象(标签)就学习多少！现在，您应该可以在本地目录的`/data`文件夹中看到所有图像标签的子目录。

### 上传数据集到 FloydHub

接下来——让我们把我们的图像上传到 [FloydHub](https://www.floydhub.com/whatrocks/datasets/cozmo-images) 作为 [FloydHub 数据集](https://docs.floydhub.com/guides/create_and_upload_dataset/)。这将允许我们在 FloydHub 上即将到来的模型培训和模型服务工作中挂载这些图像。FloydHub 上的数据集是您的培训作业引用受版本控制的数据集的一种简单方式。

```
cd data
floyd data init cozmo-images
floyd data upload 
```

Enter fullscreen mode Exit fullscreen mode

在我们的例子中，我将这个图像数据集命名为`cozmo-images`。我已经把它做成一个[公共数据集](https://www.floydhub.com/whatrocks/datasets/cozmo-images)，所以你可以在你自己的 Cozmo 项目中随意使用它！

## 2。在 FloydHub 上训练我们的模型

现在好戏开始了。首先，确保您在项目的根目录中，然后初始化一个 FloydHub 项目，以便我们可以在 FloydHub 的一个完全配置的 TensorFlow cloud GPU 机器上训练我们的模型。

旁注-如果最后一句话听起来很难，那么只需知道 FloydHub 会负责配置和优化你的云机器上的一切，以便为你的 GPU 驱动的深度学习实验做好准备。你可以指定你想要使用的确切的深度学习框架——无论是 TensorFlow 1.4 还是 PyTorch 0.3 还是[more](https://docs.floydhub.com/guides/environments/)——FloydHub 将确保你的机器拥有你立即开始训练所需的一切。

好了，回到正题，让我们初始化我们的项目:

```
floyd init cozmo-tensorflow 
```

Enter fullscreen mode Exit fullscreen mode

现在我们准备在 FloydHub 上开始深度学习培训工作。

需要注意一些事情:

*   我们将用谷歌提供的 [Inception v3 模型](https://github.com/tensorflow/models/tree/master/research/inception)做一些简单的迁移学习。我们可以从这个预训练的模型开始，而不是从头开始训练一个模型，然后只交换出它的最后一层，这样我们就可以教它识别我们希望 Cozmo 学习的对象。迁移学习是一种非常有用的技术，你可以在 [TensorFlow 的网站](https://www.tensorflow.org/tutorials/image_retraining)上了解更多。
*   我们将在 FloydHub 机器上的`/data`目录中挂载 Cozmo 创建的带有`--data`标志的图像数据集。
*   我为这项工作启用了 Tensorboard 的`--tensorboard`标志，这样我就可以直观地监控我工作的培训过程
*   我已经编辑了`retrain.py`脚本([最初由 TensorFlow 团队](https://github.com/googlecodelabs/tensorflow-for-poets-2)提供)以将其输出写入`/output`目录。当你使用 FloydHub 时，这一点非常重要，因为 FloydHub 作业总是将它们的输出存储在`/output`目录中。在我们的例子中，我们将把重新培训的 ImageNet 模型及其相关的培训标签保存到作业的`/output`文件夹中。

```
floyd run \
  --gpu \
  --data whatrocks/datasets/cozmo-images:data \
  --tensorboard \
  'python retrain.py --image_dir /data' 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！不需要在 AWS 上配置任何东西或者安装 TensorFlow 或者处理 GPU 驱动之类的东西。(如果你正在密切关注，我没有在我的作业命令中包括`--env`标志——这是因为 FloydHub 的默认环境包括 TensorFlow 1.1.0 和 Keras 2.0.6，这是我的培训所需要的😎).

一旦您的工作完成，您将能够在您工作的输出目录中看到您新重新培训的模型。

我建议将您的作业输出转换为独立的 FloydHub 数据集，以便您在未来的作业中更容易地挂载我们的再训练模型(我们将在下一步中执行)。您可以通过单击作业输出页面上的“创建数据集”按钮来完成此操作。查看名为 [cozmo-imagenet](https://www.floydhub.com/whatrocks/datasets/cozmo-imagenet) 的数据集，查看我重新训练的模型和标签。

## 3。将 Cozmo 连接到我们的再培训模型

我们可以通过在 FloydHub 上运行另一个作业来测试我们新重新培训的模型，该作业:

*   安装我们的[再培训模型并贴上标签](https://www.floydhub.com/whatrocks/datasets/cozmo-imagenet)
*   为模型服务设置公共 REST 端点

[模特服务](https://docs.floydhub.com/guides/run_a_job/#-mode-serve)是 FloydHub 上的一个实验性功能——我们很乐意在推特上听到你的[反馈！为了让这个特性工作，你需要在你的项目代码中包含一个简单的叫做`app.py`的 Flask 应用。](https://www.twitter.com/floydhub_)

对于我们当前的项目，我创建了一个简单的 Flask 应用程序，它将在 POST 请求中接收来自 Cozmo 的图像，使用我们在上一步中训练的模型对其进行评估，然后用模型的结果进行响应。Cozmo 然后可以使用结果来确定它是否在看一个特定的对象。

```
floyd run \
  --data whatrocks/datasets/cozmo-imagenet:model \
  --mode serve 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们运行我们的`cozmo-detective.py`脚本，让 Cozmo 在办公室里移动来寻找一个特定的对象。

```
python3 cozmo-detective.py toothpaste 
```

Enter fullscreen mode Exit fullscreen mode

每当 Cozmo 移动时，机器人会将它看到的任何东西的黑白图像发送到 FloydHub 上的模型端点——Floyd hub 将根据该图像运行模型，返回以下有效载荷和“Cozmo 的猜测”以及计算猜测花费的时间。

```
{
  'answer': 
    {
      'plant': 0.022327899932861328, 
      'seltzer': 0.9057837128639221, 
      'toothpaste': 0.07188836485147476
    }, 
  'seconds': 0.947
} 
```

Enter fullscreen mode Exit fullscreen mode

如果 Cozmo 至少有 80%的信心它正在看着有问题的物体，那么机器人就会胜利地向它跑去！

[![finder](../Images/8b1d806c4c939f676ac31e6f571fd91d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MmSQIk97--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vuxx3xomgncw95jthdte.gif)

一旦 Cozmo 找到了你所有丢失的对象，不要忘记关闭你在 FloydHub 上的服务工作。

## 新的希望

这是一个神奇的世界，兄弟...我们去探险吧！

我渴望看到你和你的 Cozmo 能一起找到什么，以及你在 FloydHub 的朋友们的一点帮助。在 [Twitter](https://twitter.com/floydhub_) 上与我们分享您的发现！

### 参考文献

这个项目是 [@nheidloff](https://dev.to/nheidloff) 的 [Cozmo 视觉识别项目](https://github.com/nheidloff/visual-recognition-for-cozmo-with-tensorflow)和[Google Code Labs tensor flow for Poets 项目](https://codelabs.developers.google.com/codelabs/tensorflow-for-poets/#0)的延伸。我也在我的[个人网站](https://www.charlieharrington.com/teaching-my-robot-with-tensorflow)上写了这个项目——除了更多地提到了《短路》和《塞尔达传说》。