# 如何防止滥用公共 REST API 端点？

> 原文：<https://dev.to/inambe/how-to-prevent-misuse-of-a-public-rest-api-endpoint-46cb>

我有一个针对数据库表上 CRUD 操作的 REST API，其中插入记录不需要任何认证。问题是，我如何防止我 API 的端点被误用，因为任何人都可以使用一个简单的脚本在一分钟内输入数千行。

我用的是 Slim-PHP 和 JWT。

谢谢你。