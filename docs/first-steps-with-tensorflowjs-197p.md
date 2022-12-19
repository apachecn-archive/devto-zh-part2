# 使用 TensorFlow.js 的第一步

> 原文：<https://dev.to/aralroca/first-steps-with-tensorflowjs-197p>

*原博文:
【https://aralroca.com/blog/first-steps-with-tensorflowjs】T2*

我想做更多的文章来解释一点关于所有机器学习和深度学习的基础知识。我是这方面的初学者，但我想很快解释这些概念，以创建一些有趣的人工智能模型。然而，我们不需要深入了解机器学习来使用一些现有的模型。我们可以使用一些库，如 Keras，Tensorflow 或 TensorFlow.js。我们将在这里看到如何创建基本的人工智能模型，并使用 TensorFlow.js 使用更复杂的模型。虽然这不需要很深的知识，但我们将解释几个概念。

## 什么是模型？

或者一个更好的问题应该是:“现实是什么？”。是的，这个问题很难回答...为了理解它，我们需要简化它！表示这种简化的“现实”的一部分的方法是使用模型。所以；有无限种模型:世界地图，图表等。 [![lonely-planet-698649-unsplash](../Images/d916e351d24864640299dcfd6a08a30f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BvcEQ9bp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aralroca.files.wordpress.com/2018/08/lonely-planet-698649-unsplash.jpg%3Fw%3D1024) 没有机器的帮助，我们更容易理解可以使用的模型。例如，如果我们想要做一个模型来表示巴塞罗那房子的价格，关于房子的大小:首先，我们可以收集一些数据:

| 房间数量 | 价格 |
| three | 131.000€ |
| three | 125.000€ |
| four | 235.000€ |
| four | 265.000€ |
| five | 535.000€ |

然后，我们在 2D 图上显示这些数据，每个参数(价格、房间)一个维度:

[![ezgif-2-a1e0e81df8](../Images/84f01a97e7ad2bcb486b37fa2c930b7b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gQOct3PU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://aralroca.files.wordpress.com/2018/08/ezgif-2-a1e0e81df8.gif)

和...瞧啊。我们现在可以画一条线，并开始预测 6，7 或更多房间的房子的一些价格。这个模型被称为线性回归，它是机器学习世界中最简单的模型之一。当然这个模型还不够好:

1.  只有 5 个例子，所以不够可靠。
2.  只有两个参数(价格、房间)，但还有更多因素会影响价格:地区、房龄等。

对于第一个问题，我们可以通过添加更多的示例来处理，例如 1.000.000 个示例而不是 5 个。对于第二个问题，我们可以增加更多的维度...对吗？用 2D 圆图我们可以理解数据并画线，而在 3D 维度我们也可以用平面: [![](../Images/877c1e15eea48c8b5756394d111287ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zlmNTW6K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aralroca.files.wordpress.com/2018/08/wtwk9vvjdsqblhiwdg2wdaze51520496527_kc-1.jpeg) 但是，如何处理 3D 以上的呢？4D 还是 1000000D？我们的大脑无法在图表上想象这个，但是...好消息！我们可以使用数学和计算三维以上的超平面，神经网络是一个很好的工具！顺便说一句，我有好消息告诉你；使用 TensorFlow.js，您不需要成为数学专家。

## 什么是神经网络？

在了解什么是神经网络之前，我们需要知道什么是神经元。一个神经元，在现实世界中看起来类似于这样: [![neuron.gif](../Images/871fcd61f097c86b46b60a09443a6018.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--voaXS4lj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://aralroca.files.wordpress.com/2018/08/neuron.gif) 一个神经元最重要的部分是:

*   **树突**:是数据的输入。
*   **轴突**:是输出。
*   **突触**(不在图片中):这是一种允许一个神经元与另一个神经元进行通信的结构。它负责在轴突的神经末梢和附近神经元的树突之间传递电信号。这些突触是学习的关键，因为它们根据用途增加或减少电活动。

机器学习中的一个神经元(简体): [![Neuron 2.png](../Images/0769bac7b9b38f84a1cb164286799360.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YzkufehG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aralroca.files.wordpress.com/2018/08/neuron-2.png)

*   **输入**:输入的参数。
*   **权重**:像突触一样，它们的活动增加或减少来调整神经元，以便建立更好的线性回归。
*   **线性函数**:每个神经元就像一个线性回归函数，所以对于一个线性回归模型，我们只需要一个神经元！
*   **激活函数**:我们可以应用一些激活函数将输出从一个标量变为另一个非线性函数。越普通；西格蒙德，RELU 和唐。
*   **输出**:应用激活功能后的计算输出。

激活函数的用法非常有用，它是神经网络的力量。没有任何激活功能，就不可能有智能神经元网络。原因是，尽管你的网络中有多个神经元，但神经网络的输出总是线性回归。我们需要某种机制将这种单独的线性回归变形为非线性的，以解决非线性问题。借助激活函数，我们可以将这些线性函数转换成非线性函数: [![](../Images/454f10bdf2c46a62ddc34fa7cf9e638f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VQn-RGXj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aralroca.files.wordpress.com/2018/08/neural-network.png%3Fw%3D640)

## 训练一个模特

在我们的图表上画一条线，就像 2D 线性回归的例子一样，足以让我们开始预测新的数据。尽管如此，“深度学习”的想法是我们的神经网络学会写这一行。对于一条简单的线，我们可以使用只有一个神经元的非常简单的神经网络，但是对于另一个模型，我们可能想要做更复杂的事情，例如对两组数据进行分类。在这种情况下，“培训”将学习如何绘制这样的东西: [![classification.png](../Images/47c842e3857e9e64ce55efe58bd3691c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nHEJeHM9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aralroca.files.wordpress.com/2018/08/classification.png) 记住，这并不复杂，因为它在 2D。每个模型都是一个世界，但是“训练”的概念在所有模型中都非常相似。第一步是画一条随机线，并在迭代算法中改进它，在每次迭代中修正误差。这种优化算法有梯度下降之名(还有更精密的算法如 SGD 或 ADAM，概念相同)。为了理解梯度下降，我们需要知道每个算法(线性回归，逻辑回归等。)具有不同的成本函数来测量该误差。代价函数总是收敛于某一点，并且可以是凸函数和非凸函数。最低收敛点位于 0%误差处。我们的目标是达到这一点。 [![convex-vs-nonconvex.png](../Images/f1fc3b8b577d63dc06934ae624dcde76.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--02CgztKQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aralroca.files.wordpress.com/2018/08/convex-vs-nonconvex.png) 当我们用梯度下降算法工作时，我们从这个代价函数的某个随机点开始，但是，我们不知道它在哪里！想象你在山上，完全失明，你需要一步一步地走下去，到最低点。如果地面不规则(如非凸函数)，下降会更复杂。 [![mohamed-nohassi-345271-unsplash.jpg](../Images/40f455edcbb0467a202ad0073a9db53f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NAKmPlKm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aralroca.files.wordpress.com/2018/08/mohamed-nohassi-345271-unsplash.jpg) 我不打算深入讲解梯度下降算法。请记住，这是一种优化算法，用于训练人工智能模型，以最小化预测误差。这种算法需要时间和 GPU 进行矩阵乘法。这个收敛点通常在第一次执行时很难达到，所以我们需要修正一些超参数，比如学习率(下山的步长)或者增加一些正则化。在梯度下降的迭代之后，当误差接近 0%时，我们得到了更接近收敛点的点。此时，我们已经创建了模型，并准备开始预测！ [![neural-network.gif](../Images/d64f46c0519248aae0388be39d99d9de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3gvATseP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://aralroca.files.wordpress.com/2018/08/neural-network.gif)

## 用 TensorFlow.js 训练模型

TensorFlow.js 为我们提供了一种创建神经网络的简单方法。首先，我们将使用方法 trainModel 创建一个 LinearModel 类。对于这种模型，我们将使用序列模型。顺序模型是一层的输出是下一层的输入的任何模型，即当模型拓扑是层的简单“堆栈”时，没有分支或跳跃。在方法 trainModel 中，我们将定义各层(我们将只使用一层，因为它对于线性回归问题来说已经足够了):

```
import * as tf from '@tensorflow/tfjs';

/**
* Linear model class
*/
export default class LinearModel {
  /**
 * Train model
 */
  async trainModel(xs, ys){
    const layers = tf.layers.dense({
      units: 1, // Dimensionality of the output space
      inputShape: [1], // Only one param
    });
    const lossAndOptimizer = {
      loss: 'meanSquaredError',
      optimizer: 'sgd', // Stochastic gradient descent
    };

    this.linearModel = tf.sequential();
    this.linearModel.add(layers); // Add the layer
    this.linearModel.compile(lossAndOptimizer);

    // Start the model training!
    await this.linearModel.fit(
      tf.tensor1d(xs),
      tf.tensor1d(ys),
    );
  }

  ...more
}

```

要使用此类:

```
const model = new LinearModel();

// xs and ys -> array of numbers (x-axis and y-axis)
await model.trainModel(xs, ys);

```

经过这次训练，我们准备开始预测了！

## 用 TensorFlow.js 预测

正常预测是比较容易的部分！训练模型需要定义一些超参数...但是，预测是如此简单。我们将把下一个方法写入 LinearRegressor 类:

```
import * as tf from '@tensorflow/tfjs';

export default class LinearModel {
  ...trainingCode

  predict(value){
    return Array.from(
      this.linearModel
      .predict(tf.tensor2d([value], [1, 1]))
      .dataSync()
    )
  }
}

```

现在，我们可以在代码中使用预测方法:

```
const prediction = model.predict(500); // Predict for the number 500
console.log(prediction) // => 420.423

```

[![linear-model](../Images/d97c395232745a78ccd6f2cda43c24c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1MvQcd_0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://aralroca.files.wordpress.com/2018/08/linear-model.gif) 你可以在这里玩代码:

*   [https://stackblitz.com/edit/linearmodel-tensorflowjs-react](https://stackblitz.com/edit/linearmodel-tensorflowjs-react)

## 使用 TensorFlow.js 的预训练模型

学习创建模型是最难的部分；标准化用于训练的数据，正确地决定所有超参数，等等。如果你是这方面的初学者(和我一样)，想玩一些模型，可以用预先训练好的模型。TensorFlow.js 可以使用许多预先训练的模型。此外，您可以导入使用 TensorFlow 或 Keras 创建的外部模型。例如，您可以将 [posenet](https://github.com/tensorflow/tfjs-models/tree/master/posenet) 模型(实时人体姿势估计)用于有趣的项目: [![posenet](../Images/e017adc8638bc5e4eb98aef19433bd40.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uA73W6BN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://aralroca.files.wordpress.com/2018/08/posenet.gif) 📕代码:[https://github.com/aralroca/posenet-d3](https://github.com/aralroca/posenet-d3)非常好用:

```
import * as posenet from '@tensorflow-models/posenet';

// Constants
const imageScaleFactor = 0.5;
const outputStride = 16;
const flipHorizontal = true;
const weight = 0.5;

// Load the model
const net = await posenet.load(weight);

// Do predictions
const poses = await net
      .estimateSinglePose(
          imageElement, 
          imageScaleFactor, 
          flipHorizontal, 
          outputStride
      );

```

**poses** 变量是这个 JSON:

```
{
  "score": 0.32371445304906,
  "keypoints": [
    {
      "position": {
        "y": 76.291801452637,
        "x": 253.36747741699
      },
      "part": "nose",
      "score": 0.99539834260941
    },
    {
      "position": {
        "y": 71.10383605957,
        "x": 253.54365539551
      },
      "part": "leftEye",
      "score": 0.98781454563141
    },
    // ...And for: rightEye, leftEar, rightEar, leftShoulder, rightShoulder
    // leftElbow, rightElbow, leftWrist, rightWrist, leftHip, rightHip,
    // leftKnee, rightKnee, leftAnkle, rightAnkle
  ]
}

```

想象一下，仅仅用这个模型你就能开发出多少搞笑的项目！[![demo.gif](../Images/f2ee0e867437da23fcfa556e96e9f650.png)T2】📕代号:](https://res.cloudinary.com/practicaldev/image/fetch/s--XY0xEcim--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://aralroca.files.wordpress.com/2018/08/demo.gif)[https://github.com/aralroca/fishFollow-posenet-tfjs](https://github.com/aralroca/fishFollow-posenet-tfjs)

## 从 Keras 导入模型

我们可以将外部模型导入 TensorFlow.js，在这个例子中，我们将使用 Keras 模型进行数字识别(h5 文件格式)。为此，我们需要 [**tfjs_converter**](https://github.com/tensorflow/tfjs-converter) 。

```
pip install tensorflowjs

```

然后，使用转换器:

```
tensorflowjs_converter --input_format keras keras/cnn.h5 src/assets

```

最后，您可以将模型导入到您的 JS 代码中了！

```
// Load model
const model = await tf.loadModel('./assets/model.json');

// Prepare image
let img = tf.fromPixels(imageData, 1);
img = img.reshape([1, 28, 28, 1]);
img = tf.cast(img, 'float32');

// Predict
const output = model.predict(img);

```

几行代码就足以享受从 Keras 到我们的 JS 代码的数字识别模型。当然，现在我们可以在这段代码中添加更多的逻辑来做一些更有用的事情，比如用画布画一个数字，然后捕捉这个图像来预测这个数字。[![](../Images/b8a3fae4877bcb96a57d3425d7a7adb1.png)T2】📕代号:](https://res.cloudinary.com/practicaldev/image/fetch/s--G44obCJy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://aralroca.files.wordpress.com/2018/08/mnist.gif)[https://github.com/aralroca/MNIST_React_TensorFlowJS](https://github.com/aralroca/MNIST_React_TensorFlowJS)

## 为什么在浏览器里？

根据设备的不同，浏览器中的训练模型可能非常低效。即使 TensorFlow.js 利用 WebGL 在幕后训练模型，也比 TensorFlow Python 慢 1.5-2 倍。然而，在 TensorFlow.js 之前，如果没有 API 交互，就无法在浏览器中直接使用机器学习模型。现在，我们可以在应用程序中离线训练和使用模型。此外，预测要快得多，因为它们不需要向服务器发出请求。另一个好处是服务器成本低，因为现在所有这些计算都在客户端进行。[![](../Images/09c4d2d825e711af864791e8d3408244.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qwzsCApp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aralroca.files.wordpress.com/2018/08/money.jpeg%3Fw%3D640)

## 结论

*   模型是一种表示现实简化部分的方式，我们可以用它来预测事物。
*   创建模型的一个好方法是使用神经网络。
*   TensorFlow.js 是创建神经网络的一个好而简单的工具。

[![](../Images/d83a45c7381484dc09162c1e95e1fadf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XD1c26yB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aralroca.files.wordpress.com/2018/08/bye.jpeg%3Fw%3D640)

## 参考文献:

*   [https://js.tensorflow.org](https://js.tensorflow.org)
*   [https://en.wikipedia.org/wiki/Scientific_modelling](https://en.wikipedia.org/wiki/Scientific_modelling)
*   [https://www . quant start . com/articles/Supervised-Learning-for-Document-class ification-with-Scikit-Learn](https://www.quantstart.com/articles/Supervised-Learning-for-Document-Classification-with-Scikit-Learn)
*   [https://en.wikipedia.org/wiki/Synapse](https://en.wikipedia.org/wiki/Synapse)
*   [https://medium . com/tensor flow/real-time-human-pose-estimation-in-the-tensor flow-js-7 DD 0 BC 881 CD 5](https://medium.com/tensorflow/real-time-human-pose-estimation-in-the-browser-with-tensorflow-js-7dd0bc881cd5)
*   [https://github . com/tensor flow/tfjs-models/tree/master/pose net](https://github.com/tensorflow/tfjs-models/tree/master/posenet)
*   [https://www.youtube.com/watch?v=Y_XM3Bu-4yc](https://www.youtube.com/watch?v=Y_XM3Bu-4yc)
*   [https://ml 4a . github . io/demos/confusion _ mnist/](https://ml4a.github.io/demos/confusion_mnist/)