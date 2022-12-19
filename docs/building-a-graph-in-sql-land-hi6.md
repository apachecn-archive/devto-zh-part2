# 在 SQL 领域中构建图表

> 原文：<https://dev.to/annarankin/building-a-graph-in-sql-land-hi6>

本着分享失败并从中吸取教训的精神，我想告诉你一个故事，年轻时的我帮助创建了一个“聪明”的系统来组织内容层次。这个警世故事主要是为了好玩，但也记录了我一路上学到的一些有趣的事情和痛苦的教训。为了保护无辜的人，名字和实体都被改变了😜

## 但是...为什么？

很久很久以前，在一个遥远的产品团队中，有一个项目是为藏书创建一个新的、更灵活的层次结构。最初，我们的数据是这样设置的:

[![original data setup - collections, genres, and books](../Images/ee7193b3e1ddaf4d006b2086cd07453a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Zm0BX_Ci--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t5bahvoa7w36sq4n7myu.png)

用户可以访问一系列书籍(在这种情况下是“小说”)；一个集合包含了几个流派，这些流派又包含了各自的书籍。用户可以被授权查看一个收藏中的所有内容，并可以创建“阅读列表”,这样他们就可以根据自己的标准来喜欢/分类书籍。

这很有效——用户可以看到收藏中的所有小说书籍，他们甚至可以将这些书籍添加到个性化的阅读列表中。然而，后来我们的产品团队开始测试一个新系列:

[![same diagram, but with a new "nonfiction" collection in addition to the first](../Images/104219f7b767edc32c950465ce13ad76.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vMMwf-SF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wwqmg75m7jffpkb3wj0b.png)

我们最初不希望允许所有用户访问新的“非小说类”收藏，所以我们单独授予访问权限。

我们有一些问题——主要是用户一次只能从一个集合中访问和创建阅读列表，并且他们必须在 UI 中手动切换它们。当我们只有一个集合时，这种方法工作得很好，但是当我们开始添加更多集合时，就缺乏灵活性了。此外，如果一本书出现在一个以上的流派中，它的条目将需要被复制——我们的数据结构不期望一本书有一个以上的流派😬用户还被禁止将根本不属于我们任何收藏的书籍添加到他们的阅读列表中。如果他们想要一个一站式商店来跟踪他们正在阅读的内容，他们就要倒霉了。

我们的想法是，随着我们添加更多的收藏，我们需要一种更好的方法来分类我们的内容并避免重复。来自整个组织的人们一起工作来创建一个新的内容结构:

[![proposed content hierarchy](../Images/37f608e2c835aecc8b76c35497d8ceaf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dioK6EgH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3cnn0ur0ebledvcotbt3.png)

我们不是将我们的内容分割成“集合”，而是将所有的东西存放在一个巨大的集合中——称为“图书馆”——并允许书籍以多种风格出现。我们希望这将使我们的内容在搜索中更容易被发现，减少重复，并允许用户拥有包含小说和非小说标题的阅读列表。

### 技术

我们已经在这个应用中使用了一些可靠的技术:

*   Postgres 数据库(大多数 SQL 数据库都有某种形式的数据库视图支持)
*   在一个作为 ORM 的 [Ruby on Rails](https://rubyonrails.org/) 应用程序中 [ActiveRecord](https://guides.rubyonrails.org/active_record_basics.html)

现在，作为工程师，我们的工作是实现产品愿景，并为我们的同事提供与这种新模式互动所需的工具。我们有一个选择:我们是创建一系列通过标准关系相互链接的离散表，还是尝试一些新的东西？

(剧透警告——我们尝试了新的东西。)

* * *

## 走势图

我们很快就决定，如果我们要创建这个新的“世界观”，我们希望在这个过程中使用一些很酷的新技术——公平地说，图形模型似乎确实可以很好地服务于当前的用例。我们画出了一些我们期望我们的实体和关系看起来像什么的概念。

[![image of graph containing library, reading list, genres and books a user can access](../Images/d06bbdd41b46ebe27985be4e30908b5e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8ywXJ5Rs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lbyt3lpkpas6cspmz5vo.png)

如果用户愿意的话，他们可以把自己的书添加到阅读清单中——但这并不意味着它们属于图书馆的藏书。

完成后，我们讨论了实现——我们如何将它引入到我们的应用程序中？这如何适应我们当前的代码库？经过一些研究和探索，我们决定避免引入真正的图形数据库(如 Neo4j、OrientDB 等)。).成本是一个潜在的问题，我们希望避免强迫我们的开发人员学习[新概念](https://neo4j.com/developer/get-started/)和[新查询语言](http://tinkerpop.apache.org/docs/current/reference/#intro)，直到我们确定这个模型是我们真正想要坚持的。

决定之后，我们起草了在关系数据库中存储图形实体和关系(“节点”和“边”)的方法:

[![table setup](../Images/717b2632f2df5192632a601867370bce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TfbF_srh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jb9orgc3kd8f6jgl7p68.png)

## 好人

一旦我们建立了新的层次结构，呈现嵌套关系就变得简单了。我们可以很容易地将一个更高层次的模型(比如一个流派)转换成一个树状的 JSON 表示(在现实世界中，我们有几个额外的层次要处理——比如类别、子类别等等)。-所以这很有帮助)。

```
library.to_json
# => 
{
  id: 1,
  name: 'Library',
  genres: [{
    id: 1,
    name: 'Fantasy',
    books: [{
      name: 'Book 1',
      id: 10
    },{
      name: 'Book 2',
      id: 20
    }]
  }]
} 
```

与数据的交互也变得非常简单——我们的节点都有相同的属性，所以我们使用非常通用的视图和服务来显示、创建和编辑从图书馆和阅读列表到流派和书籍的所有内容。

```
<h1>Editing <%= @node.name %></h1>
<%= render 'node-edit-form', node: @node %> 
```

另一个好处是很好地理解了图形结构。我们学习了如何遍历一个图，如何过滤一个图，如何检查循环引用等等。

## 坏了

一开始一切都很好——当一切看起来都一样的时候。然而，没过多久，我们发现我们需要在发送到前端的有效载荷中包含额外的信息。

```
# Our data model started to become more complex...
{
  id: 1,
  name: 'Library',
  genres: [{
    id: 1,
    name: 'Fantasy',
    book_count: 2,
    books: [{
      name: 'Book 1',
      available_for_checkout: true,
      author: 'Bob Bobberson',
      id: 10
    },{
      name: 'Book 2',
      available_for_checkout: false,
      author: 'Frances Farina',
      id: 20
    }]
  }]
} 
```

我们开始在库层次结构中混合行为和附加属性，而不是让图结构做它擅长的事情——定义实体之间的关系。我们开始硬塞单个节点的细节，使呈现我们过于一般化的视图所需的逻辑变得复杂。这些视图很快变得复杂，充满了节点类型的切换。

```
<h1>Editing <%= @node.name %></h1>

<% if @node.type == 'Genre' %>
  <p>Book count: <%= @node.book_count %></p>
  <%= render 'node-edit-form', node: @node %>
<% elif @node.type == 'Book' %>
  <%= render 'book-node-edit-form', node: @node %>
<% else %>
  <%= render 'node-edit-form', node: @node %>
<% end %> 
```

过了一段时间，很明显我们的应用程序是一个图形数据库，我们把它打造成一个书店的形状，而不是一个利用图形数据库存储数据的书店。我们的代码库充斥着过于普通、无意义的方法和控制器，很难理解。

```
# Instead of this...
def save_book(book_data)
  # Clearly saving a book to the DB
  save_to_graph(book_data) 
end

# we ended up with this:
def save_node(node_data)
  # What are we saving here??
  Nodes.save(node_data)
end 
```

## 好可怕

随着图书馆规模的增长，查询变得非常糟糕。查询越来越复杂；为了避免进行数百次查询，需要进行复杂的预加载。我们的许多服务依赖递归来生成序列化的 JSON 或聚合数据，当试图找出您的 bug 是什么、是什么导致的，以及您的额外查询在哪里进行时，这增加了精神开销。当新的开发人员加入我们的团队时，他们很难提高我们正在做的事情，这导致了沮丧和浪费了大量时间。我们使用了类似递归 SQL 视图的工具和可怕的“octo-UI”(见下文)管理工具，这些工具带来了更多的痛苦而不是快乐。

[![image of a force-directed graph](../Images/87a292bd0acc6b64c77772f2ad0d9695.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yUlbpiFB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nborilsqh0hmmtxr24m7.png)

*该图类似于我们实现的权宜管理界面，允许管理员与该图互动——截图取自[D3 示例图库](https://github.com/d3/d3/wiki/Gallery)T3】*

## 忍无可忍

总的来说，在我们放弃幽灵并开始撕掉它之前，我们用这个伪图结构工作了不到一年。一些值得注意的原因:

*   如上所述，一旦我们的数据模型发展起来，为“节点”的一般概念编写服务和视图就不能很好地工作了
*   ActiveRecord 当然不支持这种在关系数据库中存储类似图形的结构的想法——这导致了低效和混乱的查询
*   *非常重要的是*，我们(作为开发人员)没有为其他开发人员和我们产品的最终用户创建工具来使这些概念易于使用。

## 吸取教训

虽然我不会再这样做了，但我确实在这个过程中学到了一些东西。

1.  递归视图很酷🤓
2.  永远不要把你个人的好奇心放在别人的生计之前。
3.  使用合适的工具完成工作！

下一次，当你发现自己在关注一项闪亮的新技术，并渴望将其用于对公司日常业务至关重要的产品时，我鼓励你仔细考虑你的选择。

> 我甚至需要在这里插入闪亮的东西吗？...我真的是 T2 吗？

最常见的情况是，选择“无聊的技术”( [*选择无聊的技术，*丹·麦金利](http://mcfunley.com/choose-boring-technology))而不是“新的热门”，这对你最有利如果你一直选择令人兴奋的创新技术，而不是寿命更长、广为人知的系统，你将增加与新工程师入职和维护整个系统相关的成本/头痛。

我认为这也适用于我们用来构建软件的*模式*。当我们很好地隐藏了图形结构的实现细节时，我们引入了感觉不直观和困惑新开发人员的代码。如果我们在隔离代码方面做得更好，那么以后用真正的代码替换我们“自己开发”的版本会容易得多。

[![](../Images/1bb0e20a8e0a0dd777d5720f867be1d8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8SzUgT6J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/67s1zlsqaz1gbl9he4az.png)

上面链接的文章为限制团队使用的技术解决方案的数量提供了一个很好的例子。你可以选择专注于解决业务问题，而不是花费精力排除故障和维护自主开发的解决方案。添加太多不熟悉的技术，你可能会有一两个“专家”(晦涩难懂的大师)来解释你的应用程序是如何工作的，而不是一个大多数人可以在合理的时间内快速上手的简单代码库。

如果诚实地回答“我需要这个吗？”“是的，这是目前为止我的问题的最佳解决方案”，确保您有时间、支持、团队带宽和专业知识来这样做。如果这些条件都满足了，那么你就需要做你的研究——请使用合适的工具🙇‍♀️