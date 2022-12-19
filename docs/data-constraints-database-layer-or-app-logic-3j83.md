# 数据约束:数据库层还是 app 逻辑？

> 原文：<https://dev.to/shalvah/data-constraints-database-layer-or-app-logic-3j83>

我曾经非常相信使用 SQL 约束来确保数据完整性。我主要谈论的是像管理模型之间关系的`FOREIGN KEY`和确保唯一性的`UNIQUE`这样的约束。现在我在想，通过 ORM 和验证器，在应用程序逻辑本身中强制这样做是否更好。

我对 SQL 约束最大的不满是:

1.  *很难捕捉和应对特定的错误。*大多数时候，DB 层抛出一个普通的异常(PHP 为`PDOException`),实际的错误消息是一个字符串。如果扔出一个`UniqueConstraintViolationException`或`ForeignKeyConstraintViolation`，那就简单多了。
2.  当运行涉及操作数据库的手动测试时，有时数据库约束真的很棘手。

最近，我更倾向于在应用程序逻辑中加强我的关系。我仍然在数据库上保留唯一的约束，但是我首先手工检查重复的约束。

请分享你的想法和经历。