# MongoDB 查询优化:覆盖查询

> 原文：<https://dev.to/bladefidz/mongodb-query-optimization-covered-query--34d2>

## 简介

嗨，我叫哈菲兹。我和你们一样，只是一个普通的软件开发人员。目前，我已经注册了 MongoDB 大学的[MongoDB for developer](https://university.mongodb.com/courses/M101P/about)课程。这个课程很棒，有很棒的老师。我喜欢分享我从这个课程中学到东西。这是关于我们如何通过实现覆盖查询来优化 MongoDB 查询。

本帖与我之前的帖子有关联:[如何评价 MongoDB 上的查询性能](https://dev.to/bladefidz/how-to-evaluate-query-performance-on-mongodb-4dbm)。如果您想了解更多关于如何评估查询性能的信息，那么最好在阅读本文之前先阅读一下。

## 动机

如果集合上没有使用任何索引，那么 MongoDB 将执行`COLLSCAN`。这意味着，如果我们有 **N** 个集合，那么 MongoDB 将在 **N** 次内执行扫描。你的拥有计算机科学学位的朋友可能会对你说:“我们可以做得更好！”。是的，我们可以通过实现索引算法做得更好。MongoDB 已经提供了称为 [B+ trees](https://www.google.com/search?q=B%2B+trees&ie=utf-8&oe=utf-8&client=firefox-b-ab) 的下限数据结构，这为我们提供了 **Log N** 性能。如果你想了解更多关于索引算法的知识，那么试着阅读[这篇文档](https://docs.mongodb.com/manual/indexes/index.html)。

但是，问题是:*我们如何利用索引算法来实现高性能的查询？*

## 查询优化使用覆盖查询

MongoDB 有关于如何优化查询的优秀文档。如果要优化作为索引一部分的查询操作，那么我们可以通过实现**覆盖查询**来实现高性能操作。换句话说，我们希望这样查询操作只检查索引字段，而不检查文档字段。

## 数据库和收藏

我将使用与在[这篇文章](https://dev.to/bladefidz/how-to-evaluate-query-performance-on-mongodb-4dbm)中使用的完全相同的集合。但是如果你没有读过或者只是忘记了，那么我只需要重新解释同样的解释。

在这个简单的教程中，我们使用了一个**学校**数据库和**学生**集合，其中包含 1，000，000 个文档。所有的文件都是从 javascript 文件中随机创建的，你可以从[这里](https://github.com/Bladefidz/mongodb-analytics/blob/master/mongodb%20for%20developers/chapter_4_performance/create_scores2.js)下载。
我们文档中每个键的描述如下:

*   **student_id** :每个学生的唯一标识。
*   **分数**:一个数组包含两个键:*类型*(分数类型)和*分数*(浮点值)。
*   **class_id** :学生所属班级的唯一标识。

## 实现

在这一部分，我们想知道由`student_id`定义的任何学生属于类`5, 15, 30`。所以查询是`db.students.find({class_id: {$in: [5, 15, 30]}}, {_id: 0, student_id: 1, class_id: 1})`

### 第一次实现:无索引

在 shell 中运行以下命令:

```
var exp = db.students.explain('executionStats').find({class_id: {$in: [5, 15, 30]}}, {_id: 0, student_id: 1, class_id: 1})
exp 
```

我们有:

```
...
"winningPlan" : {
                        "stage" : "PROJECTION",
                        "transformBy" : {
                                "_id" : 0,
                                "student_id" : 1,
                                "class_id" : 1
                        },
                        "inputStage" : {
                                "stage" : "COLLSCAN",
...
"executionStats" : {
                "executionSuccess" : true,
                "nReturned" : 5977,
                "executionTimeMillis" : 840,
                "totalKeysExamined" : 0,
                "totalDocsExamined" : 1000000,
... 
```

### 第二种实现:带有索引和适当的覆盖查询

在 shell 中运行以下命令:

```
db.students.createIndex({class_id: 1, student_id: 1})
var exp = db.students.explain('executionStats').find({class_id: {$in: [5, 15, 30]}}, {_id: 0, student_id: 1, class_id: 1})
exp 
```

我们有:

```
...
"winningPlan" : {
                        "stage" : "PROJECTION",
                        "transformBy" : {
                                "_id" : 0,
                                "student_id" : 1,
                                "class_id" : 1
                        },
                        "inputStage" : {
                                "stage" : "IXSCAN",
...
"executionStats" : {
                "executionSuccess" : true,
                "nReturned" : 5977,
                "executionTimeMillis" : 16,
                "totalKeysExamined" : 5980,
                "totalDocsExamined" : 0,
... 
```

## 结论

从解释的结果可以看出，覆盖查询的性能远远高于常规的非索引查询，后者的执行时间比为 2/105，只需检查 5980 个键即可返回 5977 个文档。这是很好的性能增益，对不对？你的计算机科学朋友可能会很高兴看到这个证明。:)

## 建议

继续使用相同的集合，尝试创建多键索引`{'scores.score': -1, 'scores.type': 1}`，然后执行`var exp = db.students.explain('executionStats'); exp.find({'scores.type': 'exam', 'scores.score': {$gte: 90}})`。您可能会问为什么覆盖的查询不起作用。尝试阅读**多键覆盖**部分的[文档](https://docs.mongodb.com/manual/core/query-optimization/#covered-query)。你会找到原因的。