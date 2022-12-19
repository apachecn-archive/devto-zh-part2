# 使用 MongoDB 上的分析方法找到热点操作

> 原文：<https://dev.to/bladefidz/find-the-hot-spot-operation-using-profiling-method-on-mongodb-36ea>

## 简介

嗨，我叫哈菲兹。我和你们一样，只是一个普通的软件开发人员。目前，我已经注册了 MongoDB 大学的[MongoDB for developer](https://university.mongodb.com/courses/M101P/about)课程。这个课程很棒，有很棒的老师。在参加课程的时候，我发现自己喜欢读 Barry Wilkinson 和 Michael Allen 写的《T2 并行编程》一书。通过阅读这本书，我发现了更多关于如何在 MongoDB 上评估操作性能的见解。

在这里，我喜欢分享我已经从课程和书本中学到的东西。这是关于我们如何在 MongoDB 上使用 Profiling 方法找到热点操作。

## 动机

[古斯塔夫森定律](https://en.wikipedia.org/wiki/Gustafson%27s_law)告诉我们，延迟可以用来衡量实现的并行程序。我们获得的延迟越低，性能就越好。但是，如果我们有许多可能要执行的任务，找到降低延迟率的方法就有些困难了。一种简单的方法是优化大多数已执行的操作。这种操作被称为**热点**。

## MongoDB 上的数据库探查器

你可以从[这个文档](https://docs.mongodb.com/manual/tutorial/manage-the-database-profiler/)中找到关于**数据库概要分析器**的详细解释。为了简化，我们只需要关注这些关键点:

1.  分析级别

    *   0:关闭。
    *   1:开，但对给定阈值有选择性，单位为毫秒。
    *   2:开启并屏蔽所有执行操作。
2.  获取概要分析器输出
    运行这个命令`db.system.profile.find()`来获取概要分析数据。你可以在[这个文档](https://docs.mongodb.com/manual/reference/database-profiler/)中找到关于 profiling output 的详细解释。为了使本教程更加直观，我们只关注一些关键输出:

    *   **op** :执行操作。
    *   **ns** :操作的命名空间。
    *   **responseLength** :对应操作产生的文件长度，以字节为单位。
    *   **毫秒**:操作完成所请求文档所需的时间，以毫秒为单位。有人称这为*潜伏速度*。

## 剖析查找热点操作

假设我们已经有了一些集合，想要做一些性能测试。我们希望检查应用程序的行为，找出执行最多的操作，然后将它们保存在热点池中，并找到优化它们的最佳方法。

好了，让我们开始吧:

*   启动 mongod，打开分析选项，但是有选择地只转储任何超过 100 毫秒阈值的操作。

```
mongod --dbpath /usr/local/share/mongodb --port 27001 --profile 1 --slowms 100 
```

*   或者，您可以通过执行以下命令来激活特定数据库中分析:

```
db.setProfilingLevel(1, { slowms: 100 }) 
```

*   对数据库进行一些操作。MongoDB 将创建一个名为`system.profile`的集合，其中包含操作配置文件数据。
*   获得前五个最慢的操作

```
db.system.profile.find().sort({millis: -1}).limit(10).pretty() 
```

*   假设我们只是在 **school2.students** 集合上执行一些查询。因此，在分析日志中，我们得到了类似这样的输出:

```
{
    ...
    "op" : "query",
    "ns" : "school2.students",
    ...
    "millis" : 15820
},
{
    ...
    "op" : "query",
    "ns" : "school2.students",
    ...
    "millis" : 12560
},
{
    ...
    "op" : "query",
    "ns" : "school2.students",
    ...
    "millis" : 11084
},
{
    ...
    "op" : "query",
    "ns" : "school2.students",
    ...
    "millis" : 9493
},
{
    ...
    "op" : "query",
    "ns" : "school2.students",
    ...
    "millis" : 9059
} 
```

## 结论

看起来对 school2.student2 集合查询操作是大约一秒钟内执行的热点操作。我们可以对热点操作使用`explain(executionStats)`来分析操作的执行统计。你可能会发现[这篇文章](https://dev.to/bladefidz/how-to-evaluate-query-performance-on-mongodb-4dbm)会有帮助。

## 建议

如果我们可以将分析输出绘制成直方图，分析可能会变得更容易。您可能对这个工具感兴趣，或者找到自己的方法将分析输出绘制成直方图。