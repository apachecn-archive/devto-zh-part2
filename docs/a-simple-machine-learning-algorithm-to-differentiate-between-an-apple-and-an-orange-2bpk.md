# 区分苹果和橙子的简单机器学习算法

> 原文：<https://dev.to/educationecosystem/a-simple-machine-learning-algorithm-to-differentiate-between-an-apple-and-an-orange-2bpk>

(本文原载于 [LiveEdu 博客](http://blog.liveedu.tv/a-simple-machine-learning-algorithm-to-differentiate-between-an-apple-and-an-orange/))。

机器学习(Machine learning，缩写为 ML)是计算机科学的一个领域，它赋予应用程序从示例中自动学习并从该体验中改进的能力，而不依赖于硬编码的规则。

通过机器学习，计算机可以识别隐藏的见解，并根据提供的数据示例做出准确的预测——只需最少的人工干预。

例如，拥有五年以上 ML 经验的 AndreyBu 创建了一个可以预测股票市场数据的模型。你可以在这里观看和学习他的项目。

在这篇文章中，我们将说明如何创建一个简单的机器学习算法，可以区分苹果和橙子。

如果没有机器学习，找到这样一个问题的解决方案可能需要编写几行代码，这可能不会产生预期的结果。ML 简化了这个过程。

简单来说，我们将创建一个机器学习算法，在给出一些例子后，它可以学习两种水果之间的差异，并相应地做出预测。

## 我们开始吧

我们将使用 [Anaconda](https://anaconda.org/anaconda/python) 开源 Python 发行版。它附带了 Scikit-learn，这是一个机器学习库，我们将使用它来实现我们的算法。

在这个例子中，我们将使用监督机器学习算法，该算法利用已知数据集(称为训练数据集)来预测未来事件。

由输入数据和输出值组成的训练数据集从提供的示例中学习，并利用经验来区分两种结果。

以下是监督学习方法的一般流程:

*   收集培训数据
*   训练分类器
*   做预测

让我们来谈谈每一个步骤。

## 1。收集培训数据

训练数据是苹果和橙子的例子，我们将根据它们的差异进行分类。

下表使用两种水果的特征来区分它们:

[![two fruits features](img/900d5d95d589f6ef516d70c83dda4a86.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--l9ytdXS8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ofiqqm5x6v0tz7cz66gs.png)

正如你在上面看到的，水果是根据它们的重量和质地来区分的。

每一行的最后一列就是机器学习中所谓的*标签*。在这种情况下，标签可以是桔子或苹果。

表格中的每一行被称为一个*数据点*。整个表格被称为*训练数据*。

值得注意的是，结果的准确性将取决于训练数据中提供的示例数量。

现在，让我们使用一些 Python 代码来展示表中发生了什么。

我们将使用两个变量:*特征*(前两列中的数据)和*标签*(最后一列中的数据)。

换句话说，要素是输入数据，标注是输出值。

代码如下:

```
features = [[155, "rough"], [180, "rough"], [135, "smooth"], [110, "smooth"]] 
labels = ["orange", "orange", "apple", "apple"] 
```

接下来，因为 Scikit-learn 需要数字特征，所以让我们通过将 rough 定义为 *0* 并将 smooth 定义为 *1* 来将字符串转换为整数。

让我们对橙子和苹果做同样的事情，分别给它们整数值 *1* 和 *0* 。

这是我们的新代码:

```
features = [[155, 0], [180, 0], [135, 1], [110, 1]]  
labels = [1, 1, 0, 0] 
```

## 2。训练分类器

在 ML 中，分类是一种监督学习技术，通过这种技术，算法从示例数据中学习，然后利用经验对新观察结果进行分类。

现在，我们将使用示例数据来训练我们的分类器，以进行新的观察并对它们进行分类。在你的机器学习问题中，有几种类型的分类器可以使用。

在这种情况下，为了简单起见，我们将使用决策树，它可以使用从数据特征中推导出的决策规则来学习并做出适当的预测。

下面是导入决策树分类器并将其添加到我们的项目中的代码:

```
classifier = tree.DecisionTreeClassifier() 
```

将分类器添加到我们的项目后，我们需要使用学习算法来训练它；否则，它可能无法区分苹果和桔子。

学习算法识别训练数据中的模式，并做出适当的结论。

例如，它可能认识到苹果在质地上往往更光滑；因此，它将制定一个规则，倾向于将任何光滑的水果识别为苹果。

在 Scikit-learn 中，训练算法被称为 Fit(可以粗略地解释为“在数据中寻找模式”。).

下面的代码将它添加到我们的项目中:

```
classifier = classifier.fit(features, labels) 
```

## 3。做预测

对分类器进行一些训练后的最后一步是测试它，看看它是否能对新水果进行分类。我们将使用*预测*函数来进行预测。

假设新果光滑，重 120 克。请记住，我们将 smooth 表示为 *1* 。

并且，由于权重不是很高，很可能是一个苹果 *(0)* 。

再者，顺滑是苹果的一个特点。

让我们看看我们的 ML 算法能否做出这样的预测:

```
print (classifier.predict([[120, 1]])) 
```

有用！

输出是我们预期的:0(苹果)。

## 包装完毕

以下是该项目的完整代码:

```
from sklearn import tree
 # Gathering training data
# features = [[155, "rough"], [180, "rough"], [135, "smooth"], [110, "smooth"]]  # Input to classifier features = [[155, 0], [180, 0], [135, 1], [110, 1]]  # scikit-learn requires real-valued features
# labels = ["orange", "orange", "apple", "apple"]  # output values labels = [1, 1, 0, 0]
 # Training classifier classifier = tree.DecisionTreeClassifier()  # using decision tree classifier classifier = classifier.fit(features, labels)  # Find patterns in data
 # Making predictions print (classifier.predict([[120, 1]]))
# Output is 0 for apple 
```

用机器学习编程并不难。为了掌握如何在您的应用程序中使用它，您需要理解一些重要的概念。

因此，为了增加你的技能，你可以使用来自 [LiveEdu](https://www.liveedu.tv/projects/premium/artificial-intelligence/) 的实际项目，了解专家如何构建真正改变世界的机器学习应用程序。

快乐学习机器学习！