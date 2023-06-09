# 亚马逊 SageMaker:总结 6 个月的客户会议

> 原文：<https://dev.to/juliensimon/amazon-sagemaker-summing-up-6-months-of-customer-meetings-3o27>

大约 6 个月前，亚马逊 SageMaker 在 re:Invent 2017 上[发布](https://www.youtube.com/watch?v=lM4zhNO5Rbg)。从那以后，我与许多 AWS 客户讨论了这种新的机器学习服务如何帮助他们解决长期存在的痛点，从而腾出时间和资源来专注于实际的高价值机器学习任务。

在这篇文章中，我将尝试总结这些讨论，希望能够帮助更多的人在正确的方向上开始。

[![](img/b83629eafc497eca4daf549d78591488.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LGL-xQ2n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Acb7AYoikk-4FUhm98LV-Og.jpeg) 

<figcaption>哦好极了。法国航空公司又罢工了。最好快点！</figcaption>

#### 痛点#1:“我们不知道从哪里开始”

很多公司根本不知道如何开始。我想你可以写关于这个主题的书，但这里有一些简单的建议:)

[AWS 上的机器学习](http://ml.aws)有多层。根据你的技能水平和资源，其中一个会更有意义，我建议你先探索这个。

*   对于通用问题，应用服务是最简单的选择。无论是[图像分析](http://aws.amazon.com/rekognition)、[文字转语音](http://aws.amazon.com/polly)、[自然语言处理](http://aws.amazon.com/comprehend)等等，使用它们都不需要先了解机器学习。没有数据集，没有培训:只需将您的数据传递给 API，就能完成工作。
*   **如果你需要对数据集和/或算法进行更多的控制，那么** [**亚马逊 SageMaker**](http://aws.amazon.com/sagemaker) **就是下一个逻辑步骤**。例如，你可能想要建立一个在你的医学图像数据集上训练的癌症检测模型，或者一个专门研究法律术语的机器翻译模型——这两个任务太专业了，以至于 [Amazon Rekognition](http://aws.amazon.com/rekognition) 和 [Amazon Translate](http://aws.amazon.com/translate) 无法正确处理。
*   **如果您需要完全控制包括基础设施在内的一切，那么您应该考虑将** [**EC2 实例**](http://aws.amazon.com/ec2) **与** [**深度学习 AMI**](https://aws.amazon.com/machine-learning/amis/) 一起使用，这是一个预建的服务器映像，包括 TensorFlow、PyTorch 等流行工具，以及 NVIDIA 库和用于 GPU 培训的驱动程序。

现在，着手机器学习项目呢？下面是我看到的客户成功使用的两种技术。

*   **从小处着手，迭代逐步增加复杂度** ( [高尔定律](https://en.wikipedia.org/wiki/John_Gall_(author))！).这里也一样，为期 12 个月、将交付巨大成果的伟大项目从未真正发挥作用，不是吗？
*   建造许多小教堂，而不是大教堂。想想拖垮你公司的 100 个小的低效之处:手动流程、应用程序中的硬编码“业务规则”等等。对列表进行优先排序，并开始构建简单的机器学习模型来修复每一个问题。这似乎比大公司范围的人工智能项目更快、风险更小地产生了积极的影响，这个项目将解决我们所有的问题。以我的经验来看，大爆炸方法只有一件事是肯定的:它确实会以爆炸结束。

#### 痛点#2:“我们不能(或不想)编写机器学习代码”

这听起来很奇怪，但我经常听到:一些公司有特定领域的问题，无法通过上面讨论的应用服务解决，但他们没有足够的机器学习技能——或者他们不想花时间——从头开始编写所有东西。

当然，有很多好的机器学习和深度学习库( [scikit-learn](http://scikit-learn.org/) ， [Apache Spark MLlib](https://spark.apache.org/mllib/) 等)。)，为开发者提供内置算法的集合。我相信亚马逊 SageMaker 更进一步，为开发者带来了:

*   [**高度可扩展的实现**](https://www.youtube.com/watch?v=IeIUr78OrE0) 训练模型比其他实现更快更便宜。
*   [**最先进的算法**](https://docs.aws.amazon.com/sagemaker/latest/dg/algos.html) 像**图像分类** (CNN 架构)**、seq2seq** (LSTM 架构) **DeepAR** (多变量时间序列)**blazing text**(word 2 vec 的 GPU 实现)等等。

有了这些内置的算法，你不需要编写一行机器学习代码。您将编写的唯一代码实际上是“助手代码”:定义数据集的存储位置，设置超参数等。你会在这本[笔记本收藏](https://github.com/awslabs/amazon-sagemaker-examples)中找到很多有用的例子。就像我常说的:如果你读写 50 行 Python，你就能做到这一点！

#### 难点 3:“构建和准备数据集很难”

哦，当然。每个处理数据的人都知道，收集、清理和转换数据是一项繁重的工作。有时候对数据本身就是一种挑战！我最近遇到了一个非常大的企业客户的数据科学团队，他们试图从几十年的内部研究中收集数据。正如他们所说:“数据在 pdf 中”……我感受到了他们的痛苦。

一般来说，**在实际的机器学习过程**之前，需要大量的 ETL 工作。AWS 有一套[大数据和分析服务](https://aws.amazon.com/big-data/analytics/)，肯定会派上用场:)

此外，亚马逊 SageMaker 包括一个 [Spark SDK](https://github.com/aws/sagemaker-spark) ，它让开发人员**将 Spark 应用程序与 SageMaker 管理的培训和预测工作无缝集成。**简而言之，这让您可以将 ETL 关注点从机器学习关注点中分离出来，为每一个关注点使用最佳的实例类型和最佳的集群大小，等等。如果你想了解更多，请参考这篇[博客文章](https://medium.com/@julsimon/apache-spark-and-amazon-sagemaker-the-infinity-gems-of-analytics-8bd780b07243)或这篇[网络研讨会](https://medium.com/@julsimon/tech-talk-using-apache-spark-with-amazon-sagemaker-32ea9dd95e2b)。

#### 痛点#4:“一切都需要永远”

另一个流行的。机器学习团队经常生活在一个筒仓里…或者一个掩体里…或者一个象牙塔里。他们为 IT 团队创建了一张获取服务器的入场券。他们为生产团队创建更多的标签来部署他们的模型。每天都需要跨越界限。人们之间的交流不够。到处都是傲慢的混蛋让事情变得更糟。一切都会好起来的…对吗？

很多软件团队早就采用 DevOps 来解决这个问题。事实证明，我相信这也适用于机器学习(MLOps，有人吗？).当然，重点不是要把数据科学家和机器学习工程师变成铁杆运营战士(反过来也不行)。

**团队需要的是使用一个简单的工具试验、构建和部署模型的能力**。我相信亚马逊 SageMaker 通过其定制的 [Python SDK](https://github.com/aws/sagemaker-python-sdk) 让他们做到了这一点。它有效地让团队负责从实验到生产的整个过程。

同样， [notebook collection](https://github.com/awslabs/amazon-sagemaker-examples) 将向您展示如何针对所有配置做到这一点:内置算法、深度学习库的内置环境，甚至是您自己定制的环境。

#### 痛点#5:“基础设施跟不上”

老样子，老样子:**永远没有足够的物理存储或计算来以客户需要的规模运行他们想要的所有项目**。

这个问题以多种形式出现:

*   开发环境和生产环境是不同的，会导致意想不到的问题和倒退。
*   没有足够的硬件可用于实验，这减缓了创新。我遇到了一个很棒的公司，团队使用内部网来保留物理服务器几个星期…你猜怎么着，他们“忘记”归还，然后真正有趣的事情开始了:-/
*   一年前花费大量金钱的 GPU 现在已经过时，但在未来几年内不会被取代。
*   GPU 服务器的功耗导致数据中心出现电气和 HVAC 热点。

这样的例子不胜枚举。深度学习只会让事情变得更糟，因为它拥有庞大的图像数据集，以及对 10，000 美元以上 GPU 的不可抑制的渴望:)

**EC2 实例和亚马逊 SageMaker 解决了所有的问题**(还有一些)。到目前为止，AWS 的弹性和可伸缩性优势是众所周知的，它们也适用于机器学习工作负载。

#### 痛点#6:“我们必须控制成本”

啊，花最少的钱登上月球的永恒矛盾:)继续前面的弹性和可伸缩性主题，Amazon SageMaker 让您创建:

*   **全托管开发环境**(又名[笔记本实例](https://docs.aws.amazon.com/sagemaker/latest/dg/nbi.html))。开发人员可以从大量的实例类型中选择:最便宜的是 ml.t2.medium，每小时 0.0464 美元。您可以在不需要时停止它们，在需要时重新启动它们，从而节省资金。这对于 EC2 用户来说听起来非常熟悉，但对于刚刚开始使用 AWS 的机器学习团队来说可能是新闻:)
*   **使用 SageMaker SDK 按需创建的全面管理的培训环境**，在培训工作完成时自动终止，并按秒计费。这保证了您永远不会为培训基础架构支付过高的费用，这是物理基础架构的一个常见问题…即使是基于云的集群，如果您管理不当也是如此。
*   **完全管理的预测环境**，使用 SageMaker SDK 按需创建和终止。[自动缩放](https://docs.aws.amazon.com/sagemaker/latest/dg/endpoint-auto-scaling.html)现在也可用了，这是不超支的另一个重要因素！

#### 关闭思绪

Amazon SageMaker 只有 **6 个月大**,我们仍在迭代添加新功能，客户告诉我们这对他们来说是优先考虑的。尽管如此，我与 AWS 客户的接触告诉我，它已经在机器学习社区引起了[的共鸣。当然，](https://www.marketwatch.com/press-release/tens-of-thousands-of-customers-flocking-to-aws-for-machine-learning-services-2018-04-04) [AWS re:Invent 2018](https://reinvent.awsevents.com/) 即将到来:我迫不及待地想看看届时会宣布什么。

最后但同样重要的是:这项服务是 [**AWS 免费层**、](http://aws.amazon.com/free)的一部分，所以我鼓励你尝试一下，亲自看看它是否有助于解决你的机器学习痛点，并给我发送一些**反馈**。你尝试了什么？你喜欢什么？更重要的是，你错过或不喜欢什么？请联系并告诉我！

今天到此为止。感谢您的阅读，一如既往，请随时向我提问，在这里或在 [Twitter](https://twitter.com/julsimon) 上。

[https://medium . com/media/748 a06e 9019942 f 2677 BBA 917 ef 55804/href](https://medium.com/media/748a06e9019942f2677bba917ef55804/href)

* * *