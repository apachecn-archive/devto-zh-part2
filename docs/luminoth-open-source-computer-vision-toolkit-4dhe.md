# Luminoth 0.1:开源计算机视觉工具包

> 原文：<https://dev.to/tryolabs/luminoth-open-source-computer-vision-toolkit-4dhe>

Luminoth 是一个基于 Tensorflow 和 Sonnet 的开源计算机视觉工具包。我们刚刚发布了一个新的版本，所以这是一个很好的时间去深入了解它！

0.1 版本带来了几个非常令人兴奋的改进:

*   添加了[单次多盒探测器(SSD)](https://arxiv.org/abs/1512.02325) 型号的**实现，这是一个比已经包含的[更快的 R-CNN](https://arxiv.org/abs/1506.01497) 更快(尽管不太精确)的物体探测器。这允许在大多数现代 GPU 上实时执行对象检测，允许处理例如视频流。**

*   对更快的 R-CNN 模型的一些**调整，以及新的基本配置，使其在 COCO 和 Pascal 数据集上训练时达到与其他现有实现相当的结果。**

*   **SSD 和更快的 R-CNN 模型的检查点**现已提供，分别在 Pascal 和 COCO 数据集上进行训练，并提供最先进的结果。这使得在图像中执行对象检测变得非常简单，因为这些检查点将由库自动下载，即使只是使用命令行界面。

*   一般性的**可用性改进**，例如大多数命令的命令行界面更简洁，支持预测视频，以及重新设计包含的 web 前端，以便轻松使用模型。

现在，我们将通过示例来探索这些特性，逐步
构建我们自己的探测器。

## 首先要做的事情:测试它

首先当然要装 Luminoth。在您的虚拟世界中运行:

```
`$ pip install luminoth`
```

(注意:如果您有可用的 GPU 并想使用它，请首先运行“pip install tensorflow-gpu ”,然后运行上述命令。)

由于增加了检查点功能，我们现在为更快的 R-CNN 和 SSD 提供了开箱即用的预训练模型。实际上，这意味着通过发出几个命令，您可以下载一个训练有素的对象检测模型供您使用。让我们从使用 Luminoth 的 CLI 工具`lumi`:`` 刷新检查点存储库开始

```
$ lumi checkpoint refresh
Retrieving remote index... done.
2 new remote checkpoints added.
$ lumi checkpoint list

================================================================================

|           id |                  name |       alias | source |         status |

================================================================================

| 48ed2350f5b2 |   Faster R-CNN w/COCO |    accurate | remote | NOT_DOWNLOADED |

| e3256ffb7e29 |      SSD w/Pascal VOC |        fast |  local | NOT_DOWNLOADED |
================================================================================

```

输出显示了所有可用的预训练检查点。每个检查点用`id`字段(在本例中为`48ed2350f5b2`和`e3256ffb7e29`)以及可能的`alias`(例如`accurate`和`fast`)来标识。您可以使用命令`lumi checkpoint detail <checkpoint_id_or_alias>`检查其他信息。我们将尝试更快的 R-CNN 检查点，所以我们将下载它(通过使用别名而不是 ID ),然后使用`lumi predict`命令:

```
$ lumi checkpoint download accurate

Downloading checkpoint...  [####################################]  100%

Importing checkpoint... done.
Checkpoint imported successfully.
$ lumi predict image.png
Found 1 files to predict.
Neither checkpoint not config specified, assuming `accurate`.
Predicting image.jpg... done.
{
  "file": "image.jpg",
  "objects": [
    {"bbox": [294, 231, 468, 536], "label": "person", "prob": 0.9997},
    {"bbox": [494, 289, 578, 439], "label": "person", "prob": 0.9971},
    {"bbox": [727, 303, 800, 465], "label": "person", "prob": 0.997},
    {"bbox": [555, 315, 652, 560], "label": "person", "prob": 0.9965},
    {"bbox": [569, 425, 636, 600], "label": "bicycle", "prob": 0.9934},
    {"bbox": [326, 410, 426, 582], "label": "bicycle", "prob": 0.9933},
    {"bbox": [744, 380, 784, 482], "label": "bicycle", "prob": 0.9334},
    {"bbox": [506, 360, 565, 480], "label": "bicycle", "prob": 0.8724},
    {"bbox": [848, 319, 858, 342], "label": "person", "prob": 0.8142},
    {"bbox": [534, 298, 633, 473], "label": "person", "prob": 0.4089}
  ]
}
```

`lumi predict`命令默认使用别名为`accurate`的检查点，但是我们可以使用选项`--checkpoint=<alias_or_id>`指定其他方式。在现代 CPU 上运行大约 30 秒后，输出如下:

[![](img/3aaf62c1e3700f229129a5e0487bec5f.png "People and bikes detected with the Faster R-CNN model.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JbYgO9yw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aqtczuhu3lgjoanj2gya.png)

您还可以将 JSON 输出写入一个文件(通过`--output`或`-f`选项)，并让 Luminoth 存储绘制了边界框的图像(通过`--save-media-to`或`-d`选项)。

## 现在实时！

除非你在未来几年读到这篇文章(来自过去的你好！)，
你可能注意到更快的 R-CNN 花了相当长的时间来检测图像中的物体。这是因为这种模型更倾向于预测准确性而不是计算效率，所以它实际上并不可行，例如，用于视频的实时处理(特别是如果你没有现代硬件的话):即使在非常快的 GPU 上，更快的 R-CNN 每秒也不会处理超过 2-5 张图像。

进入 SSD，单次多盒检测器。这个模型提供了一个较低的准确性(随着你想要检测的类别越多，准确性越强),但是速度却快得多。在相同的 GPU 上，你每秒可以获得几个图像，SSD 每秒可以获得大约 60 个图像，这使它更适合运行视频流或一般的视频。

那我们就这么做吧！再次运行`lumi predict`，但是这次使用`fast`检查点。还有，注意我们怎么没有事先下载；CLI 会注意到这一点，并在远程存储库中查找它。

```
$ lumi predict video.mp4 --checkpoint=fast --save-media-to=.
Found 1 files to predict.
Predicting video.mp4  [####################################]  100%     fps: 45.9
```

[![SSD model applied to dog playing fetch.](img/da600e8045828321a84085d9730854d0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--N4Mj-5P6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xowsrbzctqaqoiqkkun4.gif)

它，快多了！该命令将通过逐帧运行 SSD 来生成视频，因此没有花哨的时间预测模型(至少目前没有)。在实践中，这意味着您可能会看到一些框中的抖动，以及一些预测的出现和消失，但没有什么后期处理不能修复的。

## 当然，还要训练你自己

假设您只想检测窗外的汽车，并且您对 COCO 中的 80 个课程不感兴趣。训练您的模型来检测较低数量的类可能会提高检测质量，所以我们就这么做吧。然而，请注意，在 CPU 上的培训可能需要相当长的时间，所以一定要使用 GPU 或云服务，如谷歌的 ML 引擎(阅读更多关于 Luminoth 与其集成的信息[这里](https://luminoth.readthedocs.io/en/latest/usage/training.html#google-cloud))，或者干脆跳过这一部分，看看漂亮的图片。

Luminoth 包含从标准格式准备和构建自定义数据集的工具，例如 COCO 或 Pascal VOC 使用的格式。您还可以构建自己的数据集转换器来支持自己的格式，但这是另一篇博文的内容。现在，我们将使用`lumi dataset` CLI 工具构建一个仅包含汽车的数据集，这些数据取自 COCO 和 Pascal (2007 年和 2012 年)。

首先从这里的、这里的和这里的下载数据集，并将它们存储到在您的工作目录上创建的`datasets/`目录中(具体来说，存储到`datasets/pascal/2007/`、`datasets/pascal/2012/`和`datasets/coco/`)。然后通过运行以下命令，将所有数据合并到一个准备好供 Luminoth 使用的`.tfrecords`文件中:

```
$ lumi dataset transform \
        --type pascal \
        --data-dir datasets/pascal/VOCdevkit/VOC2007/ \
        --output-dir datasets/pascal/tf/2007/ \
        --split train --split val --split test \
        --only-classes=car
$ lumi dataset transform \
        --type pascal \
        --data-dir datasets/pascal/VOCdevkit/VOC2012/ \
        --output-dir datasets/pascal/tf/2012/ \
        --split train --split val \
        --only-classes=car
$ lumi dataset transform \
        --type coco \
        --data-dir datasets/coco/ \
        --output-dir datasets/coco/tf/ \
        --split train --split val \
        --only-classes=car
$ lumi dataset merge \
        datasets/pascal/tf/2007/classes-car/train.tfrecords \
        datasets/pascal/tf/2012/classes-car/train.tfrecords \
        datasets/coco/tf/classes-car/train.tfrecords \
        datasets/tf/train.tfrecords
$ lumi dataset merge \
        datasets/pascal/tf/2007/classes-car/val.tfrecords \
        datasets/pascal/tf/2012/classes-car/val.tfrecords \
        datasets/coco/tf/classes-car/val.tfrecords \
        datasets/tf/val.tfrecords
```

现在我们准备开始训练。为了使用 Luminoth 训练模型，您必须创建一个配置文件，指定一些必需的信息(例如运行名称、数据集位置和要使用的模型，以及一组依赖于模型的超参数)。因为我们已经提供了基本的配置文件，类似这样的文件就足够了:

```
train:
  run_name: ssd-cars
  # Directory in which model checkpoints & summaries (for Tensorboard) will be saved.
  job_dir: jobs/
  # Specify the learning rate schedule to use. These defaults should be good enough.
  learning_rate:
    decay_method: piecewise_constant
    boundaries: [1000000, 1200000]
    values: [0.0003, 0.0001, 0.00001]
dataset:
  type: object_detection
  # Directory from which to read the dataset.
  dir: datasets/tf/
model:
  type: ssd
  network:
    # Total number of classes to predict. One, in this case.
    num_classes: 1
```

将它作为“config.yml”存储在您的工作目录中(“datasets/”所在的位置)。如您所见，我们将训练一个 SSD 模型。您可以按如下方式开始运行:

```
$ lumi train -c config.yml
INFO:tensorflow:Starting training for SSD
INFO:tensorflow:Constructing op to load 32 variables from pretrained checkpoint
INFO:tensorflow:ImageVisHook was created with mode = "debug"
INFO:tensorflow:Create CheckpointSaverHook.
INFO:tensorflow:Graph was finalized.
INFO:tensorflow:Running local_init_op.
INFO:tensorflow:Done running local_init_op.
INFO:tensorflow:Saving checkpoints for 1 into jobs/ssd-cars/model.ckpt.
INFO:tensorflow:step: 1, file: b'000004.jpg', train_loss: 20.626895904541016, in 0.07s
INFO:tensorflow:step: 2, file: b'000082.jpg', train_loss: 12.471542358398438, in 0.07s
INFO:tensorflow:step: 3, file: b'000074.jpg', train_loss: 7.3356428146362305, in 0.06s
INFO:tensorflow:step: 4, file: b'000137.jpg', train_loss: 8.618950843811035, in 0.07s
(ad infinitum)
```

许多小时后，模型应该有一些合理的结果(当它超过一百万步左右时，您可以停止它)。您可以通过运行下面的命令并转到

```
$ lumi server web -c config.yml
Neither checkpoint not config specified, assuming 'accurate'.
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

[![Luminoth's frontend with cars being detected.](img/60708dcb136e394761d5a09b849747f2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fRS2Q9Fz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wuv53da8pord9z1e89xk.jpg)

由于 Luminoth 是基于 Tensorflow 构建的，您还可以通过在配置中指定的`job_dir`上运行它来利用
[Tensorboard](https://www.tensorflow.org/programmers_guide/summaries_and_tensorboard) ，以便查看训练进度。

## 结论

就是这样！这就结束了我们对 Luminoth 的新(旧)功能的概述:我们已经使用预训练的模型检测到图像和视频中的对象，甚至在几个命令中训练了我们自己的对象。我们将自己局限于 CLI 工具，甚至没有提到 Python API，通过 Python API，您可以将经过训练的模型用作更大系统的一部分。下次吧！

这是迄今为止功能最丰富的 Luminoth 版本，所以我们希望你能尝试一下。由于我们仍处于 0.1，你可能会碰到一些粗糙的边缘。请随意在我们的 GitHub 上写一些问题，甚至投稿！我们非常欢迎所有的反馈，让 Luminoth 变得更好。你也可以点击查看**文档** [，其中包含了更多的用法示例。](http://luminoth.readthedocs.io/)

再说一次，如果你遇到任何障碍，打电话给我们！

本文原载[此处](https://tryolabs.com/blog/2018/04/17/announcing-luminoth-0-1/)。