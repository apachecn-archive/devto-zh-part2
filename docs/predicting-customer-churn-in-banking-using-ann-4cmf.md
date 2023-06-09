# 用人工神经网络预测银行业的客户流失

> 原文：<https://dev.to/blackbird/predicting-customer-churn-in-banking-using-ann-4cmf>

[![](img/40c3ca1e0eee8fc5f29bcb5548b805a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EWmXtCCN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2ACAnlolK6WNrLQBnz) 

<figcaption>利用人工神经网络预测银行业客户流失</figcaption>

### 数据集

数据集“Churn _ Modelling.csv”包含一家银行的 10，000 名客户的记录，包括以下各列:

1.  RowNumber
2.  CustomerId
3.  姓
4.  信用分数
5.  地理
6.  性别
7.  年龄
8.  任期
9.  保持平衡
10.  NumOfProducts
11.  哈斯卡尔
12.  IsActiveMember
13.  估计销售额
14.  激动的

通过使用列 1 至 13，我们希望预测客户是否会退出，即列 14。

### 数据预处理

*   删除不必要的功能
*   标签编码器
*   一个热编码器
*   列车测试分离
*   标准缩放器

### 使用 Keras 的简单 ANN 模型

**创建一个共有 4 层的人工神经网络**

*   一个具有 11 个输入要素和 6 个输出要素的输入图层
*   具有 6 个输出特征的隐藏层
*   具有 1 个输出要素的最终图层

**激活功能**

*   第 1 层:继电器
*   第二层:继电器
*   第三层:乙状结肠

**超参数**

*   优化器:亚当
*   损失:二元交叉熵
*   度量:准确性
*   批量大小:10
*   纪元:100 年

**精确度(可能会有变化)**

*   训练集:0.8610
*   测试集:0.86

### 改善安

*   使用 **k-fold 分类器**将训练集分割成 10 个部分，并对 10 个部分中的 9 个部分应用训练，每次都对另一个部分进行测试，以减少每次运行代码时的准确性波动。
*   使用具有特定阈值的丢弃技术来减少对训练集的过度拟合。应用于该数据集给出了 0.8321 的准确度，这意味着现在数据较少过度适合于该训练集。
*   使用 **GridSearchCV** 自动寻找最佳参数。输入所有你想用来测试你的网络的超参数，在测试完所有参数后，它会给出最好的精度和参数。我尝试了以下参数:

**批量大小:** 25，32

**纪元:** 100，500

优化器:亚当，rmsprop

*   早上醒来后(是的，需要很长时间…)，这是我发现的…

**最佳 _ 参数**

*   批量大小:25
*   纪元:500 年
*   优化器:rmsprop

**精度:** 0.8545

### 进一步改进

您可以通过更改超参数和尝试 GridSearchCV 中其他范围的值来进一步改进这个模型。但是需要注意的是，随着 GridSearchCV 中参数数量的增加，训练时间也会增加。

### 代码

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [ blackbird71SR ](https://github.com/blackbird71SR) / [神经网络项目](https://github.com/blackbird71SR/Neural-Network-Projects)

### 神经网络项目

<article class="markdown-body entry-content p-5" itemprop="text">

# 神经网络

## 1.[预测银行业的客户流失](https://github.com/Omkar-Ajnadkar/Neural-Network-Projects/tree/master/Predicting%20Customer%20Churn%20In%20Banking)

## 2.[预测 StackOverflow 上的标签](https://github.com/Omkar-Ajnadkar/Neural-Network-Projects/tree/master/Predict%20tags%20on%20StackOverflow)

## 3.[手语和静态手势识别](https://github.com/Omkar-Ajnadkar/Neural-Network-Projects/tree/master/Sign%20Language%20and%20Static-Gesture%20Recognition)

</article>

[View on GitHub](https://github.com/blackbird71SR/Neural-Network-Projects)

发送任何建议和错误的请求…