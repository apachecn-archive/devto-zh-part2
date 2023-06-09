# 数据清理和映射功能-利用英特尔人工智能技术学习人工智能

> 原文：<https://dev.to/rosejcday/data-cleaning-and-mapping-functions---learning-ai-with-intel-ai-technology--24g8>

一年多前，当我开始攻读硕士学位，然后辞去工作时，我开始了我的旅程，以学习更多关于数据科学和人工智能的知识。我作为一名数据科学专业的学生注册了全日制课程，从那以后我一直很享受这个决定。当我开始学习数据科学课程时，大多数课程都侧重于数据存储、管理和分析的基础知识。所以在业余时间，我一直在努力学习更多关于 AI 的知识。我在人工智能中发现的一个特别感兴趣的事情是图像识别和分类。通过这种方式，我涉足了不同的项目，研究运动和人物检测的实时图像识别。从这里继续，我发现分析卫星图像以从这些图像中收集信息是一件有趣的事情。我一直在做的一个特别的项目是我关于使用英特尔人工智能技术分析森林砍伐和城市化的提案。通过这些项目，我学到了数据导入、设置和管道方面的新东西，我认为会有兴趣与大家分享。

### 清洗图像

在为 AI 处理图像时，我学到的第一件事就是图像导入和设置并不像向模型发送图像那样简单。当使用英特尔优化 TensorFlow 来训练神经网络识别图像时，需要一个带有类别的图像数据集。我读到的第一件让我感到奇怪的事情是，所有的图像都应该是相同的大小，例如(28×28 像素)。这在用于训练神经网络的真实世界图像数据集上很常见，因为图像通常有不同的大小。根据这些信息，它们应该被分成固定的大小。这个信息在 TensorFlow 的[导入数据](https://www.tensorflow.org/guide/datasets#decoding_image_data_and_resizing_it)指南中有很好的解释，如下所示。

```
# Reads an image from a file, decodes it into a dense tensor, and resizes it
# to a fixed shape.
def _parse_function(filename, label):
  image_string = tf.read_file(filename)
  image_decoded = tf.image.decode_jpeg(image_string)
  image_resized = tf.image.resize_images(image_decoded, [28, 28])
  return image_resized, label 
```

由此，我能够理解图像在放入模型之前是如何预处理的。随着我继续研究图像是如何处理的，我也发现重新检查像素格式很有趣。最常见的像素格式是字节图像，这意味着 28 乘 28 像素图像中的每个像素的范围是从 0 到 255，其中 0 是黑色，255 是白色，中间的所有值都是灰色。这意味着所有处理的图像都是以灰度而不是彩色进行分析的。有关图像转换的更多信息，请参见 TensorFlow [Images](https://www.tensorflow.org/api_guides/python/image) 文档。

### 数据管道和解码图像

在了解了图像清理之后，我研究了数据管道和解码图像。从上面的代码片段可以看出，该函数从文件中读取图像，然后将该图像解码为稠密张量，然后将其调整为确定的 28 x 28 像素。

```
# A vector of filenames.
filenames = tf.constant(["/var/data/image1.jpg", "/var/data/image2.jpg", ...])

# `labels[i]` is the label for the image in `filenames[i].
labels = tf.constant([0, 37, ...]) 
```

可以看到，这个函数接收一组图像和标签。这些图像和标签可以保存在如下的 tf.constants 中。起初这看起来很奇怪，但通过进一步阅读，我意识到 tf.constant(value)的这个动作是将图像和标签保存到两个常量张量中，分别称为文件名和标签。

### 张量流映射函数

通过所有的数据清理、解码和流水线操作，最终归结到代码的最后两步，即在映射最终函数之前，从文件名和标签创建 TensorFlow 数据集。我发现这一行`tf.data.Dataset.from_tensor_slices`，乍一看特别费解。通过研究，我能够辨别出这一行指的是从上面创建的输入常量向量制作一个 TensorFlow 数据集。这允许在创建后对数据集中的图像文件和标签进行分割。

```
dataset = tf.data.Dataset.from_tensor_slices((filenames, labels))
dataset = dataset.map(_parse_function) 
```

一旦创建好，`dataset.map`函数就可以用之前创建的解析函数作为参数来调用。Map 允许跨数据集的所有元素进行转换，然后返回一个包含所有已转换项的新数据集。这些项目将以相同的顺序出现在原始输入中。因此，当调用这个 map 函数时，它调用最初创建的 parse 函数。这个解析函数之前为我们完成了所有的图像转换和清理工作。

几个月前，写一份关于使用英特尔人工智能技术分析森林砍伐和城市化的提案似乎是一件小事，但现在它让我能够进一步推进人工智能和图像分类。我希望到目前为止我学到的东西也能帮助到你！

#### 参考文献

封面图片来源于 [Vexel 的博客](https://www.vexels.com/blog/illustrator-tutorial-abstract-waves/)
[使用英特尔 AI 技术 DevMesh](https://devmesh.intel.com/projects/analyzing-deforestation-and-urbanization-using-intel-ai-technologies)
[英特尔优化 tensor flow](https://software.intel.com/en-us/articles/intel-optimization-for-tensorflow-installation-guide)
[tensor flow 导入数据](https://www.tensorflow.org/guide/datasets#decoding_image_data_and_resizing_it)
[TensorFlow 基本分类](https://www.tensorflow.org/tutorials/keras/basic_classification)
[构建图像数据管道](https://cs230-stanford.github.io/tensorflow-input-data.html#building-an-image-data-pipeline)
[TF . constant](https://www.tensorflow.org/api_docs/python/tf/constant)
[tensor flow 图像](https://www.tensorflow.org/api_guides/python/image)
[TensorFlow 数据集](https://www.tensorflow.org/api_docs/python/tf/data/Dataset#map)