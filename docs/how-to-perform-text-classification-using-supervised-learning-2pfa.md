# 如何使用监督学习进行文本分类

> 原文：<https://dev.to/webdva/how-to-perform-text-classification-using-supervised-learning-2pfa>

关于如何使用神经网络进行监督学习以进行文本分类的演练。

# 目标

我们的目标是学习如何制作一个简单的神经网络并操作它。我们希望神经网络结构能够人工学习如何对文本进行分类。这将有助于我们通过观看一个人的表演来了解神经网络。神经网络不会完全由我们构建，因为我们将使用由[neaptic](https://www.npmjs.com/package/neataptic)库提供给我们的功能；在本教程中，我们不会关注神经网络如何运行的内部机制。

## 行动计划

首先，我们需要一种机制来将文本转换为神经网络可以操作的数字数据格式，单词袋向量模型将帮助我们实现这一目标。接下来，我们将使用我们的文本转换机制对特定词汇集进行转换，为神经网络提供训练数据。然后我们将创建神经网络并训练它。最后，我们将使用神经网络执行文本分类。

我们使用的编程语言将是 Javascript。

# 进口

我们将需要`natural`和`neataptic` Node.js 包来实现我们的目标。`natural`包将用于分词和词干化，而`neataptic`包将为我们提供一个神经网络。

```
const natural = require('natural');
const neataptic = require('neataptic'); 
```

Enter fullscreen mode Exit fullscreen mode

使用控制台命令`npm install --save natural neataptic`安装软件包。

请注意，Neataptic 需要 7.6 或更高版本的 Node.js，并且可能需要安装更高版本才能满足其庞大而复杂的依赖关系。

# 为神经网络转换文本

我们需要一种算法来将文本转换成神经网络可以操作的结构。我们将函数命名为 *cleanSentence* ,以表明它将句子转换为神经网络的首选格式。需要提供一个句子(即一个字符串)和一个词汇列表，作为词汇超集。

我们的算法的目标是对单个句子进行标记，对标记句子中的每个单词进行词干提取，然后将文本数据转换为单词袋向量模型。

## 标记化

将句子标记化意味着将句子解构为单词片段或标记。这相当于将一个句子字符串转换成一个由单个单词元素组成的数组。

我们将使用导入的`natural`包来为我们执行令牌化。

```
function cleanSentence(sentence, vocabulary) {
    const naturalTokenizer = new natural.TreebankWordTokenizer();
    sentence = naturalTokenizer.tokenize(sentence);
} 
```

Enter fullscreen mode Exit fullscreen mode

`sentence`现在是一个令牌数组。

## 词干

为了方便起见，把单词的词根或词干抽出来。我们的`sentence`数组中的每个标记都将删除其词法结尾，如果适用的话，产生基本或词干段。

我们也将使用`natural`包来完成这个任务。为了保持一致，它也将帮助我们的单词小写。

```
function cleanSentence(sentence, vocabulary) {
    const naturalTokenizer = new natural.TreebankWordTokenizer();
    sentence = naturalTokenizer.tokenize(sentence);

    // stem each word/token in the sentence array
    sentence = sentence.map(word => natural.LancasterStemmer.stem(word));

    // remove duplicate words
    sentence = Array.from(new Set(sentence));    
} 
```

Enter fullscreen mode Exit fullscreen mode

`sentence`中的所有标记现在都被词干化了。

## 制作词袋向量

神经网络需要连续的数值来操作，而单词不是这样。因此，我们将从我们正在使用的文本中创建一个单词袋向量模型。

单词袋向量模型是一组值，它们映射词汇超集中单词在句子中的出现。它可以记录每个单词在一个句子中映射到一个词汇的出现次数，但我们只想让模型知道某个特定的单词是否出现。

便于理解的图示:

*(word1，word2，word3)* 是词汇集合词， *word1* ， *word2* ， *word3* 。使用词汇集的句子“单词 2 是一个东西”的单词包向量模型看起来像(0，1，0)，因为*单词 2* 是词汇集中唯一出现的单词，并且它是向量中的第二个元素，因此是(0，1，0)。

下面附加的算法，以及完整的函数，为我们的句子创建了一个单词袋向量模型，并在函数的末尾返回它。

```
function cleanSentence(sentence, vocabulary) {
    const naturalTokenizer = new natural.TreebankWordTokenizer();
    sentence = naturalTokenizer.tokenize(sentence);

    sentence = sentence.map(word => natural.LancasterStemmer.stem(word));

    sentence = Array.from(new Set(sentence));

    let bag = [];
    vocabulary = vocabulary.map(word => natural.LancasterStemmer.stem(word)); // stem this set of words too for consistency
    vocabulary.forEach((word) => {
        if (sentence.includes(word))
            bag.push(1);
        else
            bag.push(0);
    });

    return bag;   
} 
```

Enter fullscreen mode Exit fullscreen mode

# 训练文本

单词*困*、*累*、*美食*、*饿*将成为我们的词汇集。

```
const vocabulary = ['sleepy', 'tired', 'food', 'hungry']; 
```

Enter fullscreen mode Exit fullscreen mode

词汇集的单词包向量将是一个映射到一组分类编码的数组。分类编码将是[1，0]表示困倦的意图，带有单词*困倦*和*疲倦*，以及[0，1]表示饥饿的意图，带有单词*食物*和*饥饿*，因为这是我们的监督。

例如，单词袋向量模型 *[1，0，0，0]* 将被分类为困倦意图，因为单词*困倦*是词汇集中的第一个元素，并且由于我们分配的映射，该模型将保证输出*【1，0】*。

下面是一组训练输入输出对，按照 Neataptic 库的要求格式提供给神经网络。

```
const trainingSet = [
    {input: [1, 0, 0, 0], output: [1, 0]},
    {input: [0, 1, 0, 0], output: [1, 0]},
    {input: [0, 0, 1, 0], output: [0, 1]},
    {input: [0, 0, 0, 1], output: [0, 1]}
]; 
```

Enter fullscreen mode Exit fullscreen mode

# 神经网络训练

将选择一种[多层感知器](https://en.wikipedia.org/wiki/Multilayer_perceptron)神经网络类型，因为它代表了神经网络的本质，并且对于我们的学习目的来说简单而有效。

神经网络的构建将要求输入层的大小为 4，因为我们有大小为 4 的词汇词袋向量模型。由于我们有两个分类类，因此输出图层大小将为 2。我们将选择大小为 6 的隐藏层，这是我们的输入层大小和输出层大小的总和。隐藏层的大小和数量可以改变，但这些目前的参数足以满足本教程。

```
const NN = neataptic.architect.Perceptron(4, 6, 2); 
```

Enter fullscreen mode Exit fullscreen mode

必须调整神经网络的附加参数。我们的 Neataptic 库需要一个带有参数的 JSON 对象。

```
NN.train(trainingSet, {
    log: 10,
    iterations: 100000,
    error: 0.00001,
    rate: 0.01
}); 
```

Enter fullscreen mode Exit fullscreen mode

神经网络将根据[成本函数](https://en.wikipedia.org/wiki/Cost_function)的最小化来执行其任务。

`iterations`是我们希望神经网络在没有预先达到目标`error`值的情况下执行的成本函数最小化迭代的最大次数。

`error`值显示了我们希望最小化成本函数的程度。理想情况下，它应该是零，因为这意味着完全没有误差，但是机器能做的最好的事情就是尽可能精确。

`rate`是成本函数在每次迭代中使用的步长值。它与成本函数收敛到其指定目标值`error`的速度有关。

`10`的`log`值仅仅意味着在每十次迭代中，神经网络将在训练期间在控制台中记录其当前状态。

一个例子，当使用`trainingSet`训练神经网络的上述`train`语句被执行时:

```
iteration 11140 error 0.000010074673175227337 rate 0.01
iteration 11150 error 0.000010052491208186209 rate 0.01
iteration 11160 error 0.000010030380692738644 rate 0.01
iteration 11170 error 0.000010008341326644574 rate 0.01 
```

Enter fullscreen mode Exit fullscreen mode

经过一段时间的训练，神经网络现在可用于文本分类操作。

# 神经网络运算

当使用新训练的神经网络时，我们使用我们创建的`cleanSentence`函数将句子转换为神经网络预期的输入格式。神经网络的`activate`函数返回所提供的输入属于每个类的概率。

```
console.log(NN.activate(cleanSentence('sleepy girl', vocabulary)));
console.log(NN.activate(cleanSentence('sleepy and tired girl', vocabulary)));
console.log(NN.activate(cleanSentence('sleepy girl, hungry too! where is food she said', vocabulary))); 
```

Enter fullscreen mode Exit fullscreen mode

上面的语句可能会产生下面的输出，数组中的每个元素都是属于我们的两个类别(困倦和饥饿)的概率:

```
[ 0.9972345487495489, 0.0027823015223758674 ]
[ 0.9993448101567569, 0.0006689189700326538 ]
[ 0.0032375739385209342, 0.9967425328909244 ] 
```

Enter fullscreen mode Exit fullscreen mode

神经网络已经确定，第一个激活语句，即带有句子“瞌睡女孩”的语句，属于瞌睡类，有 99%的可能性是瞌睡，而属于饥饿类，有 0%的可能性是瞌睡。类似地，第二次激活，带有句子“困倦的女孩”的激活，产生了属于困倦类的 99%的概率和属于饥饿类的 0%的概率。

最后一次激活混合了意图类别，产生了 0 到 99 的嗜睡-饥饿分类比率，而不是更分数的结果，如 33 到 66，因为我们没有训练神经网络这样做。如果我们提供并监督更多的训练样本，那么神经网络将学习额外的现象，因为这是监督学习的本质。