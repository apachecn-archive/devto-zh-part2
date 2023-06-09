# 掌握模型部署的神秘艺术

> 原文：<https://dev.to/juliensimon/mastering-the-mystical-art-of-model-deployment-2afi>

关于算法选择、超参数优化等等的所有讨论，你可能会认为训练模型是机器学习过程中最难的部分。然而，根据我的经验，真正棘手的步骤是在 web 生产环境中安全地部署这些模型。

在这篇文章中，我将首先谈论在生产中部署和验证模型的典型任务。然后，我将介绍几种**模型部署技术**以及如何用[亚马逊 SageMaker](http://aws.amazon.com/sagemaker) 实现它们。特别是，我将向您详细展示如何在同一个预测端点上**托管多个模型，这是最小化部署风险的一项重要技术。**

[![](img/5efe5cf5ccc952959b0c7917b028a936.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--coU37nwR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/500/1%2AAZHhCobmks9vT2vWYN0kww.gif) 

<figcaption>那家伙扮演了艾伦·图灵和奇异博士。努夫说。</figcaption>

### 验证模型

即使您已经在数据科学沙盒中仔细地训练和评估了模型，也需要额外的工作来检查它是否能在您的生产环境中正确工作。这通常包括以下任务:

*   建立一个**监控系统**来存储和可视化模型度量，
*   构建一个**测试 web 应用**捆绑模型并运行**技术测试**(模型速度快吗？它需要多少内存？等等。)以及**预测测试**(我的模型还在按预期预测吗？).
*   将模型与您的**业务应用**集成，并运行端到端测试，
*   使用诸如**蓝绿色部署**或**金丝雀测试**之类的技术在生产中部署应用程序(稍后将详细介绍)，
*   在更长的时间内并行运行**同一模型的不同版本**，以便根据业务度量来衡量它们的长期有效性(也称为 **A/B 测试**)。

相当多的工作，然后。让我们首先来看看部署模型的不同方式。

### 部署选项

#### 标准部署

最简单的形式是，部署一个模型通常需要构建一个定制的 **web 应用程序来托管您的模型**并接收预测请求。测试是您所期望的:发送 HTTP 请求、检查日志和检查指标。

SageMaker **大大**简化了这个过程。只需几行代码， [SageMaker SDK](https://github.com/aws/sagemaker-python-sdk) 中的[估计器](http://sagemaker.readthedocs.io/en/latest/estimators.html)对象(或其内置算法、张量流等的子类)。)允许您将模型部署到 HTTPS **端点**并运行预测测试。不需要写任何 app。此外，技术指标可在 [CloudWatch](https://docs.aws.amazon.com/sagemaker/latest/dg/monitoring-cloudwatch.html) 中获得。

> 如果你对负载测试和确定端点大小感兴趣，这篇不错的 [AWS 博客文章](https://aws.amazon.com/blogs/machine-learning/load-test-and-optimize-an-amazon-sagemaker-endpoint-using-automatic-scaling/)将向你展示如何去做。

我不会详述这个问题:我在以前的帖子中已经多次提到过，你也会在 sage maker[notebook collection](https://github.com/awslabs/amazon-sagemaker-examples%24)中找到很多例子。

#### 蓝绿色部署

这种经过验证的[部署技术](https://martinfowler.com/bliki/BlueGreenDeployment.html)需要**两个相同的环境**:

*   运行**版本 n** 的实时生产环境(“蓝色”)，
*   运行**版本 n+1** 的该环境的精确副本(“绿色”)。

首先，你**在绿色环境下运行测试**,**监控**技术和业务指标，检查一切是否正确。如果是，那么你可以**将流量**切换到绿色环境……然后再次检查。如果出现问题，您可以立即将**切换回**蓝色环境并进行调查。如果一切正常，可以**删除**蓝色环境。

为了使这个过程对客户端应用程序完全透明，一个中间人——位于客户端和环境之间——负责**实现交换机**:常见的选择包括负载平衡器、DNS 等。这是它看起来的样子。

[![](img/05d9e9f7ed62c2a7ea93bb4b187c6d15.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XWzibPuX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AWGLxrDadcyWmSzC608wKhg.png)T3】蓝绿部署

#### 蓝绿色部署，SageMaker 道

SageMaker 的 [AWS SDK 以](https://boto3.readthedocs.io/en/latest/reference/services/sagemaker.html) [**端点配置**](https://docs.aws.amazon.com/sagemaker/latest/dg/API_CreateEndpointConfig.html) 的形式提供了我们需要的中间人。这个资源让我们可以将**几个型号**连接到同一个端点，具有不同的重量和不同的实例配置(又名**生产变体**)。在端点的生命周期中，可以随时更新设置。

事实上，人们可以把一个端点想象成一种特殊类型的负载平衡器，使用加权循环法向托管不同模型的实例池发送预测请求。下面是用[*CreateEndpointConfig*](https://docs.aws.amazon.com/sagemaker/latest/dg/API_CreateEndpointConfig.html)API 设置一个所需的信息。

```
"[ProductionVariants](https://docs.aws.amazon.com/sagemaker/latest/dg/API_CreateEndpointConfig.html#SageMaker-CreateEndpointConfig-request-ProductionVariants)": [ 
      { 
         "[InitialInstanceCount](https://docs.aws.amazon.com/sagemaker/latest/dg/API_ProductionVariant.html#SageMaker-Type-ProductionVariant-InitialInstanceCount)": _number_,
         "[InitialVariantWeight](https://docs.aws.amazon.com/sagemaker/latest/dg/API_ProductionVariant.html#SageMaker-Type-ProductionVariant-InitialVariantWeight)": _number_,
         "[InstanceType](https://docs.aws.amazon.com/sagemaker/latest/dg/API_ProductionVariant.html#SageMaker-Type-ProductionVariant-InstanceType)": "_string_",
         "[ModelName](https://docs.aws.amazon.com/sagemaker/latest/dg/API_ProductionVariant.html#SageMaker-Type-ProductionVariant-ModelName)": "_string_",
         "[VariantName](https://docs.aws.amazon.com/sagemaker/latest/dg/API_ProductionVariant.html#SageMaker-Type-ProductionVariant-VariantName)": "_string_"
      }
   ] 
```

实施蓝绿色部署现在是这样的:

*   创建一个**新端点配置**，保存现有实时模型和新模型的**生产变量**。
*   用新的端点配置([*update endpoint*](https://docs.aws.amazon.com/sagemaker/latest/dg/API_UpdateEndpoint.html)API)更新**现有的活动端点**:sage maker 为新的生产变体创建所需的基础设施，并更新权重**而无需任何停机时间。**
*   **将流量**切换到新模型([*updateendpointweightandpacities*](https://docs.aws.amazon.com/sagemaker/latest/dg/API_UpdateEndpointWeightsAndCapacities.html)API)，
*   创建一个新的端点配置，仅保存新的生产变体并将其应用于端点:SageMaker 终止了先前生产变体的基础设施。

这是它看起来的样子。

[![](img/894b8489031684279fbc995b6666d3b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iAGhsKuN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AOmCCeJXV7KD5R5LC7qfQUQ.png) 

<figcaption>蓝绿色部署与单一 SageMaker 端点</figcaption>

#### **金丝雀测试**

金丝雀测试让你通过首先为你的**用户**的一小部分部署一个新版本来验证一个具有最小风险的新版本:其他所有人都继续使用以前的版本。这种用户划分可以通过多种方式实现:随机、地理定位、特定用户列表等。一旦你对这个版本感到满意，你就可以逐步把它推广到**所有用户**。

这需要“**粘性**”:在测试期间，指定用户必须被路由到运行**新版本**的服务器。这可以通过为这些用户设置特定的 cookie 来实现，允许 web 应用程序识别他们并将他们的流量发送到适当的服务器。

您可以在应用程序本身或专用的 web 服务中实现这个逻辑。后者将负责接收预测请求并调用适当的端点。

这看起来像是额外的工作，但是无论如何您都需要一个 web 服务来进行数据预处理(标准化、在预测请求中注入额外的数据，等等)。)和后期处理(过滤预测结果、日志记录等)。).Lambda 似乎是一种很好的方式:易于部署、易于扩展、内置高可用性等。:这里有一个用 AWS Chalice 实现的[例子](https://medium.com/@julsimon/using-chalice-to-serve-sagemaker-predictions-a2015c02b033)。

这是有两个端点的情况。

[![](img/84ec0f737b27919626fe89fad4977850.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h2pk3KsH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AxCtJrB5KeBb9akLDRCjoug.png) 

<figcaption>使用一个“switch”web 服务和两个单模端点进行金丝雀测试。</figcaption>

一旦我们对新模型的工作感到满意，我们就可以逐步将其推广到所有用户，相应地扩大和缩小端点。

[![](img/12a36c7e8494169d875ab7b684fa9c75.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DpfWpcNb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ay896vuwRBZI4ccJOVCU4DA.png) 

<figcaption>逐渐将所有用户切换到新机型。</figcaption>

#### A/B 测试

A/B 测试是关于**比较同一功能**的不同版本的性能，同时监控一个高层次的**指标**(例如点击率、转换率等。).在这种情况下，这意味着对不同的用户使用不同的模型进行预测，并分析结果。

从技术上来说，A/B 测试类似于金丝雀测试，有更大的用户群和更长的时间跨度(几天甚至几周)。粘性是必不可少的，上面提到的技术肯定会起作用:构建用户桶，将它们粘贴到不同的端点并记录结果。

正如您所看到的，将多个模型部署到同一个端点的能力是验证和测试的一个重要需求。让我们看看这是如何工作的。

### 部署多个模型到同一个端点

想象一下，我们想要使用不同的超参数来比较使用用于图像分类的内置算法训练的不同模型。

这些是我们需要采取的步骤(Gitlab 上的[完整笔记本](https://gitlab.com/juliensimon/dlnotebooks/blob/master/sagemaker/05-Image-classification-two-models.ipynb)):

1.  火车模型 a。
2.  火车模型 b。
3.  创建模型 A 和 B，即在 SageMaker 中注册它们。
4.  创建具有两个生产变量的端点配置。
5.  创建端点。
6.  发送流量，查看 CloudWatch 指标。

我们已经在[上一篇](https://medium.com/@julsimon/image-classification-on-amazon-sagemaker-9b66193c8b54)文章中训练过这个算法，所以我就不赘述了:简而言之，我们只是用不同的学习速率训练两个模型。

#### 创建端点配置

这就是我们定义**两个生产变量**的地方:一个用于型号 A，一个用于型号 b。首先，我们将为它们分配**相等的权重**，以便使流量对半平衡。我们还将使用**相同的实例配置**。