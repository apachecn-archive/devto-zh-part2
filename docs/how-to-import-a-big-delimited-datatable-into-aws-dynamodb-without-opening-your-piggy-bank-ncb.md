# 如何在不打开存钱罐的情况下将一个大的分隔数据表导入 AWS DynamoDB？

> 原文：<https://dev.to/djviolin/how-to-import-a-big-delimited-datatable-into-aws-dynamodb-without-opening-your-piggy-bank-ncb>

我在做一个无服务器的堆栈。

我有一个 5-6 百万行的分隔 CSV 文件。我想把这个导入 AWS DynamoDB。

有没有可能把它导入 AWS DynamoDB，而不会把我的月薪付之一炬？如何便宜的解决这个问题？

这个数据转储将不断更新，我想导入到 DB 的新行。我认为最有效的方法是从我的 DynamoDB 中创建一个数据转储，找到一个键，将其与新的第三方数据转储的键进行比较(这个键需要通过 regex 从一个列中提取)，然后只将新行导入到我的 DB 中。你有一些建议(大数据框架或一些 AWS 服务)可以帮助我吗？或者，如果有 shell 或 Go 脚本来做同样的事情，我也是开放的。

谢谢大家！