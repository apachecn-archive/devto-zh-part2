# 当达到 100%的 CPU 使用率时，MySQL 会发生什么？

> 原文：<https://dev.to/mrbenosborne/what-happens-to-mysql-when-hitting-100-cpu-usage-1j4c>

大家好，我在 RDS (AWS)上运行 MySQL 服务器，我向数据库中发出了 5 个 INSERT 查询，每个查询大约需要 2 分钟。服务器的 CPU 占用率为 100%,这是意料之中的，但这是一件坏事吗？MySQL 服务器仍然可以很好地服务请求？