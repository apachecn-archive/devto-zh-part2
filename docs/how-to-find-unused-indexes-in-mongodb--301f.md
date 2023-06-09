# 如何在 MongoDB 中找到未使用的索引？

> 原文：<https://dev.to/scalegrid/how-to-find-unused-indexes-in-mongodb--301f>

在本文中，我们将向您展示如何在 MongoDB 中找到未使用的索引，检查索引集合的使用统计，以及如何删除未使用的索引。

对于开发团队来说，在几个应用程序版本中主动管理 MongoDB 数据库索引是一个挑战。需求经常随着每个版本而变化，可能会添加一些新的索引，而旧的索引可能会被放弃。随着时间的推移，这使得很难跟踪哪些 MongoDB 索引正在被使用，哪些索引现在是不必要的。

索引对写性能有很大的影响——每次对集合进行写操作时，相关的索引都需要更新。缺少索引会立即表现出来，并降低查询速度——未使用的索引更加微妙，需要主动删除以提高写性能。更多信息可以在这里找到- [在 MongoDB 上建立索引的风险](https://scalegrid.io/blog/the-perils-of-building-indexes-on-mongodb/)。

在 MongoDB 的早期版本中，没有简单的方法来确定索引是否没有被使用。然而，从 3.2 版本开始，MongoDB 增加了对 [$indexStats](https://docs.mongodb.com/manual/reference/operator/aggregation/indexStats/) 操作符的支持，该操作符允许您收集关于索引使用的统计信息。

## 在 MongoDB 中查找未使用的索引

要检查集合中特定索引的使用统计信息，可以使用以下命令:

1 db . collection . aggregate([{ $ indexStats:{ } }，{ $ match:{ " name ":"&lt；index name&gt；”}}])

要获取集合中所有索引的统计信息，请执行以下操作:

1 db . collection . aggregate([{ $ indexStats:{ } }])

返回的文档将如下所示:

1 {
2 "name" : "test "，
3 "key" : {
4 "key" : 1
5 }，
6 "host" : "xxx:27017 "，
7 " access ":{
8 " ops ":NumberLong(145)，
9 " since ":iso date(" 2017-11-25t 16:21:36.285 z ")
10 }
11

这里需要注意的两个重要字段是:

1.  #### 工作

    这是使用索引的操作数。

2.  #### 因为

    这是 MongoDB 收集统计数据的时间，通常是最后一次开始时间。

## 删除 MongoDB 中未使用的索引

一旦识别并验证了未使用的 MongoDB 索引，就可以使用下面的代码删除该索引:

1db . collection . drop index("&lt；索引名称& gt)&nbsp；或者
2db . collection . drop index(" { key 1:1.....}")

和往常一样，在继续删除操作之前，请验证您删除的是正确的索引。

如果您对在 MongoDB 中查找未使用的索引有任何疑问，请在下面留下评论或通过 [support@scalegrid.io.](mailto:support@scalegrid.io) 联系我们