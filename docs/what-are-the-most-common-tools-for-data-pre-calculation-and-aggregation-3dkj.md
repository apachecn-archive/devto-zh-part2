# 数据预计算和汇总最常用的工具有哪些？

> 原文：<https://dev.to/buinauskas/what-are-the-most-common-tools-for-data-pre-calculation-and-aggregation-3dkj>

我工作的公司做数据研究和搜集，然后汇总并发布给我们的客户。为了在 web 应用程序中提供更快的数据查找，我们还尝试去规范化数据。

到目前为止，我们使用 SQL Server 中的机制来完成这些聚合。但最近，这已成为一个瓶颈，流程需要太多时间来执行，并与工作时间重叠。

除了关系数据库之外，market 还使用哪些工具来执行聚合和预计算？我的发现包括:

*   Apache Hadoop MapReduce
*   阿帕奇猪
*   阿帕奇火花