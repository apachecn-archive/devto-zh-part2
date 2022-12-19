# 介绍我的新宠物项目:我应该 HODL 吗？

> 原文：<https://dev.to/preslavrachev/introducing-my-new-pet-project-should-i-hodl-594j>

我从去年开始对加密的痴迷可能已经消退了一点，但我仍然是一个长期投资者和分权的信徒。此外，它预测了加密市场的下一步行动，这让我的一些旧机器学习实验尘埃落定，并为我成为数据科学家的使命提供了新的动力。虽然人们几乎听不到我谈论这个或那个硬币，但我仍然在积极地研究和构建工具。

像 [ShouldIHODL](https://preslav.me/shouldihodl/) 这样的工具。这是一个极其简单的网站，不做任何其他事情，只是对投资比特币的时机是否合适这个问题给出肯定或否定的回答。强调一个词**投资** = >购买更长期的。我对纯粹的投机或去年充斥市场的被操纵的抽油泵和泵出机计划不感兴趣。此外，不言而喻，这是一个简单的附带项目，主要用于娱乐和学习目的，没有任何形式的财务建议。我不想代表任何人对潜在的损失或错过的机会承担任何责任。

[![ShouldIHODL](../Images/a4fd0056df2581ec62d7146a9cd70128.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PgdntP_t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://preslav.me/assets/img/2018/june/shouldihodl_screen.jpg)

## 它是如何工作的？

### 原始数据

这个决定涉及到相当多的机器学习。首先，通过开放的 [Poloniex API](https://poloniex.com/support/api/) 获取数万个数据点。每个数据点代表比特币价格历史的 30 分钟间隔，包含常见图表工具和 API 中已知的数据:

*   `open` -区间开始时的价格
*   `close` -区间末的价格
*   `low` -整个区间内达到的最低价格
*   `high` -整个区间内达到的最高价格
*   `quoteVolume` -以报价货币(又名 BTC)单位表示的交易量变动
*   `baseVolume` -以基础货币单位表示的交易量变动，可以是美元、欧元，也可以是我们的情况 [USDT](https://tether.to/)

对于图表爱好者和交易者来说，前四个更为人所知，因为它们描述了一个[烛台](https://www.investopedia.com/terms/c/candlestick.asp):

[![Candlestick](../Images/bb1348fc47254217fa5f5040ba5ec1c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--54lSXrUf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.investopedia.com/inv/dictionary/terms/candle.gif) 
图片: [Investopedia](https://www.investopedia.com/terms/c/candlestick.asp)

### 清理并提取一些特征

下一步是将原始数据驯服成一个熊猫数据框架:

[![Raw Data Inputs](../Images/e79914e2a40b1d08aa21242522ca3c15.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jbGvCc7a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://preslav.me/assets/img/2018/june/raw_inputs.jpg)

有了几千个烛台，人们就可以利用熊猫惊人的[滚动](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.rolling.html)能力，来滚动 HiLo 比率和在给定时期内归一化到最大值的移动平均值:

```
num_periods_in_7d = 2 * 24 * 7 # Once, every 30 min 
df['hilo_7d'] = df['low'].rolling(num_periods_in_7d).mean() /
df['high].rolling(num_periods_in_7d).mean() 
```

```
# Still volatile, but less so than the current price df['ma_30'] = df['avg_price'].rolling(30).mean()

# Reacting slowly to rapid changes. Can be used as a line of
# support / resistance df['ma_6400'] = df['avg_price'].rolling(6400).mean()

# Gives an idea how far the average price is from a past peak,
# or if it is at the peak itself df['ma_30_6400_ratio'] = df['ma_30'] / 
df['avg_price'].rolling(6400).max() 
```

收集其中一些，你将有效地建立一种简单的方法来表示某个时间窗口内的[价格模式](https://www.investopedia.com/articles/technical/112601.asp):

[![Price Patterns](../Images/92bebdedaf312f25f45055b5828f09e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5l650DyL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://preslav.me/assets/img/2018/june/price_patterns.jpg)

为了节省你更多的细节，这样的比率在技术分析中非常流行，被称为“振荡指标”。事实证明，价格并不只是朝着一个方向或另一个方向疯狂波动，而是表现得有点像物理学中的力。例如，上升运动是由购买群体施加于价格的力量引起的。它不可能无限上涨(即使是在秘密地带)，但最终会面临一股阻力，迫使价格下跌。这就是为什么标准化后，价格线类似正弦波，可以这样分析。对于那些对技术分析基础感兴趣的人，我建议熟悉一下[支撑和](https://www.investopedia.com/university/technical/techanalysis4.asp)阻力的概念。

### 对案件进行分类

仅基于输入，我们的机器学习算法如何知道它们中的哪一个指示潜在的向上或向下移动？没错，我们首先需要对它们进行分类。一个简单的方法是将任意给定时间`T`的价格与未来某个时间(比如一周)`T'`的价格进行比较。如果一周以后的价格高于时间`T`时的价格，我们把这种情况标为 1(上升趋势)，或者标为-1(下降趋势)。利用熊猫的前后移动功能，这是小菜一碟:

```
# negative shifting will pair df['future_price'] = df['ma_6400'].shift(-1 * num_periods_in_7d)

df['future_price_chg'] = (df['future_price'] / df['ma_6400']) - 1
df.loc[df['future_price_chg'] >= 0, 'label'] = 1
df.loc[df['future_price_chg'] <= 0, 'label'] = -1 
```

### 选择机器学习算法

提取特征并标记数据后，我们的输入矩阵开始看起来像一堆胡言乱语，但对于受过训练的眼睛来说，希望对于我们的机器学习算法来说，这已经足够了。

[![Extracted Input Features](../Images/7ab80a7a45301f12c7cfe8ed6965d999.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dPRBN1NQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://preslav.me/assets/img/2018/june/extracted_input_features.jpg)

在机器学习的世界中，有许多算法，每种算法都有不同的用途。尽管媒体希望你相信，ML 并不完全是关于神经网络的。事实上，出于这个项目的目的，我将使用一个[决策树分类器](http://scikit-learn.org/stable/modules/tree.html)来代替。设计得好的话，决策树可以同样有效，其规模和复杂性只是一个像样的神经网络的一小部分。

```
class sklearn.tree.DecisionTreeClassifier(criterion=’gini’, 
                                          splitter=’best’, 
                                          max_depth=None, 
                                          min_samples_split=2, 
                                          min_samples_leaf=1, 
                                          min_weight_fraction_leaf=0.0, 
                                          max_features=None, 
                                          random_state=None, 
                                          max_leaf_nodes=None, 
                                          min_impurity_decrease=0.0, 
                                          min_impurity_split=None, 
                                          class_weight=None, 
                                          presort=False) 
```

与深度神经网络不同，决策树具有易于可视化的优势。决策树图可以被导出并呈现给人类专家，如果算法选择的分支标准看起来合乎逻辑，他们可以进行确认。

[![Sample Decision Tree](../Images/5bf7b33998a3d134c699762f3b414440.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NJRWv619--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://preslav.me/assets/img/2018/june/sample_decision_tree.jpg)

决策树的一个缺点是它们很容易过度拟合，也就是说，学习用手头的特定输入来解决问题，但不能真正推广解决方案。所有类型的机器学习算法都受此困扰，至于如何应对它，真的没有单一的答案。一种方法是将输入数据打乱并分成两个子集。一个用于训练分类器，另一个用于测试算法的准确性:

```
from sklearn.model_selection import train_test_split
from sklearn.tree import DecisionTreeClassifier

inputs_train, inputs_test, labels_train, labels_test = train_test_split(inputs, labels)

tree_classifier = DecisionTreeClassifier()
tree_classifier.fit(inputs_train, labels_train)

# The testing data has not been fed to the estimator before score = tree_classifier.score(inputs_test, labels_test) 
```

在训练时，您还可以使用输入数据折叠之类的东西来引入历元拟合。折叠是一个别出心裁的名字，用于将原始数据集分割成多个子集，并从每个子集中导出训练和测试数据点:

```
from sklearn.model_selection import KFold
from sklearn.tree import DecisionTreeClassifier

tree_classifier = DecisionTreeClassifier()

for train_index test_index in KFold(n_splits=10, random_state=None, shuffle=False):
    X_train, Y_train = inputs.iloc[train_index], labels.iloc[train_index]
    X_test, Y_test = inputs.iloc[test_index], labels.iloc[test_index]

    tree_classifier.fit(inputs_train, labels_train)

    # This score relates to the particular subset only
    # You can yield it and average out the scores for all subsets
    # at then end. You can also fit each subset into a new tree.
    # When predicting the overall score, all trees need to be called,
    # and the results of their scores averaged out. 
    score = tree_classifier.score(inputs_test, labels_test) 
```

#### 随机森林笔记

另一个可能更好的选择是使用随机森林。随机森林是使用随机化超参数(最大深度、最小分裂计数、最大叶节点数等)生成的树的集合，每个树都有不同的输入。当预测被导出时，每棵树被要求分别预测一个结果，并且采取多数投票:

[![Random Forest Ensemble / Wikimedia](../Images/a6992096eb196a7894244f2a2e88e8fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u0C3Mc1---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/c/c7/Randomforests_ensemble.gif) 
来源:[维基媒体](https://commons.wikimedia.org/wiki/File:Randomforests_ensemble.gif)

随机森林的训练和测试方式与单一决策树非常相似:

```
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import cross_val_score

# An important parameter here is the number of trees in the forest tree_classifier = RandomForestClassifier(n_estimators=50)

# Cross-val-score combines folding, splitting, and scoring in one function score = cross_val_score(tree_classifier, X=inputs, y=labels, cv=10).mean() 
```

无论你如何处理它，一旦训练完毕，分类器就可以通过输入当前价格数据(当然是转换成输入特征)来预测未来事件的结果

```
current_point_as_inputs = extract_inputs_from_data_frame(df.iloc[-1]))

# This call will return either `1` or `-1` (our labels) end_class = tree_classifier.predict(current_point_as_inputs)

# This call will return an array of probabilities for each class to occur class_probs = tree_classifier.predict_proba(current_point_as_inputs)
# class_probs => [0.23, 0,77] 
```

### 渲染输出并安排每日更新

做出我们的分类器输出决定后，是时候向网站的访问者显示它了。由于整个操作都是从 GitHub 上读取和托管的，所以我使用了 [Travis](https://travis-ci.org/) 的魔力来获取和转换历史数据，通过决策树分类器对其进行处理，并使用一个名为 [Pelican](https://blog.getpelican.com/) 的 Python 静态站点生成器来呈现输出。Travis 支持某个分支的预定玉米重建，这对于我的目的来说是理想的。所以，本质上，我几乎只用了一个 crown 作业和一些 GitHub 磁盘空间来存储渲染输出。我们来谈谈无服务器架构，好吗？😀

## 对我有什么好处？

没什么。我既不打算从网站获利，也不打算收集用户数据或跟踪用户行为。目前，我这样做只是为了学习和个人发展。每个人都可以查看网站和代码，发表评论，发布建议和请求。正如文章开头提到的，这个工具不是专业的交易建议，仅仅根据它给你的信息来做交易决定，就等于把你的钱花在国家彩票上(你们很多人都这样做，但仍然如此)。

尽情享受吧，不要犹豫给我留下反馈，或者在 [Twitter](https://twitter.com/intent/tweet?text=Should%20I%20HODL%20is%20a%20neat%20machine%20learning%20toy%20project%20by%20%40preslavrachev&url=https%3A%2F%2Fpreslav.me%2Fshouldihodl) 上与你的朋友分享这个词。

* * *

注意:这篇文章最先出现在我的博客上[。](https://preslav.me/2018/06/02/my-new-pet-project-shouldidhodl/)