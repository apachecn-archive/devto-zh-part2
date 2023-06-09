# 抄写员

> 原文：<https://dev.to/elcotu/copymaker-26i2>

大家好，
我想向你们展示一个我制作并决定在 GitHub 上发布的小工具。

我主要使用有自己数据库的遗留 ERP，很多时候我需要在表中创建新记录。但是在很多情况下，不得不在将一个 40 多个字段的表映射到我的一个对象(使用它们自己的 CRUD 行为)或生成原始 SQL 语句之间做出选择。
在第二种情况下，我最喜欢的策略之一是选择表的一个模型元素，并构建一个 SQL 查询，如下所示:

```
insert into TABLE
select Field1, Field2, Field3 from TABLE where Field1 = :Value 
```

Enter fullscreen mode Exit fullscreen mode

修改了这个 SQL，将 select 中的字段名替换为值:

```
insert into TABLE
select :NewId, 'New Value', Field3 from TABLE where Field1 = :Value 
```

Enter fullscreen mode Exit fullscreen mode

所以这个类来了， [CopyMaker](https://github.com/dcoturel/copymaker) ，它允许你:
1)添加字符串或整数值，通知它们覆盖哪个字段
2)为指定的表和指定的条件生成 SQL

我乐于接受建议，改进和这方面的任何事情。

你好，