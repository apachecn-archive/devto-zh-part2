# ActiveRecord 与 Ecto 第二部分

> 原文：<https://dev.to/appsignal/activerecord-vs-ecto-part-two-3001>

这是“ActiveRecord vs. Ecto”系列的第二部分，蝙蝠侠和蝙蝠女侠为查询数据库而战，我们比较苹果和橘子。

在 [ActiveRecord 与 Ecto 第一部分](https://blog.appsignal.com/2018/09/28/active-record-vs-ecto.html)中研究了数据库模式和迁移之后，这篇文章介绍了 ActiveRecord 和 Ecto 如何使开发人员能够查询数据库，以及 ActiveRecord 和 Ecto 在处理相同需求时如何进行比较。一路走来，我们还将找出蝙蝠女侠 1989-2011 年的身份。

## 种子数据

我们开始吧！基于本系列[第一篇](%5Bhttps://blog.appsignal.com/2018/09/28/active-record-vs-ecto.html%5D)中定义的数据库结构，假设`users`和`invoices`表中存储了以下数据:

**用户**

| 身份证明（identification） | 全名 | 电子邮件 | 创建于* | 更新数据 |
| --- | --- | --- | --- | --- |
| one | 更好的凯恩 | [bette@kane.test](mailto:bette@kane.test) | 2018-01-01 10:01:00 | 2018-01-01 10:01:00 |
| Two | 芭芭拉·戈登 | [芭芭拉@戈登. test](mailto:barbara@gordon.test) | 2018-01-02 10:02:00 | 2018-01-02 10:02:00 |
| three | 卡珊德拉·该隐 | [cassandra@cain.test](mailto:cassandra@cain.test) | 2018-01-03 10:03:00 | 2018-01-03 10:03:00 |
| four | 斯蒂芬妮·布朗 | stephanie@brown.test | 2018-01-04 10:04:00 | 2018-01-04 10:04:00 |

* ActiveRecord 的`created_at`字段在 Ecto 中默认命名为`inserted_at`。

**发票**

| 身份证明（identification） | 用户标识 | 支付方式 | 支付时间 | 创建于* | 更新数据 |
| --- | --- | --- | --- | --- | --- |
| one | one | 信用卡 | 2018-02-01 08:00:00 | 2018-01-02 08:00:00 | 2018-01-02 08:00:00 |
| Two | Two | 贝宝 | 2018-02-01 08:00:00 | 2018-01-03 08:00:00 | 2018-01-03 08:00:00 |
| three | three |  |  | 2018-01-04 08:00:00 | 2018-01-04 08:00:00 |
| four | four |  |  | 2018-01-05 08:00:00 | 2018-01-05 08:00:00 |

* ActiveRecord 的`created_at`字段在 Ecto 中默认命名为`inserted_at`。

通过本帖执行的查询假设上述数据存储在数据库中，因此在阅读时请记住这些信息。

## 使用主键查找项目

让我们从使用主键从数据库获取记录开始。

### [激活记录](#activerecord)

```
irb(main):001:0>  User.find(1)
User Load (0.4ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = $1 LIMIT $2  [["id", 1], ["LIMIT", 1]] => #<User id: 1, full_name: "Bette Kane", email: "bette@kane.test", created_at: "2018-01-01 10:01:00", updated_at: "2018-01-01 10:01:00"> 
```

Enter fullscreen mode Exit fullscreen mode

### Ecto

```
iex(3)> Repo.get(User, 1)
[debug] QUERY OK source="users" db=5.2ms decode=2.5ms queue=0.1ms
SELECT u0."id", u0."full_name", u0."email", u0."inserted_at", u0."updated_at" FROM "users" AS u0 WHERE (u0."id" = $1) [1]
%Financex.Accounts.User{
  __meta__: #Ecto.Schema.Metadata<:loaded, "users">,
  email: "bette@kane.test",
  full_name: "Bette Kane",
  id: 1,
  inserted_at: ~N[2018-01-01 10:01:00.000000],
  invoices: #Ecto.Association.NotLoaded<association :invoices is not loaded>,
  updated_at: ~N[2018-01-01 10:01:00.000000]
} 
```

Enter fullscreen mode Exit fullscreen mode

### 比较

这两种情况非常相似。ActiveRecord 依赖于`User`模型类的`find`类方法。这意味着每个 ActiveRecord 子类都有自己的`find`方法。

Ecto 使用一种不同的方法，依靠[库](https://martinfowler.com/eaaCatalog/repository.html)概念作为映射层和域之间的中介。当使用 Ecto 时，`User`模块不知道如何找到自己。这种职责存在于`Repo`模块中，该模块能够将其映射到底层的数据存储，在我们的例子中是 Postgres。

当比较 SQL 查询本身时，我们可以发现一些差异:

*   ActiveRecord 加载所有字段(`users.*`)，而 Ecto 只加载在`schema`定义中列出的字段。
*   ActiveRecord 在查询中包含了一个`LIMIT 1`，而 Ecto 没有。

## 抓取所有项目

让我们更进一步，从数据库中加载所有用户。

### [激活记录](#activerecord)

```
irb(main):001:0>  User.all
User Load (0.5ms)  SELECT  "users".* FROM "users" LIMIT $1  [["LIMIT", 11]] => #<ActiveRecord::Relation [#<User id: 1, full_name: "Bette Kane", email: "bette@kane.test", created_at: "2018-01-01 10:01:00", updated_at: "2018-01-01 10:01:00">, #<User id: 2, full_name: "Barbara Gordon", email: "barbara@gordon.test", created_at: "2018-01-02 10:02:00", updated_at: "2018-01-02 10:02:00">, #<User id: 3, full_name: "Cassandra Cain", email: "cassandra@cain.test", created_at: "2018-01-03 10:03:00", updated_at: "2018-01-03 10:03:00">, #<User id: 4, full_name: "Stephanie Brown", email: "stephanie@brown.test", created_at: "2018-01-04 10:04:00", updated_at: "2018-01-04 10:04:00">]> 
```

Enter fullscreen mode Exit fullscreen mode

### Ecto

```
iex(4)> Repo.all(User)
[debug] QUERY OK source="users" db=2.8ms decode=0.2ms queue=0.2ms
SELECT u0."id", u0."full_name", u0."email", u0."inserted_at", u0."updated_at" FROM "users" AS u0 []
[
  %Financex.Accounts.User{
    __meta__: #Ecto.Schema.Metadata<:loaded, "users">,
    email: "bette@kane.test",
    full_name: "Bette Kane",
    id: 1,
    inserted_at: ~N[2018-01-01 10:01:00.000000],
    invoices: #Ecto.Association.NotLoaded<association :invoices is not loaded>,
    updated_at: ~N[2018-01-01 10:01:00.000000]
  },
  %Financex.Accounts.User{
    __meta__: #Ecto.Schema.Metadata<:loaded, "users">,
    email: "barbara@gordon.test",
    full_name: "Barbara Gordon",
    id: 2,
    inserted_at: ~N[2018-01-02 10:02:00.000000],
    invoices: #Ecto.Association.NotLoaded<association :invoices is not loaded>,
    updated_at: ~N[2018-01-02 10:02:00.000000]
  },
  %Financex.Accounts.User{
    __meta__: #Ecto.Schema.Metadata<:loaded, "users">,
    email: "cassandra@cain.test",
    full_name: "Cassandra Cain",
    id: 3,
    inserted_at: ~N[2018-01-03 10:03:00.000000],
    invoices: #Ecto.Association.NotLoaded<association :invoices is not loaded>,
    updated_at: ~N[2018-01-03 10:03:00.000000]
  },
  %Financex.Accounts.User{
    __meta__: #Ecto.Schema.Metadata<:loaded, "users">,
    email: "stephanie@brown.test",
    full_name: "Stephanie Brown",
    id: 4,
    inserted_at: ~N[2018-01-04 10:04:00.000000],
    invoices: #Ecto.Association.NotLoaded<association :invoices is not loaded>,
    updated_at: ~N[2018-01-04 10:04:00.000000]
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

### 比较

它遵循与上一节完全相同的模式。ActiveRecord 使用`all`类方法，而 Ecto 依赖于存储库模式来加载记录。

SQL 查询中也有一些不同之处:

*   与上一节相同，ActiveRecord 加载所有字段(`users.*`)，而 Ecto 只加载`schema`定义中列出的字段。
*   ActiveRecord 还定义了一个`LIMIT 11`，而 Ecto 只是简单地加载一切。这个限制来自于控制台上使用的`inspect`方法([https://github . com/rails/rails/blob/master/active record/lib/active _ record/relation . Rb # L599](https://github.com/rails/rails/blob/master/activerecord/lib/active_record/relation.rb#L599))。

## 带条件查询

我们不太可能需要从一个表中获取所有记录。一个常见的需求是使用条件来过滤掉返回的数据。

让我们用那个例子来列出所有还未支付的`invoices`(`WHERE paid_at IS NULL`)。

### [激活记录](#activerecord)

```
irb(main):024:0>  Invoice.where(paid_at: nil)
Invoice Load (18.2ms)  SELECT  "invoices".* FROM "invoices" WHERE "invoices"."paid_at" IS NULL LIMIT $1  [["LIMIT", 11]] => #<ActiveRecord::Relation [#<Invoice id: 3, user_id: 3, payment_method: nil, paid_at: nil, created_at: "2018-01-04 08:00:00", updated_at: "2018-01-04 08:00:00">, #<Invoice id: 4, user_id: 4, payment_method: nil, paid_at: nil, created_at: "2018-01-05 08:00:00", updated_at: "2018-01-05 08:00:00">]> 
```

Enter fullscreen mode Exit fullscreen mode

### Ecto

```
iex(19)> where(Invoice, [i], is_nil(i.paid_at)) |> Repo.all()
[debug] QUERY OK source="invoices" db=20.2ms
SELECT i0."id", i0."payment_method", i0."paid_at", i0."user_id", i0."inserted_at", i0."updated_at" FROM "invoices" AS i0 WHERE (i0."paid_at" IS NULL) []
[
  %Financex.Accounts.Invoice{
    __meta__: #Ecto.Schema.Metadata<:loaded, "invoices">,
    id: 3,
    inserted_at: ~N[2018-01-04 08:00:00.000000],
    paid_at: nil,
    payment_method: nil,
    updated_at: ~N[2018-01-04 08:00:00.000000],
    user: #Ecto.Association.NotLoaded<association :user is not loaded>,
    user_id: 3
  },
  %Financex.Accounts.Invoice{
    __meta__: #Ecto.Schema.Metadata<:loaded, "invoices">,
    id: 4,
    inserted_at: ~N[2018-01-04 08:00:00.000000],
    paid_at: nil,
    payment_method: nil,
    updated_at: ~N[2018-01-04 08:00:00.000000],
    user: #Ecto.Association.NotLoaded<association :user is not loaded>,
    user_id: 4
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

### 比较

在这两个例子中，都使用了关键字`where`，它是 SQL `WHERE`子句的一个连接。尽管生成的 SQL 查询非常相似，但是这两种工具的实现方式有一些重要的不同。

ActiveRecord 自动将`paid_at: nil`参数转换为`paid_at IS NULL` SQL 语句。为了使用 Ecto 得到相同的输出，开发人员需要通过调用`is_nil()`来更加明确他们的意图。

另一个需要强调的区别是 Ecto 中函数`where`的“纯”行为。单独调用`where`函数时，并不会不与数据库交互。`where`函数的返回是一个`Ecto.Query`结构:

```
iex(20)> where(Invoice, [i], is_nil(i.paid_at))
#Ecto.Query<from i in Financex.Accounts.Invoice, where: is_nil(i.paid_at)> 
```

Enter fullscreen mode Exit fullscreen mode

只有当调用`Repo.all()`函数时，数据库才会被触动，将`Ecto.Query` struct 作为参数传递。这种方法允许在 Ecto 中进行查询组合，这是下一节的主题。

## 查询作文

数据库查询最强大的方面之一是组合。它以包含多个条件的方式描述查询。

如果您正在构建原始 SQL 查询，这意味着您可能会使用某种连接。假设你有两个条件:

1.  `not_paid = 'paid_at IS NOT NULL'`
2.  `paid_with_paypal = 'payment_method = "Paypal"'`

为了使用原始 SQL 将这两个条件结合起来，意味着您必须使用类似于以下内容的内容将它们连接起来:

```
SELECT * FROM invoices WHERE #{not_paid} AND #{paid_with_paypal} 
```

Enter fullscreen mode Exit fullscreen mode

幸运的是，ActiveRecord 和 Ecto 都有解决方案。

### [激活记录](#activerecord)

```
irb(main):003:0>  Invoice.where.not(paid_at: nil).where(payment_method: "Paypal")
Invoice Load (8.0ms)  SELECT  "invoices".* FROM "invoices" WHERE "invoices"."paid_at" IS NOT NULL AND "invoices"."payment_method" = $1 LIMIT $2  [["payment_method", "Paypal"], ["LIMIT", 11]] => #<ActiveRecord::Relation [#<Invoice id: 2, user_id: 2, payment_method: "Paypal", paid_at: "2018-02-01 08:00:00", created_at: "2018-01-03 08:00:00", updated_at: "2018-01-03 08:00:00">]> 
```

Enter fullscreen mode Exit fullscreen mode

### Ecto

```
iex(6)> Invoice |> where([i], not is_nil(i.paid_at)) |> where([i], i.payment_method == "Paypal") |> Repo.all()
[debug] QUERY OK source="invoices" db=30.0ms decode=0.6ms queue=0.2ms
SELECT i0."id", i0."payment_method", i0."paid_at", i0."user_id", i0."inserted_at", i0."updated_at" FROM "invoices" AS i0 WHERE (NOT (i0."paid_at" IS NULL)) AND (i0."payment_method" = 'Paypal') []
[
  %Financex.Accounts.Invoice{
    __meta__: #Ecto.Schema.Metadata<:loaded, "invoices">,
    id: 2,
    inserted_at: ~N[2018-01-03 08:00:00.000000],
    paid_at: #DateTime<2018-02-01 08:00:00.000000Z>,
    payment_method: "Paypal",
    updated_at: ~N[2018-01-03 08:00:00.000000],
    user: #Ecto.Association.NotLoaded<association :user is not loaded>,
    user_id: 2
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

### 比较

两个查询都在回答同一个问题:“哪些发票是用 Paypal 支付的？”。

正如预期的那样，ActiveRecord 提供了一种更简洁的方式来编写查询(对于那个例子)，而 Ecto 要求开发人员在编写查询上多花一点时间。像往常一样，Batgirl(孤儿，卡珊德拉·该隐身份的哑巴)或 Activerecord 并不冗长。

不要被上面显示的 Ecto 查询的冗长和明显的复杂性所迷惑。在真实世界环境中，该查询将被重写为类似于:

```
Invoice
|> where([i], not is_nil(i.paid_at))
|> where([i], i.payment_method == "Paypal")
|> Repo.all() 
```

Enter fullscreen mode Exit fullscreen mode

从这个角度来看，函数`where`(它本身不执行数据库操作)的“纯”方面与管道操作符的结合，使得 Ecto 中的查询组合非常干净。

## 订购

排序是查询的一个重要方面。它使开发人员能够确保给定的查询结果遵循指定的顺序。

### [激活记录](#activerecord)

```
irb(main):002:0>  Invoice.order(created_at: :desc)
Invoice Load (1.5ms)  SELECT  "invoices".* FROM "invoices" ORDER BY "invoices"."created_at" DESC LIMIT $1  [["LIMIT", 11]] => #<ActiveRecord::Relation [#<Invoice id: 4, user_id: 4, payment_method: nil, paid_at: nil, created_at: "2018-01-05 08:00:00", updated_at: "2018-01-05 08:00:00">, #<Invoice id: 3, user_id: 3, payment_method: nil, paid_at: nil, created_at: "2018-01-04 08:00:00", updated_at: "2018-01-04 08:00:00">, #<Invoice id: 2, user_id: 2, payment_method: "Paypal", paid_at: "2018-02-01 08:00:00", created_at: "2018-01-03 08:00:00", updated_at: "2018-01-03 08:00:00">, #<Invoice id: 1, user_id: 1, payment_method: "Credit Card", paid_at: "2018-02-01 08:00:00", created_at: "2018-01-02 08:00:00", updated_at: "2018-01-02 08:00:00">]> 
```

Enter fullscreen mode Exit fullscreen mode

### Ecto

```
iex(6)> order_by(Invoice, desc: :inserted_at) |> Repo.all()
[debug] QUERY OK source="invoices" db=19.8ms
SELECT i0."id", i0."payment_method", i0."paid_at", i0."user_id", i0."inserted_at", i0."updated_at" FROM "invoices" AS i0 ORDER BY i0."inserted_at" DESC []
[
  %Financex.Accounts.Invoice{
    __meta__: #Ecto.Schema.Metadata<:loaded, "invoices">,
    id: 3,
    inserted_at: ~N[2018-01-04 08:00:00.000000],
    paid_at: nil,
    payment_method: nil,
    updated_at: ~N[2018-01-04 08:00:00.000000],
    user: #Ecto.Association.NotLoaded<association :user is not loaded>,
    user_id: 3
  },
  %Financex.Accounts.Invoice{
    __meta__: #Ecto.Schema.Metadata<:loaded, "invoices">,
    id: 4,
    inserted_at: ~N[2018-01-04 08:00:00.000000],
    paid_at: nil,
    payment_method: nil,
    updated_at: ~N[2018-01-04 08:00:00.000000],
    user: #Ecto.Association.NotLoaded<association :user is not loaded>,
    user_id: 4
  },
  %Financex.Accounts.Invoice{
    __meta__: #Ecto.Schema.Metadata<:loaded, "invoices">,
    id: 2,
    inserted_at: ~N[2018-01-03 08:00:00.000000],
    paid_at: #DateTime<2018-02-01 08:00:00.000000Z>,
    payment_method: "Paypal",
    updated_at: ~N[2018-01-03 08:00:00.000000],
    user: #Ecto.Association.NotLoaded<association :user is not loaded>,
    user_id: 2
  },
  %Financex.Accounts.Invoice{
    __meta__: #Ecto.Schema.Metadata<:loaded, "invoices">,
    id: 1,
    inserted_at: ~N[2018-01-02 08:00:00.000000],
    paid_at: #DateTime<2018-02-01 08:00:00.000000Z>,
    payment_method: "Credit Card",
    updated_at: ~N[2018-01-02 08:00:00.000000],
    user: #Ecto.Association.NotLoaded<association :user is not loaded>,
    user_id: 1
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

### 比较

在这两种工具中，向查询添加订单都非常简单。

尽管 Ecto 示例使用了一个`Invoice`作为第一个参数，但是`order_by`函数也接受`Ecto.Query`结构，这使得`order_by`函数可以用于组合，比如:

```
Invoice
|> where([i], not is_nil(i.paid_at))
|> where([i], i.payment_method == "Paypal")
|> order_by(desc: :inserted_at)
|> Repo.all() 
```

Enter fullscreen mode Exit fullscreen mode

## 限制

什么是没有限制的数据库？一场灾难。幸运的是，ActiveRecord 和 Ecto 都有助于限制返回记录的数量。

### [激活记录](#activerecord)

```
irb(main):004:0>  Invoice.limit(2)
Invoice Load (0.2ms)  SELECT  "invoices".* FROM "invoices" LIMIT $1  [["LIMIT", 2]] => #<ActiveRecord::Relation [#<Invoice id: 1, user_id: 1, payment_method: "Credit Card", paid_at: "2018-02-01 08:00:00", created_at: "2018-01-02 08:00:00", updated_at: "2018-01-02 08:00:00">, #<Invoice id: 2, user_id: 2, payment_method: "Paypal", paid_at: "2018-02-01 08:00:00", created_at: "2018-01-03 08:00:00", updated_at: "2018-01-03 08:00:00">]> 
```

Enter fullscreen mode Exit fullscreen mode

### Ecto

```
iex(22)> limit(Invoice, 2) |> Repo.all()
[debug] QUERY OK source="invoices" db=3.6ms
SELECT i0."id", i0."payment_method", i0."paid_at", i0."user_id", i0."inserted_at", i0."updated_at" FROM "invoices" AS i0 LIMIT 2 []
[
  %Financex.Accounts.Invoice{
    __meta__: #Ecto.Schema.Metadata<:loaded, "invoices">,
    id: 1,
    inserted_at: ~N[2018-01-02 08:00:00.000000],
    paid_at: #DateTime<2018-02-01 08:00:00.000000Z>,
    payment_method: "Credit Card",
    updated_at: ~N[2018-01-02 08:00:00.000000],
    user: #Ecto.Association.NotLoaded<association :user is not loaded>,
    user_id: 1
  },
  %Financex.Accounts.Invoice{
    __meta__: #Ecto.Schema.Metadata<:loaded, "invoices">,
    id: 2,
    inserted_at: ~N[2018-01-03 08:00:00.000000],
    paid_at: #DateTime<2018-02-01 08:00:00.000000Z>,
    payment_method: "Paypal",
    updated_at: ~N[2018-01-03 08:00:00.000000],
    user: #Ecto.Association.NotLoaded<association :user is not loaded>,
    user_id: 2
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

### 比较

ActiveRecord 和 Ecto 都有限制查询返回的记录数量的方法。

Ecto 的`limit`与`order_by`的工作方式类似，适用于查询组合。

## 联想

当谈到如何处理关联时，ActiveRecord 和 Ecto 有不同的方法。

### [激活记录](#activerecord)

在 ActiveRecord 中，您可以使用模型中定义的任何关联，而不必对此做任何特殊处理，例如:

```
irb(main):012:0>  user = User.find(2)
User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = $1 LIMIT $2  [["id", 2], ["LIMIT", 1]] => #<User id: 2, full_name: "Barbara Gordon", email: "barbara@gordon.test", created_at: "2018-01-02 10:02:00", updated_at: "2018-01-02 10:02:00">
irb(main):013:0>  user.invoices
Invoice Load (0.4ms)  SELECT  "invoices".* FROM "invoices" WHERE "invoices"."user_id" = $1 LIMIT $2  [["user_id", 2], ["LIMIT", 11]] => #<ActiveRecord::Associations::CollectionProxy [#<Invoice id: 2, user_id: 2, payment_method: "Paypal", paid_at: "2018-02-01 08:00:00", created_at: "2018-01-03 08:00:00", updated_at: "2018-01-03 08:00:00">]> 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子表明，当调用`user.invoices`时，我们可以获得用户发票的列表。执行此操作时，ActiveRecord 会自动查询数据库并加载与用户相关联的发票。虽然这种方法使事情变得更简单，因为编写的代码更少，或者不必担心额外的步骤，但是如果要迭代多个用户并获取每个用户的发票，这可能会是一个问题。这个问题被称为“N + 1 问题”。

在 ActiveRecord 中，对“N + 1 问题”的建议修复是使用`includes`方法:

```
irb(main):022:0>  user = User.includes(:invoices).find(2)
User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = $1 LIMIT $2  [["id", 2], ["LIMIT", 1]]
Invoice Load (0.6ms)  SELECT "invoices".* FROM "invoices" WHERE "invoices"."user_id" = $1  [["user_id", 2]] => #<User id: 2, full_name: "Barbara Gordon", email: "barbara@gordon.test", created_at: "2018-01-02 10:02:00", updated_at: "2018-01-02 10:02:00">
irb(main):023:0>  user.invoices
=> #<ActiveRecord::Associations::CollectionProxy [#<Invoice id: 2, user_id: 2, payment_method: "Paypal", paid_at: "2018-02-01 08:00:00", created_at: "2018-01-03 08:00:00", updated_at: "2018-01-03 08:00:00">]> 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，ActiveRecord 在获取用户时会立即加载`invoices`关联(如所示的两个 SQL 查询所示)。

### Ecto

你可能已经注意到了，埃克托真的不喜欢魔术或含蓄。它要求开发者明确他们的意图。

让我们尝试一下同样的方法，将`user.invoices`与
一起使用

```
iex(7)> user = Repo.get(User, 2)
[debug] QUERY OK source="users" db=18.3ms decode=0.6ms
SELECT u0."id", u0."full_name", u0."email", u0."inserted_at", u0."updated_at" FROM "users" AS u0 WHERE (u0."id" = $1) [2]
%Financex.Accounts.User{
  __meta__: #Ecto.Schema.Metadata<:loaded, "users">,
  email: "barbara@gordon.test",
  full_name: "Barbara Gordon",
  id: 2,
  inserted_at: ~N[2018-01-02 10:02:00.000000],
  invoices: #Ecto.Association.NotLoaded<association :invoices is not loaded>,
  updated_at: ~N[2018-01-02 10:02:00.000000]
}
iex(8)> user.invoices
#Ecto.Association.NotLoaded<association :invoices is not loaded> 
```

Enter fullscreen mode Exit fullscreen mode

结果是一个`Ecto.Association.NotLoaded`。没那么有用。

要访问发票，开发人员需要使用`preload`函数:
让 Ecto 知道这一点

```
iex(12)> user = preload(User, :invoices) |> Repo.get(2)
[debug] QUERY OK source="users" db=11.8ms
SELECT u0."id", u0."full_name", u0."email", u0."inserted_at", u0."updated_at" FROM "users" AS u0 WHERE (u0."id" = $1) [2]
[debug] QUERY OK source="invoices" db=4.2ms
SELECT i0."id", i0."payment_method", i0."paid_at", i0."user_id", i0."inserted_at", i0."updated_at", i0."user_id" FROM "invoices" AS i0 WHERE (i0."user_id" = $1) ORDER BY i0."user_id" [2]
%Financex.Accounts.User{
  __meta__: #Ecto.Schema.Metadata<:loaded, "users">,
  email: "barbara@gordon.test",
  full_name: "Barbara Gordon",
  id: 2,
  inserted_at: ~N[2018-01-02 10:02:00.000000],
  invoices: [
    %Financex.Accounts.Invoice{
      __meta__: #Ecto.Schema.Metadata<:loaded, "invoices">,
      id: 2,
      inserted_at: ~N[2018-01-03 08:00:00.000000],
      paid_at: #DateTime<2018-02-01 08:00:00.000000Z>,
      payment_method: "Paypal",
      updated_at: ~N[2018-01-03 08:00:00.000000],
      user: #Ecto.Association.NotLoaded<association :user is not loaded>,
      user_id: 2
    }
  ],
  updated_at: ~N[2018-01-02 10:02:00.000000]
}

iex(15)> user.invoices
[
  %Financex.Accounts.Invoice{
    __meta__: #Ecto.Schema.Metadata<:loaded, "invoices">,
    id: 2,
    inserted_at: ~N[2018-01-03 08:00:00.000000],
    paid_at: #DateTime<2018-02-01 08:00:00.000000Z>,
    payment_method: "Paypal",
    updated_at: ~N[2018-01-03 08:00:00.000000],
    user: #Ecto.Association.NotLoaded<association :user is not loaded>,
    user_id: 2
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

与 ActiveRecord `includes`类似，preload with 获取关联的`invoices`，这将使它们在调用`user.invoices`时可用。

### 比较

ActiveRecord 和 Ecto 之间的战斗再次以一个众所周知的观点结束:显式性。这两个工具都使开发人员能够轻松地访问关联，但是虽然 ActiveRecord 使它不那么冗长，但是它的结果可能会有意想不到的行为。Ecto 遵循 WYSIWYG 类型的方法，只做开发人员定义的查询中看到的事情。

Rails 以在应用程序的所有不同层使用和推广缓存策略而闻名。一个例子是关于使用“俄罗斯娃娃”缓存方法，它完全依靠“N + 1 问题”来实现其缓存机制。

## 验证

ActiveRecord 中的大多数验证也可以在 Ecto 中使用。下面是常见验证的列表，以及 ActiveRecord 和 Ecto 如何定义它们:

| ActiveRecord | 埃克托 |
| --- | --- |
| `validates :title, presence: true` | `validate_required(changeset, [:title])` |
| `validates :email, confirmation: true` | `validate_confirmation(changeset, :email)` |
| `validates :email, format: {with: /@/` } | `validate_format(changeset, :email, ~r/@/)` |
| `validates :start, exclusion: {in: %w(a b)}` | `validate_exclusion(changeset, :start, ~w(a b))` |
| `validates :start, inclusion: {in: %w(a b)}` | `validate_inclusion(changeset, :start, ~w(a b))` |
| `validates :terms_of_service, acceptance: true` | `validate_acceptance(changeset, :terms_of_service)` |
| `validates :password, length: {is: 6}` | `validate_length(changeset, :password, is: 6)` |
| `validates :age, numericality: {equal_to: 1}` | `validate_number(changeset, :age, equal_to: 1)` |

## 总结起来

现在你有了:基本的苹果和橘子的比较。

ActiveRecord 侧重于执行数据库查询的便利性。它的绝大部分特性都集中在模型类本身，不需要开发人员对数据库有深入的理解，也不需要这些操作的影响。默认情况下，ActiveRecord 隐式地做了很多事情。虽然这使开始变得更容易，但它使理解幕后发生的事情变得更难，而且只有当你遵循“活动记录方式”时，它才会起作用。

另一方面，Ecto 要求显式性，这会导致代码更加冗长。作为一个好处，一切都在聚光灯下，没有幕后，你可以指定自己的方式。

两者都有其优势，这取决于你的视角和偏好。比较了苹果和桔子之后，我们就到了这个话题的结尾。差点忘了告诉你 bat girl(1989-2001)的代号是....甲骨文。但是我们不要深入讨论这个问题。😉

*本文由客座作者[埃尔维奥·维科萨](http://www.elviovicosa.com)撰写。埃尔维奥是《为 Rails 开发者准备的[凤凰》一书的作者。](http://www.phoenixforrailsdevelopers.com)*