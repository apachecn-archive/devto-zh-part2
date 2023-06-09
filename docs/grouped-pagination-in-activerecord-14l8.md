# ActiveRecord 中的分组分页

> 原文：<https://dev.to/michaelherold/grouped-pagination-in-activerecord-14l8>

有时候，设计师会模拟一个他们已经考虑过并测试过的非常有用的页面。一旦他们完成了，并把它交给你去实施，你看着它，想，“我究竟怎样才能有效地做到这一点？”最近工作中就发生了这样一个案例。

我们正在开发一个类似于“指挥中心”的东西，在那里我们向客户展示各州的升级列表。该设计要求看起来像三个不同的记录列表，每个表由一个州的记录组成。由于一个客户可能有几十条这样的记录，我们知道我们需要对页面进行分页。设置中的一个问题是，分页应该将完整的记录列表视为数据集，而不是单独的每个列表。这让我们想知道如何在 ActiveRecord 中有效地实现分组分页。

## 天真的实现

当面对模拟时，有时很难完全理解预期的行为。例如，以下面的抽象模拟为例。蓝色代表“草稿”帖子，绿色代表“未发布”帖子，红色代表“已发布”帖子 <sup id="fn-domain-return">[1](#fn-domain)</sup> 。

[![Three groups of records, grouped by status](img/a6f7c4f2f29d393665538c625cb153b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BTI6fMXH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://michaeljherold.com/wp-content/uploads/2018/08/SortedQuery1.png) 

<figcaption>三组记录，按状态分组</figcaption>

在第一次检查时，我和我的搭档看到了三个记录列表，每个列表都由一个与状态对应的标题分组。可以说，对这个 mock 的分页行为的自然理解是分别对每个集合进行分页。当我们将其转化为控制器逻辑时，简单的实现如下所示:

```
class PostsController < ApplicationController
  def index
    @drafts = Post.draft.page(params[:dp])
    @unpublished = Post.unpublished.page(params[:up])
    @published = Post.published.page(params[:pp])
  end
end 
```

我们质疑我们是否正确理解了这个模拟，因为看起来这将是一个具有挑战性的用户体验。为了确保我们理解它，我们做了尽职调查，并联系了设计师。事实证明，我们应该对整个数据集进行分页，而不是单独对每一部分进行分页。

## 一个数据集

我们带着新发现的理解，把模拟带回去做进一步的回顾。几分钟后，我们绘制了以下示意图:

[![The intended pagination scheme for the three groups](img/b620cc33ccd8453617a3df36dc815683.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7FlNtCl9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://michaeljherold.com/wp-content/uploads/2018/08/SortedQuery2.png) 

<figcaption>三组的预定分页方案</figcaption>

通过将列表视为单个数据集，很明显每个页面上可以有多个组。然而，并不一定每个页面上都有所有的组。根据查询的大小，有些可能三种状态都有，但有些可能只有两种状态，或者只有一种！

更复杂的是，我们知道顺序主要取决于文章的状态，我们应该按照标题对每组文章(按状态分组)进行排序。显然，我们应该运行单个查询并对其进行分页，如下面的模拟:

[![The resulting set of paged records](img/6a16cfe3126141b049c9459bca87845e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qEw-pmIq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://michaeljherold.com/wp-content/uploads/2018/08/SortedQuery3.png) 

<figcaption>得到一组分页记录，每页显示。请注意排序和分组带来的复杂性。</figcaption>

我们最初被难住了。我们如何有效地做到这一点？

## 第一次尝试

我们第一次尝试这样做是在数据库中使用 SQL 中的`GROUP BY`子句。`GROUP BY`非常适用于需要聚合记录组信息的情况。然而，我们需要将该集合视为一个单独的源，这样我们就可以对其进行分页。使用`GROUP BY`是不可能的。

在那之后，我们试图不做任何花哨的事情，而是使用一个`ORDER BY`子句。然而，我们回想了一下要求:我们必须首先显示*草稿*帖子，其次是*未发布的*帖子，第三是*已发布的*帖子。因为这三个词是按字母顺序排列的，“草稿”、“已发布”、“未发布”，我们知道这不是一个简单的查询。

## 分组分页

我们稍微深入思考了一下这个问题。如果我们能以某种方式将“状态”字段更改为一个数字，我们就可以很容易地根据这个数字对文章进行排序。带着这个想法，我们使用了 [`CASE`条件表达式](https://www.postgresql.org/docs/current/static/functions-conditional.html#FUNCTIONS-CASE)来将状态字段转换成我们可以使用的东西。使用 SQL 片段转换 post 状态并按计算字段对它们进行排序，如下所示:

```
class Post < ApplicationRecord
  def self.order_by_status
    clause =
      Arel.sql <<~SQL CASE "posts"."status"
        WHEN 'draft' THEN 0
        WHEN 'unpublished' THEN 1
        WHEN 'published' THEN 2
        ELSE 3
        END SQL

    order(clause)
  end
end 
```

为此，我更喜欢使用 Arel 节点，以便在出现问题时给出更好的错误消息，因此我们重构为以下形式:

```
class Post < ApplicationRecord
  def self.order_by_status
    order(
        Arel::Nodes::Case
          .new(arel_table[:status])
          .when('draft').then(0)
          .when('unpublished').then(1)
          .when('published').then(2)
          .else(3)
    )
  end
end 
```

太好了！我们通过调用`.order(:title)`对帖子进行了适当的分组和排序。然后我们可以把它插入控制器来观看 <sup id="fn-simple-return">[2](#fn-simple)</sup> :

```
class PostsController < ApplicationController
  def index
    @drafts = posts.select(&:draft?)
    @unpublished = posts.select(&:unpublished?)
    @published = posts.select(&:published?)
  end

  private

  def posts
    @posts ||=
      Post
      .order_by_status
      .order(:title)
      .page(params[:page])
  end
end 
```

这个简单的查询 <sup id="fn-selects-return">[3](#fn-selects)</sup> 能够完成我们需要它做的事情:分组分页。它为我们提供了一个统一的数据集来分页，按状态分组，并按每个组的正确顺序排列。令人困惑的逻辑很容易被隔离在`Post.order_by_status`方法之后，我们对其进行了隔离测试，以确保未来的 Rails 升级不会破坏语法或逻辑。

ActiveRecord 中分组分页的帖子[最早出现在](https://michaeljherold.com/2018/08/18/grouped-pagination-in-activerecord/) [Michael Herold](https://michaeljherold.com) 上。

* * *

1.  为了不使这篇文章与我们领域的内部细节复杂化，我决定用博客文章作为理解的领域来展示这个例子。你需要知道的是，文章有一个“标题”,我们将状态存储为字符串。 [↩︎](#fn-domain-return)
2.  现实中，我们并没有止步于此。我们使用`SimpleDelegator`将结果提取到一个模型中。这让我们从控制器和视图中移除分组逻辑。然而，这与这篇文章没有密切关系，所以我忽略了它。 [↩︎](#fn-simple-return)
3.  使用`select`可能会引起你的注意。因为我们需要在页面上显示记录，所以从关系中进行选择不会影响性能。还有，使用`#where`有两个问题。首先，它删除了分页。其次，它运行三个查询，而不是一个。 [↩︎](#fn-selects-return)