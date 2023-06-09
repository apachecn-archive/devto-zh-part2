# 区分大小写的 WHERE 子句

> 原文：<https://dev.to/fitri/case-sensitive-where-clause-4860>

今天我在我的 URL shortener 应用程序中发现了一个 bug

我没有想到表格中的字段不会自动区分大小写。这是我的应用程序的一个问题，因为自动生成的 URL 使用 base62 (A-Za-z0-9)。“a1b2”将转发到与“A1B1”相同的链路。

我了解到这是 Laravel 默认数据库排序的预期行为。“UTF8_unicode_ci”不区分大小写。要改变这一点，我可以对整个数据库使用“UTF8_bin”。但是这给我的数据库引入了另一个特性。

所以在这种情况下，我更喜欢保留默认的字符集/排序规则，只对一个特定的表使用“UTF8_bin”。

来改变这种行为。我已经在我的迁移中添加了这两行:

```
$table->charset = 'utf8';
$table->collation = 'utf8_bin'; 
```

Enter fullscreen mode Exit fullscreen mode

现在一切都按计划进行。

或者，如果您使用的是 MySQL 版本 8+,您只需要下面一行:

```
$table->collation = 'utf8mb4_0900_as_cs' 
```

Enter fullscreen mode Exit fullscreen mode