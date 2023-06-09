# 了解 Softmax 和 Sigmoid 在图像分类中的差异

> 原文：<https://dev.to/rosejcday/learning-the-differences-between-softmax-and-sigmoid-for-image-classification--59c>

#100DaysofCode 挑战赛第二周快乐，感恩节快乐，请看第一周，在那里我讨论了[将 CSV 行解析为单独的文本文件](https://dev.to/rosejcday/parsing-csv-rows-into-separate-text-files--29lk)。

在过去的一周里，我继续研究和开发项目[使用英特尔人工智能技术分析森林砍伐和城市化](https://devmesh.intel.com/projects/analyzing-deforestation-and-urbanization-using-intel-ai-technologies)项目。在这个项目中，我一直在与英特尔优化 TensorFlow 合作，在英特尔 Nuc 上对卫星图像进行图像分类。在这第二周，我着重于更好地理解神经网络，以及它们如何使用 softmax 或 sigmoid 基于期望的输出进行图像分类。

我发现研究的第一件有趣的事情是多类和多标签分类模型之间的差异。当提到一个多类模型时，这意味着从存在的许多标签中只给输出分配一个标签。因此，在查看蔬菜图像时，可以有南瓜、黄瓜和胡萝卜标签，但是胡萝卜图像将只接收一个标签作为输出，并且不能同时是多个蔬菜。相比之下，多标签分类可以为一幅图像分配多个输出。这在可以同时谈论多个话题的文本中很容易看到。一旦我理解了多类或多标签之间的区别，我就开始研究如何将 softmax 和 sigmoid 用于每种情况，以及为什么。

### Softmax

通过我的研究，很明显 softmax 图层适用于多分类，而 sigmoid 图层适用于多标注。神经网络的 softmax 层是允许多标签的广义逻辑函数。Softmax 允许我们处理 [![](img/211c6ec70fe4a0edc12503497a3f101e.png "\Large x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}")](https://res.cloudinary.com/practicaldev/image/fetch/s--axbIW3Ez--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://latex.codecogs.com/svg.latex%3F%255CLarge%26space%3By%255E%257B%28i%29%257D%2520%255Cin%2520%255Cleft%2520%255C%257B%2520%25201%2C%2520...%2520k%2520%255Cright%2520%255C%257D) 其中 k 是类的数量。Softmax 用于计算特定标签在 k 个不同标签上的概率分布。Softmax 为所有概率等于 1 的输出返回 0 到 1 的范围。对于多标签，这将返回每个标签的概率，其中目标标签的概率最高。这在预测一组标签中的一个标签时非常理想。这可以用张量流来表示:

```
# tf.nn.softmax final_tensor = tf.nn.softmax(logits, name=final_tensor_name) 
```

这将通过计算 softmax 激活来设置图像重分类脚本中的最终张量。这样，可以用 softmax 更新的神经网络的另一个方面是交叉熵函数。如下所示，该函数允许在互斥的离散分类任务中测量概率误差。

```
# tf.nn.softmax_cross_entropy_with_logits cross_entropy = tf.nn.softmax_cross_entropy_with_logits(logits,ground_truth_input) 
```

这意味着一个图像在一个任务中，也就是说，一个图像是一个可以从一组水果中选择的水果，但是图像不能超过一个水果。

### s 形函数

相反，sigmoid 函数可用于多标签分类。sigmoid 函数是另一种逻辑函数，它具有典型的“S 曲线”，或者阶跃函数的平滑版本。Sigmoids 通常被引入神经网络以提供模型的非线性，并且通常用于聚类、模式分类和函数逼近。与 softmax 给出 k 个类的概率分布不同，sigmoid 函数允许独立的概率。当将 sigmoid 函数视为神经网络中的神经元时，sigmoid 神经元的输入值可以是 0 到 1 之间的任何值，并且输出是 sigmoid 函数。这可以用张量流表示如下:

```
# tf.nn.sigmoid final_tensor = tf.nn.sigmoid(logits, name=final_tensor_name) 
```

这通过计算 sigmoid 激活来设置图像重分类脚本中的最终张量。这样，可以用 sigmoid 更新的神经网络的另一个方面是交叉熵函数。如下所示，该函数测量离散分类任务的概率误差。

```
# tf.nn.sigmoid_cross_entropy_with_logits cross_entropy = tf.nn.sigmoid_cross_entropy_with_logits(logits,ground_truth_input) 
```

这些分类任务并不相互排斥，每一类都是独立的。因此，该功能允许多标签分类，其中图像可以包含需要检测的多种水果。

本周你面临过哪些有趣的问题？

### 参考文献:

封面图片来源于 [Alpha Coders](https://images6.alphacoders.com/368/368992.jpg)
[英特尔优化 tensor flow](https://ai.intel.com/tensorflow/)
[tensor board](https://github.com/tensorflow/tensorboard)
[tensor flow Guide](https://www.tensorflow.org/guide/)
[英特尔 DevMesh](https://devmesh.intel.com/users/rose-day)
[英特尔 AI Academy](https://software.intel.com/en-us/ai-academy)
[soft max 函数](http://deeplearning.stanford.edu/tutorial/supervised/SoftmaxRegression/)
[Sigmoid 函数](https://excel.ucf.edu/classes/2007/Spring/appsII/Chapter1.pdf)
[统计方法简介](https://www-bcf.usc.edu/~gareth/ISL/ISLR%20Seventh%20Printing.pdf)
[多类 vs](https://scikit-learn.org/stable/modules/multiclass.html)