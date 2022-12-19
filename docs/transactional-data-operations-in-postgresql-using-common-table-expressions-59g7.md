# PostgreSQL 中使用公共表表达式的事务性数据操作

> 原文：<https://dev.to/robconery/transactional-data-operations-in-postgresql-using-common-table-expressions-59g7>

PostgreSQL 有许多令人惊叹的特性，但往往被喜欢使用抽象来处理 SQL 和数据库的开发人员所忽视。这是一个大话题，显然会引发大量的争论。如果你以前看过我的博客，你会知道我非常不喜欢 ORMs，除了 LLBLGenPro，因为 Frans 是我的朋友，他不喜欢我诋毁 ORMs。

PostgreSQL 的一个重要特性是[通用表表达式，或 CTEs](https://www.postgresql.org/docs/10/static/queries-with.html) ，也称为“WITH queries”。这些是简单的链式 SQL 表达式，允许您将一个查询的结果传递给另一个函数式样式。

我经常用它们来做报告，但当有人从我这里买东西时，我也会用它们来创建订单。让我们看看怎么做。

## 设置数据库

让我们创建我的数据库的核心。这些表格是简化的，但是它们应该做的核心内容是存在的:

```
create extension if not exists pgcrypto;

create table orders(
  id serial primary key, 
  key uuid unique default gen_random_uuid(),
  email text not null, 
  total decimal(10,2),
  created_at timestamptz default now()  
);

create table order_items(
  id serial primary key,
  order_id int not null references orders(id) on delete cascade,
  sku text not null,
  price decimal(10,2) not null,
  quantity int not null default 1,
  discount decimal(10,2) not null default 0
);

create table downloads(
  id serial primary key,
  key uuid unique not null default gen_random_uuid(),
  order_id int not null references orders(id) on delete cascade,
  order_item_id int not null references order_items(id) on delete cascade,
  times_downloaded int not null default 0
);

create table products(
  id serial primary key not null,
  sku text unique not null,
  name text not null,
  price decimal(10,2) not null,
  created_at timestamptz not null default now()
); 
```

我向您展示这段代码有几个原因:

1.  如果你想合作(我鼓励)，你可以
2.  缺省值和结构使得使用 cte 更加简单
3.  如果你花时间去学习，SQL 是简单明了的

尽管如此，我还是想对这一设计提出几点看法:

*   我使用`on delete cascade`作为外键，以确保我没有孤儿
*   我正在确保空值不会进入我的数据库
*   每当我有一个`not null`约束时，我都试图确保我设置了一个`default`
*   最后，`gen_random_uuid`来自于`pgcrypto`扩展

好了，让我们向产品表添加一些数据:

```
insert into products(sku, name, price)
values
('imposter-single','The Imposter''s Handbook', 30.00),
('mission-interview','Mission:Interview',49.00); 
```

太好了。现在让我们进入正题。

## 问题 1:交易保存订单数据

当一个新订单进来时，我需要创建一个`order`记录，然后创建一个`order_items`记录。这个*必须*在交易中完成，否则会发生不好的事情。这对于 CTE 来说很简单，**，因为 cte 在单个事务** :
中执行

```
with new_order as(
  insert into orders(email, total) 
  values ('rob@bigmachine.io',100.00) 
  returning *
), new_items as (
  insert into order_items(order_id, sku, price)
  select new_order.id, 'imposter-single',30.00
  from new_order
  returning *
)
select * from new_items; 
```

当您用 PostgreSQL 插入一条新记录时，您可以用`returning *`请求它。如果只是想要`id`，可以加上`returning id`。第一个查询插入新的`order`，然后返回它，我可以在第二个查询中使用它。显然:硬编码值不是一个好主意，但是我马上会解决这个问题。

结果:

```
id | order_id | sku | price | quantity | discount 
----+----------+-----------------+-------+----------+----------
  1 | 1 | imposter-single | 30.00 | 1 | 0.00
(1 row) 
```

完美。

## 问题 2:根据我们的新订单创建下载

我从简单开始，确保事情按计划进行，然后继续前进。我的下一步是创建下载，以便用户可以立即下载他们购买的内容。为此，我可以链接一个新的查询:

```
with new_order as(
  insert into orders(email, total) 
  values ('rob@bigmachine.io',100.00) 
  returning *
), new_items as (
  insert into order_items(order_id, sku, price)
  select new_order.id, 'imposter-single',30.00
  from new_order
  returning *
), new_downloads as (
  insert into downloads(order_id, order_item_id)
  select new_order.id, new_items.id 
  from new_order, new_items
  returning *
)

select * from new_downloads; 
```

我从我的 insert 语句中为`order_items`添加了一个`returning *`，然后我可以使用它在第三个查询中生成下载，这次使用一个`select`作为插入。

结果:

```
id | key | order_id | order_item_id | times_downloaded 
----+--------------------------------------+----------+---------------+------------------
  1 | 1fa7c369-94c4-4220-83ba-78e35cfc7377 | 1 | 1 | 0
(1 row) 
```

太好了！到目前为止，所有这一切最好的部分是，由于我的约束和设计，我可以对进入我的数据库的数据感觉良好，并且我可以相信它已经被正确添加，因为**CTE 被包装在单个事务**中。

## 问题 3:插入多个订单项

CTE 的一个小缺点是每个子句只能执行一条语句。如果你用函数式编程的术语来考虑这一点，它有点像*curry*，因为你只有一个参数(前一个查询的结果)和一个返回单个值的函数体。

那么，如何插入多个`order_items`？这就是事情变得有点棘手的地方，尤其是如果您不喜欢 SQL 的话。我喜欢使用它，所以你将要看到的不会吓到我:

```
with new_order as(
  insert into orders(email, total) 
  values ('rob@bigmachine.io',100.00) 
  returning *
), new_items as (
  insert into order_items(order_id, sku, price)
  (
    select new_order.id, sku,price
    from products, new_order
    where sku in('imposter-single','mission-interview')
  )
  returning *
), new_downloads as (
  insert into downloads(order_id, order_item_id)
  select new_order.id, new_items.id 
  from new_order, new_items
  returning *
)

select * from new_downloads; 
```

我通过在 insert 中使用简单的`select`语句来解决这个问题。它将进入`products`表，插入给它的任何 SKU。让我们运行查询，看看会发生什么:

```
id | key | order_id | order_item_id | times_downloaded 
----+--------------------------------------+----------+---------------+------------------
  1 | 6e695533-dd8d-407d-bdca-d71f81c666fb | 1 | 1 | 0
  2 | 31f81049-d08a-4f4c-b30c-565169178268 | 1 | 2 | 0
(2 rows) 
```

有用！算是吧。我们还有最后一个问题…

## 硬编码、数据完整性和验证？

我对电子邮件地址和 SKU 进行了硬编码，这显然不是一件好事。这就是我们刷到你的 ORM 想为你做什么和你作为一个编码者想做什么的地方。换句话说:*你真的会在你的代码中写这个 SQL 吗？*。**我当然不会**

以下是这些问题的一些可能的解决方案。

### SKU 问题

如果传递给这个 SQL 的 SKU 不在我们产品的表中，该怎么办？简短的回答没有说服力:*没什么*。如果在`products`表中找不到 SKU，它将被忽略。这是*次优*，因为我们最终会向我们的系统添加无用的数据！

我们如何解决这个问题？我的第一个倾向是将这个例程封装在一个函数中，在一个名为`cart`的`jsonb`变量中传递电子邮件、SKU 和其他所有东西。在我的函数中，我将确保购物车总数匹配，并且 SKU 存在于数据库中。

这就是我把业务逻辑放入数据库的地方。我能理解为什么人们会这么想，但是我也能理解为什么我还是要这么做。答案很简单:比起 PostgreSQL，我更有可能改变我的平台/ORM。对我来说，这种事情属于尽可能接近你的数据。这是一个简单的数据操作，并不完全是我的业务所独有的，是吗？

另一个解决方案是确保购物车在被推送到这个查询之前得到验证。如果我们可以信任输入，那么我们就可以开始了。

### 代码中的 SQL blob

我认为 SQL 是一个美丽的东西，但那是我的问题。你的是想知道把这些东西放在哪里！您可以做的一件事是将它存储为一个[准备好的语句](https://www.postgresql.org/docs/10/static/sql-prepare.html)，这有很多好处。让我们看看代码，然后我们将深入到好处:

```
prepare new_order(text, decimal(10,2), text[]) as
with new_order as(
  insert into orders(email, total) 
  values ($1,$2) 
  returning *
), new_items as (
  insert into order_items(order_id, sku, price)
  (
    select new_order.id, sku,price
    from products, new_order
    where sku = any($3)
  )
  returning *
), new_downloads as (
  insert into downloads(order_id, order_item_id)
  select new_order.id, new_items.id 
  from new_order, new_items
  returning *
)
select * from new_downloads; 
```

每当您为 PostgreSQL 编写 SQL 语句时，引擎都需要解析 SQL，分析它，然后优化/重写它以便执行。如果您提前告诉 PostgreSQL 您计划运行的查询，那么您可以跳过这些步骤，这样它就可以解析和分析它**一次**。您可以使用`prepare`语句来实现这一点。

缺点是你需要使用位置参数，正如你看到的我在这里用`$1, $2`等，这意味着你失去了一点可读性。如果你能克服这一点，执行这条语句意味着你可以使用`execute`和一些参数:
通过名字调用它

```
execute new_order('rob@bigmachine.io',100.00, '{imposter-single,mission-interview}') 
```

您会注意到奇怪的`{imposter-single}`语法——这就是您在 PostgreSQL 中处理数组的方式。因为我已经转而使用数组，所以我选择使用`any`关键字，它的工作方式类似于`in`，但是专门针对数组值。

## 总结

很长的帖子，但是我鼓励你去探索看看你的数据库能做什么，即使它不是 PostgreSQL。我上面写的 SQL 可能会取代上百行的 ORM 代码和编排，但是确实有一个学习曲线。