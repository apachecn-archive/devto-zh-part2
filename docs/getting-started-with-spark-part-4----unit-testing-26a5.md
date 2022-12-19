# Spark 入门(第 4 部分)——单元测试

> 原文：<https://dev.to/diogoaurelio/getting-started-with-spark-part-4----unit-testing-26a5>

好吧，很久以前(已经开始计算年数了)，我发表了一个系列教程，重点是帮助人们开始使用 Spark。以下是之前帖子的概要:

*   第 1 部分入门——涵盖分布式 Spark 架构的基础知识，以及数据结构(包括老的好的 RDD 集合(！)，Dataframes 不赞成使用它)
*   [第二部分数据帧简介](https://datacenternotes.com/2015/11/01/getting-started-with-the-spark-part-2-sparksql/)
*   [第三部分 UDF 和窗口函数介绍](https://datacenternotes.com/2016/10/03/getting-started-with-spark-part-3-udfs-window-functions/)

与此同时，Spark 的受欢迎程度并没有下降，所以我想我会继续更新同一个系列。在这篇文章中，我们将讨论任何 ETL 项目的基本部分，即单元测试。

为此，我创建了一个样本库，它是任何新的 Python Spark 项目的模板代码。

让我们浏览一下主作业脚本。请注意，存储库可能包含更新的版本，这可能会推迟到下一个要点的细节。