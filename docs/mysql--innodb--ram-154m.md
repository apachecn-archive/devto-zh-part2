# MySQL : InnoDB:框架

> 原文：<https://dev.to/mrbenosborne/mysql--innodb--ram-154m>

InnoDB 表是否保存在 RAM 中，即使它们不经常被查询或者根本不被查询？

MySQL 是如何计算出在内存中保存哪个表的？

例如，如果我有 5 个表，并且这 5 个表都是 InnoDB，那么我只查询这 5 个表中的 2 个，其他 3 个表还会保存在内存中吗？