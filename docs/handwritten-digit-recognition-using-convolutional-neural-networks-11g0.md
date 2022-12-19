# 基于卷积神经网络的手写数字识别

> 原文：<https://dev.to/frosnerd/handwritten-digit-recognition-using-convolutional-neural-networks-11g0>

# 简介

在这篇博文中，我想分享我开发的一个对手写数字图像进行分类的小应用程序，以及在开发它时学到的经验。说到机器学习，过去我主要处理文本数据。图像数据的模式识别对我来说是新的，但我认为这是一个非常有用的技能。

该职位的结构如下。首先，我们将介绍图像分类的概念，以及它与文本分类等其他问题相比的特殊之处。下一节介绍一种机器学习模型，称为卷积神经网络(CNN)，常用于图像分类。第三部分展示了通过 web 界面执行手写数字分类的示例应用程序。我们通过总结主要的发现和想法来结束这篇文章。

该应用程序是用 Scala、HTML、CSS 和 JavaScript 编写的。然而，这些概念也可以转移到其他语言。我还试图将数学细节保持在最低限度，将重点放在必要的信息上，以便读者对所使用的算法有一种直觉。如果你有兴趣更深入地了解这个主题，我建议你看看其他教程、研究论文或书籍。

# 图像分类

机器学习算法期望数据以计算机可以理解的某种数字格式来表示。例如，当使用概率模型时，您的数据必须符合您的模型所使用的分布预期的格式。

作为例子，考虑一个多项式混合模型[1]。要利用这种类型的模型，您需要能够将您的数据转换成计数。在文本中，这可以通过为每个可能的文档中的每个簇的每个可能的单词引入计数变量来实现。这个模型非常简单，对于许多用例都非常有效。然而，它有一个很大的缺点:它丢弃了很多信息，例如文档中的术语共现和位置。

对于图像数据，这个问题甚至更大。虽然您仍然可以通过查看字数来确定一封电子邮件是否是垃圾邮件，但如果只计算具有特定颜色的像素数量，则识别带有猫的图像要困难得多。文本数据是一维的，即一系列术语，而图像至少是二维的，即像素矩阵，并且在像素的空间关系中包含更多的信息。

