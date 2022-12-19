# 快速实验，助推 vs 烦恼

> 原文：<https://dev.to/jeffrey04/quick-experiment-boosting-vs-annoy-5b1m>

在完成[统计学习课程](https://lagunita.stanford.edu/courses/HumanitiesSciences/StatLearning/Winter2016/)的过程中，我看到了这部分关于使用 boosting 进行回归的内容。然后通读材料，让我想知道，同样的方法可能适用于[埃里克·伯恩哈德森](https://erikbern.com/)的[骚扰算法](https://erikbern.com/2013/04/12/annoy.html)。

然后我又开始构建原型，但是与我以前的尝试不同(这里的[和这里的](https://cslai.coolsilon.com/2016/01/13/re-implementing-approximate-nearest-neighbour-search/)[和](https://cslai.coolsilon.com/2016/01/18/approximate-neighbour-search-in-multiple-dimensions/)，我对寻找最近的邻居不感兴趣，所以我没有重用那里的代码。相反，我用纯 numpy 代码构建了一个非常粗略的概念验证代码。

```
from functools import reduce

def distance(points, plane_normal, d):    
    return np.divide(np.add(d, np.sum(np.multiply(points, plane_normal), axis=1).reshape(-1,1)),
                     np.sqrt(np.sum(np.power(plane_normal, 2))))

def split_points(x):
    idx = np.random.randint(x.shape[0], size=2)
    points = x[idx,]
    plane_normal = np.subtract(*points)

    return split_points(x) if np.all(plane_normal == 0) else (points, plane_normal)

def tree_build(penalty, x, y):
    points, plane_normal = split_points(x)
    plane_point = np.divide(np.add(*points), 2)
    d = 0 - np.sum(np.multiply(plane_normal, plane_point))
    dist = distance(x, plane_normal, d)

    return {'plane_normal': plane_normal,
            'd': d,
            'points': (y[dist >= 0].size, y[dist < 0].size),
            'mean': (penalty * np.mean(y[dist >= 0]),
                     penalty * np.mean(y[dist < 0]))}

def boost_annoy(B, penalty, x, y):
    return [tree_build(penalty, x, y) for b in range(B)]

def boost_annoy_transform(forest, x):
    result = np.zeros((x.shape[0], 1))
    for tree in forest:
        dist = distance(x, tree['plane_normal'], tree['d'])
        result = np.add(result, np.where(dist >= 0, tree['mean'][0], tree['mean'][1]))

    return result 
```

Enter fullscreen mode Exit fullscreen mode

因此，树被构建为以某种方式反映增强树构建算法的行为，因此它在某种程度上受到惩罚调整参数的限制。影响预测的另一个参数也取自 boosting 模型，即树的数量 b。也因为这是一个快速原型，所以我只分支一次。

将这些值提供给模型进行训练，如下所示

```
x = np.random.normal(size=(1000, 10))
y = np.random.normal(size=(1000, 1))
forest = boost_annoy(B, 0.0001, x, y)
y_hat = boost_annoy_transform(forest, x) 
```

Enter fullscreen mode Exit fullscreen mode

虽然我没有真实世界的数据来测试，但我通过生成一系列随机观察和响应进行了测试，然后使用 scikit-learn 的 KFold 实用函数对 10 维空间中的观察进行了一系列具有不同 B 值的测试。

[![](../Images/e8fc1d6e10fdc0e2f896f6c33109550a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IX8Tm6Yx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hrejhb4p2hgzoi0kzqev.png)

而测试笔记本是[这里](https://gist.github.com/Jeffrey04/8d4b0b9899832b39c36d7d9126df0e6e)。

如图所示，虽然算法的训练误差没有 boosting 的训练误差下降得那么明显，但另一方面，测试误差通常低于 boosting 的测试误差。对于第一个概念验证类型的原型来说，这是一个相当有趣的结果。

当然，有几件事我需要解决，例如正确的分支(即超过 1 级)，一些性能问题等。然而，我对目前的结果很满意，希望我能投入更多的时间来做一些正确的事情。

* * *

后记:嗯，我在样本生成 lol 上搞砸了一点，所以在现实生活数据中有很多工作要做，看看我是否有时间对这篇文章做适当的跟进。