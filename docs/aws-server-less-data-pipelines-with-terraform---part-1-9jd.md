# 使用 Terraform 的 AWS 无服务器数据管道-第 1 部分

> 原文：<https://dev.to/diogoaurelio/aws-server-less-data-pipelines-with-terraform---part-1-9jd>

这篇文章是关注 AWS 选项以无服务器方式设置管道的系列文章的第一篇。我们在整个系列中涵盖的主题有:

*   [第 1 部分:Python Lambda 将数据加载到 AWS 红移数据仓库](https://dev.to/diogoaurelio/aws-server-less-data-pipelines-with-terraform---part-1-9jd)
*   [第 2 部分:自动触发λ功能的地形设置](https://dev.to/diogoaurelio/aws-server-less-data-pipelines-with-terraform---part-2-3fhg)
*   第 3 部分:用 AWS Lambda 调度 cron 管道的示例 AWS 步骤函数

在这篇文章中，我们倾向于另一种策略来建立数据管道，即事件触发。也就是说，我们的传统管道代码不是被安排以给定的频率执行，而是由给定的事件触发立即执行。我们的示例包括一个演示场景，用于将存储在 S3 的数据立即自动加载到 Redshift 教程中。

请注意，触发器并不是云范式独有的新东西。例如，大多数数据库都有触发器，用户可以设置这些触发器来执行用户定义的函数。然而，AWS Lambda 函数与其自身服务的大量集成令人印象深刻且非常有用。在这种情况下，是 AWS S3 可以触发 Lambda 函数的执行。

人们认为通过触发器集成 S3 和 Lambda 函数的第一个典型用例是图像处理，如缩略图。在本帖中，我们将它用于第二个用例，即 AWS 红移的近实时数据导入。从开发人员的角度来看，这项工作非常简单，因为 AWS 在后台完成了所有的工作。

事不宜迟，让我们深入研究代码。

**λ函数**

Python 代码是一个非常简单的红移加载代码。我们稍后将在 terraform 代码中展示，但重要的是要注意，每当新的对象/键保存在 S3 桶上时，S3 桶将被配置为触发/调用我们的 Lambda 函数。这个触发器对于整个 bucket 是活动的，这意味着任何复制到 Bucket 中的新文件都将触发我们的 Lambda 函数。

AWS 将把触发该函数的桶以及新的 S3 键传递给 lambda 函数的参数。现在有一个非常重要的假设你需要知道，即我们假设这个键是用一个给定的前缀创建的，这个前缀与你想要导入的目标红移表相同。例如，如果将一个新文件复制到“kpis/new_file.csv”中，lambda 将在“/”上拆分，并在 Redshift 中使用“kpis”作为表名。好了，你已经准备好看代码了。