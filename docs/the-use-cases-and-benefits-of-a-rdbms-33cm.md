# RDBMS 的使用案例和优点

> 原文：<https://dev.to/revskill10/the-use-cases-and-benefits-of-a-rdbms-33cm>

使用案例:

*   您希望让您的业务不变量远离您的代码库。
*   从数据库中获取关系数据不需要连接。
*   您希望对数据进行事务性操作
*   您需要一种强大的查询语言来高效地获取您拥有的任何数据。
*   您希望利用 RDBMS 来管理所有安全问题。

让我来解释一下这些使用案例以及您可以获得的好处。

## 1。业务不变量:

例如，你正在开发一个微服务来管理你的用户。它将处理新用户注册。这里一个明显的业务不变量是:
电子邮件必须是唯一的
所以，你开始写测试，写代码来验证你的代码，对吗？
我想说，在这种情况下，您可以从 RDBMS (PostgreSQL)中获益，通过使用 SQL 语句在您的代码库之外强制执行这条规则:

```
CREATE TABLE unique_email_policy_records (
id serial NOT NULL,
email character varying not null,
CONSTRAINT unique_email_policy_records_pkey PRIMARY KEY (id)
);
CREATE UNIQUE INDEX unique_email_policy_records_keys ON unique_email_policy_records USING btree (email); 
```

Enter fullscreen mode Exit fullscreen mode

这个 sql 脚本将强制要求，当您想要向数据库中插入新用户时，它不能包含重复的电子邮件。您的代码中不需要强制执行这一点。

## 2。从数据库中获取关系数据不需要连接。

因此，在您已经将用户插入到数据库中之后，您会想要查询您拥有的用户。有很多方法可以达到这个目的。您可以使用 sql 触发器，这样每当用户注册时，它都会插入到新表中，用于查询。

```
CREATE TABLE user_records%SUFFIX% (
id serial NOT NULL,
email character varying not null,
encrypted_password character varying not null,
created_at TIMESTAMP not null,
updated_at TIMESTAMP,
CONSTRAINT user_records_pkey%SUFFIX% PRIMARY KEY (id)
); 
```

Enter fullscreen mode Exit fullscreen mode

该脚本将自动刷新任何新的有效用户注册。所以您查询这个表来将您的用户从数据库中取出！

## 3。您希望对数据进行事务性操作

我不太了解 NoSQL，但当我处理我写作时，我希望它是原子性的。这意味着，无论何时发生任何错误，都不会对我的数据库进行新的写入。
要么全有，要么全无
RDBMS 将帮助您实现这一目标，简单而高效！

## 4。您需要一种强大的查询语言来高效地获取您拥有的任何数据。

现在，您有了要查询的 users 表。你想把它交付给客户，你的用户，你的顾客。
您应该使用 GraphQL 来实现这一目的！更具体地说，有许多框架可以帮助你查询数据库。一个例子是 hasura，你需要做的就是运行 Hasura 并将其指向你的 RDBMS(在这种情况下是 PostgreSQL)
不需要更多的代码库来实现这个
好的一点是，你的客户端现在可以使用 GraphQL 查询语言来查询你的 RDBMS 中的数据！

## 5。您希望利用 RDBMS 来管理所有安全问题。

在您已经将数据交付给用户之后，最后要做的事情就是安全性。有了 Hasura(或任何其他类似的框架)，它将允许我们以一种非常灵活的方式在你的数据库上实施权限规则！
不需要放在你的代码库中！
我听说过许多关于 RDBMS 的戏剧，一切都是一种权衡，作为一名开发人员，你能做的最重要的事情是为工作选择正确的工具。这里没有银弹。
快乐编码！