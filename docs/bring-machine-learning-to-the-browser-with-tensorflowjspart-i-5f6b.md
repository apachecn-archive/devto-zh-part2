# 使用 TensorFlow.js 将机器学习引入浏览器—第一部分

> 原文：<https://dev.to/vabarbosa/bring-machine-learning-to-the-browser-with-tensorflowjspart-i-5f6b>

> *从[medium.com](https://medium.com/ibm-watson-data-lab/bring-machine-learning-to-the-browser-with-tensorflow-js-part-i-16924457291c)T3】传中*
> 
> ***编辑于 2019 年 3 月 11 日**包含 TensorFlow.js 1.0 中引入的变更。关于 TensorFlow.js 1.0 更新的更多信息可以在[这里](https://gist.github.com/caisq/3fc0beb6597f42d66be806c6692f310d)找到。*

#### 将网络友好格式应用于预训练模型，产生网络应用。

<figure>[![](img/679124cc29c08fd870df62870c0128c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fw1JUf0P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AgA9FhjIFE9h9BbrZ) 

<figcaption>【杂色树叶挂饰】由[克里斯·劳顿](https://unsplash.com/@chrislawton?utm_source=medium&utm_medium=referral)上[下](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

[TensorFlow.js](https://js.tensorflow.org) 为 JavaScript 带来了机器学习及其可能性。它是一个开源库，旨在在浏览器(和 Node.js)中创建、训练和运行机器学习模型。

训练和构建复杂的模型需要大量的资源和时间。一些模型需要大量数据来提供可接受的准确性。并且如果计算量大，可能需要数小时或数天的训练才能完成。因此，您可能不会发现浏览器是构建这种模型的理想环境。

一个更吸引人的用例是导入和运行现有的模型。您在强大、专业的环境中训练模型，然后在浏览器中导入并运行模型，以获得令人印象深刻的用户体验。

### 转换模型

在 TensorFlow.js 中使用预先训练的模型之前，该模型需要是 web 友好格式。为此，TensorFlow.js 提供了 [tensorflowjs_converter](https://github.com/tensorflow/tfjs-converter) 工具。该工具将 TensorFlow 和 Keras 模型转换为所需的 web 友好格式。安装完`tensorflowjs` Python 包后，转换器就可用了。