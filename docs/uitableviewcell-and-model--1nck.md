# UITableViewCell 和模型

> 原文：<https://dev.to/onmyway133/uitableviewcell-and-model--1nck>

原帖[https://github.com/onmyway133/blog/issues/154](https://github.com/onmyway133/blog/issues/154)

iOS 中最常见的 UI 元素是`UITableView`，最常见的任务是使用模型显示 UITableViewCell。

虽然标题中指定了 UITableViewCell，但是问题涉及到其他视图(UICollectionView，custom view，...)也是

关于这个有很多争论，所以在这里我想做一个总结，都是我的看法

### 它始于

[UITableViewCell 不是控制器](http://inessential.com/2012/12/31/uitableviewcell_is_not_a_controller)
这篇文章遵循严格的 MVC 并声明我们不应该将模型对象传递给单元格，让单元格直接在模型上操作。他展示了几个例子，指出这项工作不适合细胞。相反，这项工作应该在控制器(UIViewController，UITableViewDataSource，...)

### 类别为简单情况

[使用视图类别的更瘦的控制器](http://www.sebastianrehnby.com/blog/2013/01/01/skinnier-controllers-using-view-categories/)
这篇文章指出，我们应该通过将作业(将模型对象映射到单元)转移到单元类别来保持视图控制器更瘦

### 使用子类化

[UITableViewCell 不是一个控制器，而是……](http://corporationunknown.com/blog/2013/01/01/uitableviewcell-is-not-a-controller-but/)
这篇文章解释了子类化利用多态性的好处。在他给出的主题化示例中，我们看到控制器现在的工作是选择正确的单元子类，而子类的工作是知道如何使用模型
“当 UITableViewCell 子类接受模型对象参数并更新它的组成子视图时，正如我所描述的，它表现为数据转换器，而不是控制器”

### 模型演示者

[模型视图控制器演示者](https://medium.com/ios-apprentice/model-view-controller-presenter-8bb4149fa5ef)
这篇文章说明了当你有更多的单元和模型时，使用子类化和类别会有重复的实现。经过多次优化后，他最终找到了模型展示者，这是知道如何在不同视图中展示模型的中心对象。
“这是一个知道如何表现某个模型各个方面的对象”

#### MVVM

MVC、MVVM、FRP 和造桥
这篇文章解释了 MVVM，但它直接触及了我们的问题。细胞和模型的问题是

1.  如何将模型映射到单元
2.  谁来做这项工作？单元、控制器或其他模型映射对象？

ViewModel 实际上是做这项工作的人，它将模型转换成视图可以轻松使用的东西

“一个表视图数据源没有这些东西。它纯粹是表视图和模型之间的一层。该模型定义了事物列表，但是表视图数据源将这些列表转换成部分和行。它还返回实际的表格视图单元格，但这不是我在这里关注的。关键是它作为中间层数据转换器的角色。”

### 我们是否访问单元格的子视图

[Paul on UITableViewCell](http://inessential.com/2013/01/02/paul_on_uitableviewcell)
Brent 接着发表了另一篇文章，解释了单元格的子视图不应该在单元格外被访问

### 数据源

[清理表格视图代码](http://www.objc.io/issue-1/table-views.html)
这篇文章处理模型对象和单元之间的桥梁。
“有时我们必须将想要显示的数据提交给视图层。因为我们仍然希望在模型和视图之间保持一个清晰的分离，所以我们经常把这个任务交给表视图的数据源。这种代码使数据源与关于单元设计的特定知识混杂在一起。我们最好将此分解为细胞类的一个类别”

这与[更亮的视图控制器](http://www.objc.io/issue-1/lighter-view-controllers.html)一起显示了处理大多数情况的实际例子

## 引用

1.  【UITableViewCells 应该了解模型对象吗？