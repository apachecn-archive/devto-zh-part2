# AWS 之路:发明 2018–每周预测，第 2 部分:数据 2.0

> 原文：<https://dev.to/dgreene/the-road-to-aws-reinvent-2018--weekly-predictions-part-2-data-20-4ie8>

原发表[此处](https://www.3pillarglobal.com/insights/the-road-to-amazon-web-services-reinvent-2018-data-2-0-weekly-predictions-part-2)。

上周，我做了一个简单的预测，在 re:Invent 上，AWS 将宣布更多所谓的“无服务器”功能。众所周知，他们全力以赴从服务器管理转向服务管理。我猜测了一些具体的可能性——SFTP 即服务、“无服务器”EC2 以及其他一些可能性。

本周，我想看看 AWS 提供的一些其他功能，并对我们可能会看到的公告做出一些预测。为什么这些对你来说很重要？如果您从事处理、存储和分析大型数据集的业务，这些更新可能会显著影响您的速度、效率和成本。

# 第 2 周预测:数据 2.0

虽然 AWS 有许多现有的工具来管理数据摄取和处理(例如，数据管道、Glue、Kinesis)，但我认为，在一个健壮的数据处理框架中添加一个针对所有步骤优化的编排框架，将真正使 AWS 的数据分析工具(Athena、QuickSight 等)大放异彩。

## 数据映射即服务

我在 WebMethods 这样的平台上进行数据集成。虽然它可能有一些缺点，但作为一个解决方案集，它在以下方面确实非常出色:

*   为数据传递提供端点
*   通过位置、格式或其它特定数据元素来标识数据
*   基于上述特性将数据路由到正确的处理器
*   每个数据条目从一种格式到另一种格式的映射
*   将转换后的数据传送到目标位置

我可以看到一个类似于托管 Apache NiFi 解决方案的等价物——类似于 AWS 的 ElasticSearch 服务。对于必须导入和处理数据文件(尤其是从第三方导入和处理数据文件)的产品来说，结合路由 Lambda 和/或 Fargate 执行的各种任务的能力、支持有向无环图(DAC)建模，以及作为最终和中间步骤将数据写入 S3 的紧密集成，将改变游戏规则。

## S3 生命周期上读时间

在 S3 生命周期管理中，我最讨厌的一点是，从标准存储类转移到非频繁访问存储类与访问文件的频率无关。虽然我认为对象存储的底层功能使得实际做到这一点非常困难，但它将提供做出存储决策所急需的度量。

## DYNAMODB 深度文档模式

DynamoDB 是一个很好的混合密钥和文档存储库。我经常用它来存储和检索小文档。然而，当前对文档大小和扫描模式的限制使得使用 DynamoDB 作为托管的 MongoDB 级解决方案是一个挑战。提供更健壮的以文档为中心的功能，同时仍然支持可伸缩性、复制和全球存在，这将大大提升 DynamoDB 的地位。作为 DynamoDB 的一个期望因素，我希望完全取消读写吞吐量的预分配。让每个请求设置一个可选的节流，但是对我实际使用的而不是我可能使用的进行收费。当前的自动缩放是一个重大的改进，但它还可以改进。

## RDS–多语言版本

有一段时间，有一种有趣的趋势，试图将多种数据库范式结合到一个视图中——结合文档+图形等。我认为 AWS 可能会尝试这种观点。通过在幕后将他们现有的一些产品结合在一起，将 ElasticSearch、Aurora 和 Neptune 结合在一起，形成一个试图结合每种存储模式优点的解决方案，将会很有意思。像大多数一体化工具一样，老实说，我不确定它的多种功能是否同样平庸。我经常为客户的数据推荐多存储解决方案——每一个都针对特定的用例进行了优化，因此可能会有所收获。

## S3 自动抓取和度量

想象一下，在一个数据桶上设置一个标志，这样每当一个数据文件放在那里时，它就被自动分类、索引，并为 Athena、Glue 或 Hive 查询做好准备。对其中的数据进行一些高级度量对于其他业务决策(行数、平均值等)会很有用。添加一些 SageMaker 算法来处理数据差异(例如，随机砍伐森林来发现数据异常值和/或趋势)以发出警报也是令人难以置信的。

## 包装完毕

在本周结束时，我认为将会有很多关于数据处理作为一个以 AWS 为中心的框架的不同公告。AWS 已经具备了大部分功能，让 AWS 管理它们的连接，这样您只需关注从数据中提取的商业价值，就能实现云对数据处理的承诺。

会在 re:Invent？请在下面留言，让我知道你希望在那里看到什么，或者你对下一步的想法。