# Azure 数据工厂和 SSIS 对比

> 原文：<https://dev.to/ameliaw933/azure-data-factory-and-ssis-compared--24ie>

当谈到 Azure Data Factory (ADF)以及它与 SSIS 的比较时，我看到了很多困惑。这不仅仅是“云中的 SSIS”。对于 ADF 的概述，我假设你知道 [SSIS](https://mindmajix.com/ssis-training) 。那么它们有什么不同呢？

SSIS 是一个提取-传输-加载工具，但 ADF 是一个提取-加载工具，因为它不在工具内进行任何转换，而是通过 ADF 调用 SQL 服务器上进行转换的存储过程，或调用 Hive 作业，或 Azure Data Lake Analytics 中的 U-SQL 作业来完成。更应该把它看作是一个编排工具。SSIS 有进行转换的额外好处，但请记住，任何转换的性能都取决于安装 SSIS 的服务器的能力，因为要转换的数据将被推送到 SSIS 服务器。其他主要差异:

1.  ADF 是一个基于云的服务(通过 Azure portal 中的 ADF 编辑器),因为它是一个 PaaS 工具，所以不需要硬件或任何安装。SSIS 是一个桌面工具(通过 SSDT ),需要一个很大的服务器，你必须管理，你必须安装 SSIS 的 SQL Server
2.  ADF 使用 JSON 脚本进行编排(编码)，而 SSIS 使用拖放任务(不编码)
3.  ADF 是通过 Azure 订阅的现收现付，SSIS 是作为 SQL Server 的一部分的许可费用
4.  ADF 可以启动 HDInsights 集群并运行 Pig 和 Hive 脚本。SSIS 也可以通过 Azure 功能包获得集成服务(SSIS)
5.  SSIS 有一个强大的图形用户界面，智能感知和调试。ADF 有一个基本的编辑器，没有智能感知或调试
6.  SSIS 通过 SSMS 管理，而 ADF 通过 Azure 门户管理
7.  SSIS 有更广泛的支持数据源和目的地
8.  SSIS 有一个编程 SDK，自动化通过 BIML，和第三方组件。* 9.ADF 没有编程 SDK，通过 PowerShell 实现自动化，没有第三方组件
9.  SSIS 有错误处理。ADF 没有
10.  ADF 有“数据血统”，标记和跟踪来自不同来源的数据。SSIS 没有这个

可以把 ADF 看作是 SSIS 的补充服务，它的主要用例局限于廉价地处理云中的大数据。

请注意，在加载大量数据时，尤其是在使用像 SQL 数据仓库这样的产品时，迁移到云需要您以不同的方式思考