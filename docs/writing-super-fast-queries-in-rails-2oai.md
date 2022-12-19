# 用 Rails 编写超快速查询

> 原文：<https://dev.to/cassidycodes/writing-super-fast-queries-in-rails-2oai>

在本周的工作中，我不得不加快一项后台工作的速度，它堵塞了我们的队列。这项工作将记录和帖子的数据聚合到我们的弹性搜索索引中。它受到各种额外数据库调用的困扰。我从这个查询中获得了很多乐趣！让事情变得快速是如此令人满意。

下面是我在构建 SQL 查询时学到的一些知识，对于典型的 ActiveRecord 对象，这些查询会变得很困难。

## 一点关于数据

假设我们有一个有很多用户的博客。每个用户有很多帖子，帖子有很多评论。您的数据库可能如下所示:

**用户**

```
id | name
---|------------------
1 | Sonja Sis
2 | Nicol Hollinghead
3 | Edison Huseman 
```

Enter fullscreen mode Exit fullscreen mode

**帖子**

id|title|body|user_id 1 |我爱狗|狗很棒。| 1 2 |我爱猫|猫比狗好。| 2 3 |我喜欢狗和猫|宠物很有趣。| 3

**评论**

```
id | body | user_id | post_id
---|---------------|---------|--------
1 | Me too! | 3 | 2
2 | They are not! | 1 | 2 
```

Enter fullscreen mode Exit fullscreen mode

现在，假设我们想知道每篇文章在过去 90 天、30 天和 15 天里收到了多少评论。我们最终要寻找这样的数据结构:

```
{ 1 => { ninety_days: 190,
         thirty_days: 67,
         fifteen_days: 14 },
  2 => { ninety_days: 583,
         thirty_days: 392,
         fifteen_days: 83 } } 
```

Enter fullscreen mode Exit fullscreen mode

我们可以这样尝试:

```
class Post < ActiveRecord::Base
  scope :between, (range)-> { where(created_at: range }
end

class MyPostSummary
  def self.summarize
    Post.find_each.with_object({}) do |post, h|
      h[:post_id] = {}
      h[post.id][:ninety_days] =
        post.comments.between(90.days.ago..0.days.ago).count
      h[post.id][:thirty_days] =
        post.comments.between(30.days.ago..0.days.ago).count
      h[:post_id][:fifteen_days] =
        post.comments.between(15.days.ago..0.days.ago).count
  end
end

MyPostSummary.summarize

# { 1 => { ninety_days: 190,
# thirty_days: 67,
# fifteen_days: 14 },
# 2 => { ninety_days: 583,
# thirty_days: 392,
# fifteen_days: 83 } } 
```

Enter fullscreen mode Exit fullscreen mode

很重复，对吧？如果我们要建立一个博客中所有帖子的摘要，我们将会做很多不必要的计算！

## 使用`select`

当我第一次解决这个问题时，我想，“嗯，也许我可以使用 ActiveRecord 的`select`来选择每个周期的计数。”这在保存数据库查询方面创造了奇迹！为了简洁起见，我在这里省略了这个类的其余部分。下面是这个查询的样子:

```
# Use SQL to count the comments for each post.
select = <<~SQL
           posts.id,
           Count(IF(comments.created_at >
                    DATE_SUB(CURRENT_TIMESTAMP, 90 DAY),
                    comments.id,
                    NULL)) AS ninety_days,
           Count(IF(DATE_SUB(CURRENT_TIMESTAMP, 30 DAY),
                    comments.id,
                    NULL)) AS thirty_days,
           Count(IF(DATE_SUB(CURRENT_TIMESTAMP, 15 DAY),
                    comments.id,
                    NULL)) AS fifteen_days
         SQL 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一个`LEFT OUTER JOIN`在这里，因为我们想确保我们得到回帖，即使他们没有评论。

还要注意这里的`group`。如果没有这一点，我们将为每个评论获得 1 条帖子记录，并且我们会以重复结束，因为帖子有许多评论！

```
posts = Post.left_outer_joins(:comments)
            .select(select)
            .group("posts.id")

posts.find_each.with_object({}) do |post, h|
  h[:post_id] = {}
  h[post.id][:ninety_days] = post.ninety_days
  h[post.id][:thirty_days] = post.thirty_days
  h[post.id][:fifteen_days] = post.fifteen_days
end 
```

Enter fullscreen mode Exit fullscreen mode

好的，太好了！我们已经解决了返回数据库统计评论的问题。现在我们只做一个查询，返回一个计数。

您知道当您添加`SELECT … AS my_select`时，ActiveRecord 会为返回的对象添加该属性的方法吗？这就是为什么`post.ninety_days`在上面的代码中工作。我觉得那很方便。

尽管如此，我还是对这个最终结果感到不舒服。我们将记录加载到 ActiveRecord 中，而我们需要的只是计数数据和文章 id。

## `exec_query`前来救援！

`exec_query`返回您要求的列名和值的散列。这让你完全跳过活动记录！

```
query = Post.left_outer_joins(:comments)
            .select(select)
            .group("posts.id")
            .to_sql
result = Post.connection.exec_query(query)
# => [{ "id" => 1,
# "ninety_days" => 190,
# "thirty_days" => 67,
# "fifteen_days" => 14 }...]

result.map!(&symbolize_keys!).group_by(&:id)
# =>
# { 1 => { ninety_days: 190,
# thirty_days: 67,
# fifteen_days: 14 },
# 2 => { ninety_days: 583,
# thirty_days: 392,
# fifteen_days: 83 } 
```

Enter fullscreen mode Exit fullscreen mode

耶！我们得到了相同的结果，看看代码有多少！如果您正在查询一个大数据集，这将节省各种时间！

## 包装完毕

如果您需要从数据库中检索数据，但是不需要模型的任何功能，使用`exec_query`来跳过 ActiveRecord 并加快速度。

如果您*确实*需要 ActiveRecord，那么您可以通过将 SQL 传递给`select`方法并使用`AS`给它命名，来为您得到的对象添加额外的属性。

对于具有复杂连接的查询，或者可能需要以编程方式构建的查询，依赖 ActiveRecord 可能会变得很困难。看看 [Arel](https://github.com/rails/arel) ，一个在 ActiveRecord 后面形成抽象语法树管理器的库，用于类似这样的情况。