幸运的是，我们可以使用其他模型来考虑空间信息。一种非常常用的模型类型是[卷积神经网络](http://cs231n.github.io/convolutional-networks/) (CNN)。虽然该领域的研究已经持续了一段时间[2]，但基于 GPU 的训练时代导致了近年来模型性能方面的重大突破[3]。

我们如何在计算机中表现原始图像？计算机图像的最小可寻址元素是一个像素。每个像素都有一个位置和一种颜色。我们可以用不同的形式来表现颜色。一种常用的彩色图像方案是红-蓝-绿(RBG)。如果我们为每个像素保留 24 位，即三种颜色中的每一种保留 8 位，我们可以分别编码 256 种不同色调的红色、蓝色和绿色。将它们组合在一起，我们就可以表现出大约 1600 万种不同的颜色。

为了从我们的代码中访问图像信息，我们可以将像素存储在二维数组中，即矩阵中。虽然有可能在该矩阵的单个坐标内组合所有三个颜色通道，但是仅存储单个数字更有效。这为我们留下了每个通道的矩阵，因此我们可以将灰度图像表示为矩阵，将彩色图像表示为三维张量。下图说明了此过程如何处理 3×3 像素的图像。请注意，在真实图像中，大多数时候颜色是混合的。

[![image pixels](../Images/f53941d551d4e3e73cd933cf53de95be.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sR_Cy-C3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dfqan0qmy6ozmmb3d8f2.png)

现在让我们来看看 CNN 是如何工作的，以及我们如何使用这种图像表示作为基于 CNN 的分类器的输入。

# 卷积神经网络

## 建筑

神经网络是一种机器学习模型，由*个神经元*的连接层组成。一个神经元包含一个数字，即所谓的*激活*。连接被赋予*权重*，该权重描述了连接的神经元的信号强度。

输入数据被送入第一层，在一定程度上激活每个输入神经元。基于权重和一个*激活函数*，网络决定激活下一层的哪些神经元以及激活的强度。这个所谓的*前馈*过程一直持续到输出神经元被激活。神经网络的体系结构对它可以处理的数据及其性能有着巨大的影响。下图说明了一个具有三层的简单神经网络。

[![simple nn](../Images/b76f615e3ab131f254eceec79f074d67.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EBC3DsId--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7j7lzvnj9impxg32vsbt.png)

CNN 是一种特殊类型的神经网络。它们可以分为两个部分:一个*特征学习*部分和一个*分类*部分。每个部分由一层或多层组成。特征学习通常通过组合两种类型的层来完成:*卷积层*和*汇集*层。然后基于通过*密集层*学习到的特征进行分类，密集层也被称为全连接层。此外，还有一个包含图像数据的*输入层*，以及一个包含我们试图预测的不同类别的*输出层*。

下图显示了具有一个卷积层、一个池层和一个密集层的 CNN。任务是预测图像是否描绘了一只猫。输入层和输出层之间的层也被称为*隐藏层*，因为当将模型视为黑盒时，状态并不直接可见。

[![cnn example](../Images/2b2a7aacd982d5c5c80f23230691cb35.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--azSWMe_H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lxmqa1f3uscmj3p5h8xv.png)

考虑单个颜色通道，输入层可以是原始图像矩阵，也可以是经过预处理的图像矩阵，例如经过裁剪、调整大小、颜色值在 0 和 1 之间缩放等等。输出层表示由最后一个隐藏层分配的每个可能类别的权重。在下一小节中，我们想仔细看看不同的隐藏层类型。

## 卷积层

卷积层负责将滤波器与前一层进行卷积。如果你不熟悉二维图像滤波，可以看看来自[机器学习大师](http://machinelearninguru.com/index.php)的[图像滤波](http://machinelearninguru.com/computer_vision/basics/convolution/image_convolution_1.html)帖子。过滤器可以被视为较小的图像，即比输入更小的矩阵，其被应用于输入的一部分。如果图像的部分与过滤器预期的相匹配，输出值将会很高。将滤波器与全部输入进行卷积将产生另一个图像，该图像突出显示输入的某些方面。

让我们看一个例子。下图显示了 Sobel-Feldman 算子[4]的应用，也称为 Sobel 边缘检测滤波器，用于我们的蓝猫。准确地说，我们应用了两个过滤器，一个用于水平边缘，一个用于垂直边缘。然后，我们将两个结果组合起来，以获得显示水平和垂直边缘的图像。滤波器核被描绘在图的中心。

[![cat filter](../Images/bfd324c5abe3b8a2e6a1c5b92a93b7da.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NOEJXSZV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l1pxwr6c17040lk5j7ak.png)

定义卷积层时有不同的配置选项。每个卷积层可以有一个或多个过滤器。卷积层然后将输出每个滤波器的输入的中间表示。过滤器越多，我们的图像特征就越多样化。

除了滤波器内核的数量，我们还可以选择一个*内核大小*。内核大小决定了过滤器的局部性，即在应用过滤器时考虑了多少周围像素。其次，我们需要选择一个*步幅*值。步幅决定了卷积时我们前进了多少像素。步长为 1 将使滤镜在每个像素上移动，而步长为 2 将跳过每隔一个像素。

问题是我们如何选择我们想要使用的过滤器？答案是，我们没有。神经网络的伟大之处在于，它们可以根据训练数据自己学习特征。训练过程将在后面的章节中详细讨论。现在让我们转到第二种类型的特征学习层:池层。

## 汇集图层

应用池层对输入进行下采样。目标是降低模型的计算复杂度并避免过拟合。信息丢失通常不是问题，因为特征的确切位置不如它们之间的关系重要。

池化是通过应用一个特殊的过滤函数来实现的，同时以一种过滤应用不重叠的方式选择内核大小和步幅值。一种常用的技术叫做 *max pooling* 。在最大池中，我们为子采样输出选择子区域的最大值。在下图中，我们可以看到将 2×2 最大池应用于 4×4 输入矩阵的结果。

[![2x2 max pooling](../Images/4cbbd0f6fbc61d551b43fc2a31dc6b20.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HqQ1pa6x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tl5e7lypd3dsbyf61g1o.png)

下图描述了对卷积层的输出进行两次子采样的结果。请注意，子采样减小了图像大小，但我再次放大了图像大小，以直观显示信息的损失。

[![subsampled cat](../Images/f505223a9449c950efb7876f2d48abc4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UM71KWTv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y73knocyw02zehm9cu5b.png)

我们如何使用派生的特征来预测一个类？让我们通过仔细观察密集层是如何工作的来找出答案。

## 密集层

致密层将每一个神经元从上一层连接到下一层。在 CNN 的上下文中，它们形成网络的分类部分。密集层中的神经元学习每个类由哪些特征组成。

密集层在参数拟合方面比卷积层更复杂。具有来自卷积层的 3×3 核的滤波器具有独立于输入神经元数量的 9 个参数。16 个神经元与前一层上的 28×28 个神经元的完全连接层已经具有 28×28×16 = 12，544 个权重。

现在我们更熟悉 CNN 的不同组件，您可能想知道如何找到所有参数的正确值，即密集层中的滤波器核和权重。

## 训练

像所有机器学习算法一样，训练是基于已知类别标签的示例输入来完成的。用随机参数初始化未训练的 CNN。然后，我们可以通过网络输入训练样本，并检查输出神经元的激活情况。基于预期的激活，即与正确类别相关联的神经元的完全激活，而其余的没有激活，我们可以导出捕获网络有多错误的成本函数。

然后我们可以开始调整参数以降低成本。这是从输出神经元开始，调整每一层的参数直到输入层。这个学习过程被称为*反向传播*。我们如何知道增加哪个参数，减少哪个参数，以及减少多少？

我不会在这里讲太多的数学细节，但是你们可能记得微积分，对于一些函数，你可以计算导数，告诉你函数的输出如何变化，给定输入变量的变化。导数表示绘制时函数切线的斜率。如果我们为我们的成本函数计算这个，它将告诉我们每个参数如何影响我们期望的类标签的结果。

由于我们的成本函数不仅有一个输入变量，而且可能有数千个输入变量，(回想一下对于一个小的密集层已经有多少个权重)，我们可以利用所谓的*梯度*。梯度是多元函数导数的推广。准确地说，我们希望使用负梯度，因为我们的目标是降低成本。负梯度将告诉我们需要如何调整网络参数，以更好地对训练样本进行分类。这种方法叫做*梯度下降*。

为我们所有的训练样本计算精确的负梯度在大多数时候是不可行的。但是，我们可以使用一个小技巧:对输入数据进行洗牌，并分组为小批量。然后，我们只在这个小的子集上计算梯度，相应地调整网络的参数，并继续下一批。这个所谓的*随机梯度下降*给出了精确答案的一个足够好的近似值。

但是请记住，通过降低梯度，我们只能提高初始随机参数允许的程度。如果不以完全不同的权重开始，网络可能无法改进，陷入所谓的成本函数的*局部最小值*。有几种技术可以避免陷入局部最小值，但它们也有缺点。

现在我们有了训练好的模型，我们可以输入没有标签的图像，并查看输出来确定正确的类别。接下来我们来看看图像分类的“Hello World”例子，以及我基于它搭建的小 app。

# 手写数字识别

## 数据

图像分类的“Hello World”是一个看似简单，但对手写数字进行分类的重要问题。在修改后的国家标准与技术研究所数据库中，有丰富的培训和测试数据集可以免费在线获得，该数据库被广泛称为 [MNIST 数据库](http://yann.lecun.com/exdb/mnist/)。

每个数字都是一个 28×28 像素的灰度图像。下图显示了每个数字的一些示例图像。

[![mnist](../Images/0ba257566242c96c32e9135baa667d54.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wUUoNDX8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6sm156embzylvs2h41vp.png)

## 应用架构

为了建立一个可以使用和玩的东西，我的目标是建立一个 web 应用程序，允许你画一个数字并对它进行分类。我正在使用 [Deeplearning4j](https://deeplearning4j.org/) (DL4J)来构建、训练、验证和应用这个模型。这是一个用于 JVM 的开源深度学习库。请在下面找到一个小的架构图。

[![application architecture](../Images/0175323a8a5ee8e7be1f8fc74ae19434.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CXhG9fiS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h368re5hbwf5sqnho0if.png)

该应用程序分为两部分:

*   培训和验证
*   预言；预测；预告

培训和验证离线进行。它从一个目录结构中读取数据，该目录结构已经将数据分为训练数据和测试数据，并在其各自的目录中包含各个数字。训练成功后，网络被序列化并保存在文件系统上(`model.zip`)。然后，预测 API 在启动时加载该模型，并使用它来服务来自前端的传入请求。

在我们详细研究各个组件之前，请注意[源代码](https://github.com/FRosner/handwritten)可以在 GitHub 上获得，应用程序是[在线](https://immense-scrubland-99285.herokuapp.com/)并且可以通过 [Heroku](https://www.heroku.com/) 试用。我只使用一个免费层，所以当应用程序第一次使用时，您可能需要等待一段时间，因为它会延迟启动服务器。

## 前端

前端是一个简单的 HTML 5 画布加上一点 JavaScript 来将数据发送到后端。它的灵感主要来自于[威廉·马隆](http://www.williammalone.com/about/)的[用 HTML 5 画布和 JavaScript](http://www.williammalone.com/articles/create-html5-canvas-javascript-drawing-app/#demo-simple) 教程创建一个绘图应用。如果你现在不能访问[现场版](https://immense-scrubland-99285.herokuapp.com/)，你可以看看下面的前端截图。

[![frontend 4](../Images/5c1a7f181cf9f07f4b292c53645a4c99.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---RyDk1GN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ddx8hyz5ukzlk1a4koa7.png)

它具有一个绘图画布、一个将画布内容发送到后端的按钮、一个清除画布的按钮和一个分类结果的输出区域。 [`index.html`](https://github.com/FRosner/handwritten/blob/2c40da1526fa312d603b0a5c74b35c79d7407818/src/main/resources/static/index.html) 并不是很复杂。下面是使用的 HTML 元素:

```
<body>
    <div id="canvasDiv"></div>
    <div id="controls">
        <button id="predictButton" type="button">Predict</button>
        <button id="clearCanvasButton" type="button">Clear</button>
    </div>
    <div id="predictionResult">
    </div>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

然后我们添加一些 CSS ( [`app.css`](https://github.com/FRosner/handwritten/blob/2c40da1526fa312d603b0a5c74b35c79d7407818/src/main/resources/static/app.css) )到混合中，让它看起来不那么难看。JavaScript 代码( [`app.js`](https://github.com/FRosner/handwritten/blob/2c40da1526fa312d603b0a5c74b35c79d7407818/src/main/resources/static/app.js) )是基本的 [jQuery](https://jquery.com/) ，没什么特别的，非常典型。它首先构建画布并定义绘图函数。预测是通过将画布内容发送到后端来完成的。一旦结果到达，我们就在输出中显示它`div`。

```
$('#predictButton').mousedown(function(e) {
  canvas.toBlob(function(d) {
  var fd = new FormData();
  fd.append('image', d)
    $.ajax({
      type: "POST",
      url: "predict",
      data: fd,
      contentType: false,
      processData: false
    }).done(function(o) {
      $('#predictionResult').text(o)
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 后端

后端( [`PredictAPI.scala`](https://github.com/FRosner/handwritten/blob/2c40da1526fa312d603b0a5c74b35c79d7407818/src/main/scala/de/frosner/PredictAPI.scala) )是一个小型的 [Akka HTTP](https://doc.akka.io/docs/akka-http/current/) web 服务器。启动时，我们从磁盘加载模型。我们必须将访问封装在一个[同步块](https://github.com/FRosner/handwritten/blob/2c40da1526fa312d603b0a5c74b35c79d7407818/src/main/scala/de/frosner/SynchronizedClassifier.scala#L9)中，因为 DL4J 的默认模型实现不是线程安全的。

```
val model = new SynchronizedClassifier(
  ModelSerializer.restoreMultiLayerNetwork("model.zip")
) 
```

Enter fullscreen mode Exit fullscreen mode

静态文件有一条路径，即`index.html`、`app.js`、`app.css`，还有一条路径用于接收数字图像进行预测。

```
val route =
  path("") {
    getFromResource("static/index.html")
  } ~
  pathPrefix("static") {
    getFromResourceDirectory("static")
  } ~
  path("predict") {
    fileUpload("image") {
      case (fileInfo, fileStream) =>
        val in = fileStream.runWith(StreamConverters.asInputStream(3.seconds))
        val img = invert(MnistLoader.fromStream(in))
        complete(model.predict(img).toString)
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

对于每一个输入的图像，我们必须应用一些基本的变换，比如调整大小和缩放，这些都是在 [`MnistLoad.fromStream`](https://github.com/FRosner/handwritten/blob/2c40da1526fa312d603b0a5c74b35c79d7407818/src/main/scala/de/frosner/MnistLoader.scala#L51) 方法中实现的。当网络被训练来分类黑色背景上的白色数字时，我们也反转图像。

## 模型

使用的模型是一个七层 CNN，主要受 CNN 的 [DL4J 代码示例](https://deeplearning4j.org/convolutionalnetwork)的启发。隐藏层是两对卷积池层，以及一个密集层。它是用 64 个图像的批次使用随机梯度下降来训练的。模型的测试准确率为 98%。

培训和验证过程在 [`TrainMain.scala`](https://github.com/FRosner/handwritten/blob/2c40da1526fa312d603b0a5c74b35c79d7407818/src/main/scala/de/frosner/TrainMain.scala) 中实施。在那里，您还可以找到确切的模型配置。在这一点上，我不想涉及太多的细节，但是如果您对模型架构有任何问题，请随时发表评论。

## 与 Heroku 一起部署

我选择用 Heroku 部署应用程序，因为它允许快速公开部署应用程序，有一个自由层，并且很好地集成到开发工作流中。我正在使用 Heroku CLI。

对于用 SBT 构建的 Scala 项目，Heroku 将执行`sbt stage`。这将产生一个应用程序的二进制工件以及所有库依赖项。 [`Procfile`](https://github.com/FRosner/handwritten/blob/2c40da1526fa312d603b0a5c74b35c79d7407818/Procfile) 指定如何启动 app。下面是部署到 Heroku 所需的命令。

*   `heroku login`(登录您的 Heroku 账户)
*   `heroku create`(初始化`heroku`遥控器)
*   `git push heroku master`(推送更改，触发构建)
*   `heroku open`(在浏览器中打开应用程序 URL)

## 问题

如果您尝试该应用程序，您可能会遇到一些奇怪的输出。事实上，即使模型有 98%的准确率，也有多个问题可能导致你所画数字的错误分类。

一个因素是图像没有居中。虽然卷积层和通过池化的二次采样的组合有所帮助，但我怀疑将所有数字移动和调整到画布的中心会有助于性能。为了获得最佳效果，请尝试在画布的下半部分绘制图像。

此外，训练数据捕捉了在美国常见的某种手写风格。在世界的其他地方，数字 1 由多行组成，而在美国，人们通常把它写成一行。这可能会导致 1，以不同的方式书写，被归类为 7。下图说明了这一点。

[![US 1](../Images/119357794267f5a055e2b12f6a02d4a5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--H9tPkbRO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4c0k7p6kogugsrs9mpje.png)

# 总结

在这篇文章中，我们看到了 CNN 是如何被用来分类图像数据的。使用近似优化技术、子采样和滤波器应用的组合，我们能够训练深度网络，该网络很好地捕捉输入图像的特征。

使用一点 JavaScript，HTML 和 CSS，你可以开发一个前端的图片分类。后端可以使用 Akka HTTP 这样的 HTTP 服务器结合 DL4J 这样的深度学习框架来实现。

我们还看到，只有当真实数据对应于构建模型时使用的训练和测试数据时，真实世界中的分类性能才与测试准确性相匹配。在运行时监控模型性能，定期调整或重新训练模型以保持较高的准确性是至关重要的。

# 参考文献

*   [1]里古斯特，l .，卡佩，o .和芮伟航，f .，2007 年。用于文本聚类的多项式混合模型的推理和评价。信息处理与管理，43 卷 5 期，第 1260-1280 页。
*   [2] LeCun，y .，Bottou，l .，Bengio，y .和 Haffner，p .，1998 年。基于梯度的学习在文档识别中的应用。IEEE 会议录，86(11)，第 2278-2324 页。
*   [3] Ciregan 博士、Meier 大学和 Schmidhuber 律师，2012 年 6 月。用于图像分类的多列深度神经网络。在计算机视觉和模式识别(CVPR)，2012 年 IEEE 会议上(第 3642-3649 页)。IEEE。
*   [4] Sobel，I .，Feldman，g .，用于图像处理的 3×3 各向同性梯度算子，于 1968 年在斯坦福人工智能项目(SAIL)上提出。