# 如何评估 MongoDB 上的查询性能

> 原文：<https://dev.to/bladefidz/how-to-evaluate-query-performance-on-mongodb-4dbm>

## 简介

嗨，我叫哈菲兹。我和你们一样，只是一个普通的软件开发人员。目前，我已经注册了 MongoDB 大学的 [MongoDB for Developers](https://university.mongodb.com/courses/M101P/about) 课程。这个课程很棒，有很棒的老师。我喜欢分享我从这个课程中学到东西。它是关于我们如何使用`explain("executionStats")`评估 MongoDB 上的查询性能。

## 动机

如果我们真的不知道应该测量哪个部分，那么评估查询性能可能会变得复杂。幸运的是，MongoDB 提供了非常方便的工具来评估查询性能:`explain("executionStats")`。这个工具为我们提供了一些常规度量，比如检查的文档数量和执行时间，这些可以用来进行统计分析。

## 解释(“executionStats”)

你可以从[这个文档](https://docs.mongodb.com/manual/reference/explain-results/#explain.executionStats)中找到`explain("executionStats")`输出的详细解释。为了简化，我们只需要关注一些输出键:

*   **n 返回**:查询操作返回的单据数。
*   **executionTimeMillis** :完成操作所需的总时间，以毫秒为单位。
*   **totalkeyspected**:如果在操作中没有找到索引，则返回 0(零)，否则返回扫描的索引键的数量，以便完成操作。
*   **totalDocsExamined** :为完成操作而检查的文档总数。

## 数据库和收藏

在这个简单的教程中，我们使用了一个**学校**数据库和**学生**集合，其中包含 1，000，000 个文档。所有的文件都是从 javascript 文件中随机创建的，你可以从[这里](https://github.com/Bladefidz/mongodb-analytics/blob/master/mongodb%20for%20developers/chapter_4_performance/create_scores2.js)下载。
我们文档中每个键的描述如下:

*   **student_id** :每个学生的唯一标识。
*   **分数**:一个数组包含两个键:*类型*(分数类型)和*分数*(浮点值)。
*   **class_id** :学生所属班级的唯一标识。

## 评价

在本节中，我们想知道索引是如何提高查询性能的。为了简化案例，我们只关注如何优化“读操作”。

### 初始评估

我们需要找到所有考试分数超过 90 分的学生。所以查询是:`db.students.find({'scores.type': 'exam', 'scores.score': {$gte: 90}})`

### 初评:无索引。

在 shell 中运行以下命令:

```
var exp = db.students.explain('executionStats');
exp.find({'scores.type': 'exam', 'scores.score': {$gte: 90}}) 
```

我们有:

```
...
"executionStats" : {
                "executionSuccess" : true,
                "nReturned" : 343310,
                "executionTimeMillis" : 2843,
                "totalKeysExamined" : 0,
                "totalDocsExamined" : 1000000,
... 
```

### 二评:带索引。

在 shell 中运行以下命令:

```
db.students.createIndex({'scores.score': -1, 'scores.type': 1})
var exp = db.students.explain('executionStats');
exp.find({'scores.type': 'exam', 'scores.score': {$gte: 90}}) 
```

我们有:

```
...
"executionStats" : {
                "executionSuccess" : true,
                "nReturned" : 343310,
                "executionTimeMillis" : 2284,
                "totalKeysExamined" : 399171,
                "totalDocsExamined" : 343310,
... 
```

## 结论

从这两个评估结果中，我们知道，使用复合索引查找任何考试分数大于 90 的学生的操作会运行得更快。在这种情况下，带索引的收集比不带索引的收集执行得更快(2284 毫秒对 2843 毫秒)。

## 建议

尝试运行以下解释查询:

```
db.students.explain('executionStats').find({'scores.score': {$gte: 90}, 'scores.type': 'exam'}) 
```

该查询不同于评估中使用的解释查询，因为我们翻转了查找参数。也许您会说服自己，这个查询应该比我们在评估教程中使用的查询运行得更快。但事实上，并非如此。试着在解释结果上寻找`winningPlan`键，你会找到原因。

我已经创建了一个简单的 javascript 代码来测量“executionTimeMillis”的方法。只需摆弄代码来进行统计分析，例如测量标准偏差。