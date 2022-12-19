# 将 CSV 行解析为单独的文本文件

> 原文：<https://dev.to/rosejcday/parsing-csv-rows-into-separate-text-files--29lk>

#100DaysofCode 挑战是由 Alexander Kallaway 发起的，旨在帮助人们学习如何更加一致地编码。这个挑战的目标是在 100 天内每天至少编码一个小时。

从这个前提出发，我决定每天至少花一个小时研究和编码图像分类。今天标志着第一周的结束，在这一周中，我研究并从事项目[使用英特尔人工智能技术](https://devmesh.intel.com/projects/analyzing-deforestation-and-urbanization-using-intel-ai-technologies)分析森林砍伐和城市化。在这个项目中，我一直在使用英特尔优化的 TensorFlow 对卫星图像进行图像分类。在这第一周，我学到了很多关于图像分类和标记的有趣的东西。我发现最有趣的分享之一是如何从 CSV 文件中解析标签，并将标签转换成我想要的格式。

对于我面临的这个挑战，让我们来看看下面的 CSV 文件示例。该表有两列，第一列是图像名称，第二列由标签数组组成。这是我正在处理的原始 CSV 文件的设计。

| 映像名 | 标签 |
| --- | --- |
| 图片 _001 | 阴霾城市农场 |
| 图片 _002 | 城市 |
| 图片 _003 | 水上农场 |

我需要的标签格式是获取图像名称并基于该名称创建一个文本文件。对于第一行，该文件将被称为`Image_001.jpg.txt`，并包含该图像的所有标签，每个标签在一个单独的行上。在 Python 3 中编码时，我创建了一个将原始 CSV 文件转换成所需文本文件的函数。如下图所示，文件`Image_001.jpg.txt`将被填充如下数据:

```
haze 
city 
farm 
```

为进行这种转换而创建的函数首先读入数据路径和文件名。数据路径是指存储原始 CSV 的路径，文件名是指 CSV 文件的名称。这些数据存储在数据帧中，然后可以遍历该数据帧来收集数据。CSV 中的每一行都作为变量行读取。一旦进入行，第一列`row[0]`用于创建新文本文件的名称。然后，根据空格拆分第二列`row[1]`。这个标签列表称为 splitrow，然后遍历这个列表，将每个标签追加到文本文件的新行上。完成后，文件关闭，可以为下一个图像创建新文件。这将为每个图像创建一个标签文本文件。

```
def _parse_labels(self):
df_train_tags = pd.read_csv(self.data_path + self.filename)
i = 0
for index, row in df_train_tags.iterrows():
    if i > len(df_train_tags):
        print("Error: Cannot read images, file length too short...")
        break
    else:
        print("Processing: Reading image " + str(row[0]) + "...")
        f = open(self.labels_path + str(row[0])+'.jpg.txt', 'w')
        splitrow = row[1].split(" ")
        for item in splitrow: 
            f.write(item + "\n")
        f.close()
        i+=1
print("Completed: Finished reading labels dataset...") 
```

这里有一个有趣的注意事项，我起初并没有意识到为什么在 for 循环中需要索引。当想到 for 循环时，我指的是`for each item in dataset then loop`，其中每一项都是数据帧中的一行。这个逻辑是有道理的，但是这里发生的是 iterrows 给出一个元组值，而不仅仅是行。这意味着我们必须为元组调用(index，row)来访问列。

在过去一周的研究和编码工作之后，这是我面临的最有趣的问题之一，因为它涉及到将一个单独的列拆分和转换成一个多行的文本文件。这个函数还有助于很好地概述 Python 中的基本数据帧和数组操作。

本周你面临过哪些有趣的问题？

### 参考文献:

封面图片来源于 [Alpha Coders](https://images6.alphacoders.com/368/368992.jpg)
[英特尔优化 tensor flow](https://ai.intel.com/tensorflow/)
[tensor board](https://github.com/tensorflow/tensorboard)
[tensor flow Guide](https://www.tensorflow.org/guide/)
[英特尔 DevMesh](https://devmesh.intel.com/users/rose-day)
[英特尔 AI 学院](https://software.intel.com/en-us/ai-academy)
[熊猫。DataFrame.iterrows](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.iterrows.html)