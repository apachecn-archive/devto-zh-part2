# ActiveRecord 与 Ecto -第一部分

> 原文：<https://dev.to/appsignal/activerecord-vs-ecto---part-one-1l7m>

数据是大多数软件应用程序的核心部分。在开发人员的生活中，映射和查询数据库中的数据是一项重复的任务。因此，理解过程并能够使用简化任务的抽象是很重要的。

在这篇文章中，两个系列中的第一个，你会发现 [ActiveRecord](http://guides.rubyonrails.org/active_record_basics.html) (红宝石)和 [Ecto](https://hexdocs.pm/ecto/Ecto.html) (长生不老药)之间的比较。我们将看到这两个工具如何使开发人员能够迁移和映射数据库模式。

所以我们将比较苹果和橘子。(原文)从来不需要说一句话的 Batgirl 对蝙蝠侠，明确表示‘我是蝙蝠侠’。隐含的、约定胜于配置的与明确的意图。第一回合。战斗！

## [激活记录](#activerecord)

自从发布以来已经超过 10 年了，你可能已经听说过 active record——著名的 [ORM](https://en.wikipedia.org/wiki/Object-relational_mapping) ,默认情况下，它与 Ruby on Rails 项目一起发布。

> *ActiveRecord* 是 MVC 中的 M——模型——是负责表示业务数据和逻辑的系统层。ActiveRecord 方便了业务对象的创建和使用，这些业务对象的数据需要持久存储在数据库中。它是 ActiveRecord 模式的一个实现，active record 模式本身是一个对象关系映射系统的描述。

尽管众所周知 ActiveRecord 主要用于 Rails，但它也可以作为一个独立的工具，嵌入到其他项目中。

## Ecto

与 ActiveRecord 相比，Ecto 是一个相当新的(目前还没有那么出名)工具。它是用 Elixir 编写的，默认包含在 Phoenix 项目中。

与 ActiveRecord 不同，Ecto 不是一个 ORM，而是一个支持使用 Elixir 编写查询并与数据库交互的库。

> Ecto 是一种特定于领域的语言，用于编写查询并与 Elixir 中的数据库进行交互。

按照设计，Ecto 是一个独立的工具，用于不同的 Elixir 项目，不与任何框架相连。

## 你不是在比较苹果和橘子吗？

是的，我们是！尽管 ActiveRecord 和 Ecto 在语义上有所不同，但是 ActiveRecord 和 Ecto 都支持数据库迁移、数据库映射、查询和验证等常见功能。使用这两种工具我们可以达到同样的效果。对于那些对来自红宝石背景的长生不老药感兴趣的人，我们认为这将是一个有趣的比较。

## 发票系统

在这篇文章的其余部分，我们将使用一个假想的发票系统进行演示。让我们想象一下，我们有一个商店出售西装给超级英雄。为了简单起见，我们将只为发票系统准备两个表:*用户*和*发票*。

下面是这些表的结构及其字段和类型:

**用户**

| 田 | 类型 |
| --- | --- |
| 全名 | 线 |
| 电子邮件 | 线 |
| created _ at(active record)/inserted _ at(Ecto) | 日期时间 |
| 更新数据 | 日期时间 |

**发票**

| 田 | 类型 |
| --- | --- |
| 用户标识 | 整数 |
| 支付方式 | 线 |
| 支付时间 | 日期时间 |
| created _ at(active record)/inserted _ at(Ecto) | 日期时间 |
| 更新数据 | 日期时间 |

用户表有四个字段:*全名*、*电子邮件*、*更新时间*以及依赖于所用工具的第四个字段。ActiveRecord 创建一个 *created_at* 字段，而 Ecto 创建一个 *inserted_at* 字段来表示记录首次插入数据库时的时间戳。

第二个表名为*发票*。它有五个字段:*用户 id* 、*支付方式*、*支付日期*、*更新日期*，与用户表类似，根据使用的工具，要么是*创建日期*要么是*插入日期*。

“用户”和“发票”表具有以下关联:

*   一个用户有许多发票
*   发票属于一个用户

## 迁徙

迁移允许开发人员使用迭代过程，随着时间的推移轻松地发展他们的数据库模式。ActiveRecord 和 Ecto 都使开发人员能够使用高级语言(分别是 Ruby 和 Elixir)迁移数据库模式，而不是直接处理 [SQL](https://en.wikipedia.org/wiki/SQL) 。

让我们通过使用 ActiveRecord 和 Ecto 来创建 users 和 invoices 表，来看看迁移是如何工作的。

### ActiveRecord:创建用户表

**迁徙**

```
class CreateUsers < ActiveRecord::Migration[5.2]
  def change
    create_table :users do |t|
      t.string :full_name, null: false
      t.string :email, index: {unique: true}, null: false
      t.timestamps
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

ActiveRecord 迁移支持使用`create_table`方法创建表格。尽管在迁移文件中没有定义`created_at`和`updated_at`字段，但是使用`t.timestamps`会触发 ActiveRecord 来创建这两个字段。

**创建的表结构**

运行`CreateUsers`迁移后，创建的表将具有以下结构:

```
 Column   |            Type             | Nullable |              Default
------------+-----------------------------+----------+-----------------------------------
 id         | bigint                      | not null | nextval('users_id_seq'::regclass)
 full_name  | character varying           | not null |
 email      | character varying           | not null |
 created_at | timestamp without time zone | not null |
 updated_at | timestamp without time zone | not null |
Indexes:
    "users_pkey" PRIMARY KEY, btree (id)
    "index_users_on_email" UNIQUE, btree (email) 
```

Enter fullscreen mode Exit fullscreen mode

迁移还负责为电子邮件字段创建唯一的索引。选项`index: {unique: true}`被传递给电子邮件字段定义。这就是为什么该表将`"index_users_on_email" UNIQUE, btree (email)`索引列为其结构的一部分。

### Ecto:创建用户表

**迁徙**

```
defmodule Financex.Repo.Migrations.CreateUsers do
  use Ecto.Migration

  def change do
    create table(:users) do
      add :full_name, :string, null: false
      add :email, :string, null: false
      timestamps()
    end

    create index(:users, [:email], unique: true)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

Ecto 迁移结合了函数`create()`和`table()`来创建 users 表。Ecto 迁移文件与其 ActiveRecord 等效文件非常相似。在 ActiveRecord 中，时间戳字段(`created_at`和`updated_at`)由`t.timestamps`创建，而在 Ecto 中，时间戳字段(`inserted_at`和`updated_at`)由`timestamps()`函数创建。

这两种工具在创建索引的方式上有一点点不同。在 ActiveRecord 中，索引被定义为正在创建的字段的一个选项。Ecto 使用函数`create()`和`index()`的组合来实现这一点，这与使用组合来创建表格本身的方式一致。

**创建表格结构**

```
 Column    |            Type             | Nullable |              Default
-------------+-----------------------------+----------+-----------------------------------
 id          | bigint                      | not null | nextval('users_id_seq'::regclass)
 full_name   | character varying(255)      | not null |
 email       | character varying(255)      | not null |
 inserted_at | timestamp without time zone | not null |
 updated_at  | timestamp without time zone | not null |
Indexes:
    "users_pkey" PRIMARY KEY, btree (id)
    "users_email_index" UNIQUE, btree (email) 
```

Enter fullscreen mode Exit fullscreen mode

运行`Financex.Repo.Migrations.CreateUsers`迁移时创建的表与使用 ActiveRecord 创建的表具有相同的结构。

### ActiveRecord:创建`invoices`表格

**迁徙**

```
class CreateInvoices < ActiveRecord::Migration[5.2]
  def change
    create_table :invoices do |t|
      t.references :user
      t.string :payment_method
      t.datetime :paid_at
      t.timestamps
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这次迁移包括`t.references`方法，这在之前的迁移中是没有的。它用于创建对用户表的引用。如前所述，一个用户有许多发票，一张发票属于一个用户。`t.references`方法在发票表中创建一个`user_id`列来保存该引用。

**创建表格结构**

```
 Column     |            Type             | Nullable |               Default
----------------+-----------------------------+----------+--------------------------------------
 id             | bigint                      | not null | nextval('invoices_id_seq'::regclass)
 user_id        | bigint                      |          |
 payment_method | character varying           |          |
 paid_at        | timestamp without time zone |          |
 created_at     | timestamp without time zone | not null |
 updated_at     | timestamp without time zone | not null |
Indexes:
    "invoices_pkey" PRIMARY KEY, btree (id)
    "index_invoices_on_user_id" btree (user_id) 
```

Enter fullscreen mode Exit fullscreen mode

创建的表遵循与先前创建的表相同的模式。唯一的区别是一个额外的索引(`index_invoices_on_user_id`)，当使用`t.references`方法时，ActiveRecord 会自动添加这个索引。

### Ecto:创建`invoices`表格

**迁徙**

```
defmodule Financex.Repo.Migrations.CreateInvoices do
  use Ecto.Migration

  def change do
    create table(:invoices) do
      add :user_id, references(:users)
      add :payment_method, :string
      add :paid_at, :utc_datetime
      timestamps()
    end

    create index(:invoices, [:user_id])
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

通过使用`references()`函数，Ecto 还支持数据库引用的创建。与推断列名的 ActiveRecord 不同，Ecto 要求开发人员显式定义`user_id`列名。`references()`函数还要求开发人员显式定义引用所指向的表，在本例中是 users 表。

**创建表格结构**

```
 Column     |            Type             | Nullable |               Default
----------------+-----------------------------+----------+--------------------------------------
 id             | bigint                      | not null | nextval('invoices_id_seq'::regclass)
 user_id        | bigint                      |          |
 payment_method | character varying(255)      |          |
 paid_at        | timestamp without time zone |          |
 inserted_at    | timestamp without time zone | not null |
 updated_at     | timestamp without time zone | not null |

Indexes:
    "invoices_pkey" PRIMARY KEY, btree (id)
    "invoices_user_id_index" btree (user_id)
Foreign-key constraints:
    "invoices_user_id_fkey" FOREIGN KEY (user_id) REFERENCES users(id) 
```

Enter fullscreen mode Exit fullscreen mode

这两种迁移也非常相似。谈到处理`references`功能的方式，有一些不同之处:

1.  Ecto 为`user_id`字段(`"invoices_user_id_fkey" FOREIGN KEY (user_id) REFERENCES users(id)`)创建了一个外键约束，它维护了 users 和 invoices 表之间的引用完整性。

2.  ActiveRecord 自动为`user_id`列创建索引。Ecto 要求开发人员明确这一点。这就是为什么迁移有`create index(:invoices, [:user_id])`语句的原因。

## ActiveRecord:数据映射&关联

ActiveRecord 以其“约定胜于配置”的座右铭而闻名。默认情况下，它使用模型类名来推断数据库表名。默认情况下，名为`User`的类使用`users`表作为其源。ActiveRecord 还将表的所有列映射为实例属性。开发人员只需要定义表之间的关联。ActiveRecord 也使用这些来推断所涉及的类和表。

看看如何使用 ActiveRecord 映射 users 和 invoices 表:

**用户**

```
class User < ApplicationRecord
  has_many :invoices
end 
```

Enter fullscreen mode Exit fullscreen mode

**发票**

```
class Invoice < ApplicationRecord
  belongs_to :user
end 
```

Enter fullscreen mode Exit fullscreen mode

### Ecto:数据映射&关联

另一方面，Ecto 要求开发人员明确数据源及其字段。尽管 Ecto 有类似的`has_many`和`belongs_to`特性，但它也要求开发人员明确相关的表和用于处理该表模式的模式模块。

这就是 Ecto 映射用户和发票表的方式:

**用户**

```
defmodule Financex.Accounts.User do
  use Ecto.Schema

  schema "users" do
    field :full_name, :string
    field :email, :string
    has_many :invoices, Financex.Accounts.Invoice
    timestamps()
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

**发票**

```
defmodule Financex.Accounts.Invoice do
  use Ecto.Schema

  schema "invoices" do
    field :payment_method, :string
    field :paid_at, :utc_datetime
    belongs_to :user, Financex.Accounts.User
    timestamps()
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 总结起来

在这篇文章中，我们毫不犹豫地比较了苹果和橘子。我们比较了 ActiveRecord 和 Ecto 处理数据库迁移和映射的方式。一场隐含的沉默的原始蝙蝠女孩与明确的“我是蝙蝠侠”蝙蝠侠的战斗。

由于“约定胜于配置”，使用 ActiveRecord 通常需要较少的编写工作。Ecto 走的是相反的方向，要求开发人员更加明确他们的意图。总的来说，除了“代码更少”更好之外，ActiveRecord 还有一些最佳缺省值，使开发人员不必对所有事情都做出决策，也不必了解所有底层配置。对于初学者来说，ActiveRecord 是一个更合适的解决方案，因为只要你严格遵循它的标准，它就会默认做出“足够好”的决策。

Ecto 的显式方面使阅读和理解一段代码的行为变得更加容易，但它也要求开发人员更多地了解数据库属性和可用的特性。第一眼看上去，可能会让 Ecto 显得笨重，这是它的优点之一。根据我在 ActiveRecord 和 Ecto world 的个人经验，Ecto 的明确性消除了 ActiveRecord 项目中常见的“幕后”效果和不确定性。开发人员在代码中读到的是应用程序中发生的事情，没有隐含的行为。

在几周后的第二篇博客“ActiveRecord 与 Ecto”系列文章中，我们将介绍查询和验证在 ActiveRecord 和 Ecto 中是如何工作的。

我们很想知道你对这篇文章的看法。我们总是在寻找新的话题，所以如果你有想了解更多的话题，请不要犹豫，留下你的评论！

*本文由客座作者[埃尔维奥·维科萨](http://www.elviovicosa.com)撰写。埃尔维奥是《为 Rails 开发者准备的[凤凰》一书的作者。](http://www.phoenixforrailsdevelopers.com)*